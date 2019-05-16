# 目录
* 问题定义
* 问题分析
* 问题分析结果
* 解决方案

# 问题定义

1. 如何构建一个支持与50万个客户端同时保持长连接的系统？
2. 当客户端的数目增加到100万、200万、500万，甚至1000万时如何实现？

# 问题分析

* 什么是长连接？
* 为什么需要长连接？
* 如何启用长连接？
* 长连接有TCP长连接和HTTP长连接，问题定义中的长连接是指哪一种？
* 什么场景下一个系统需要与50万个客户端同时保持长连接？
* 为什么问题不是“如何构建一个支持与50万个客户端同时保持连接的系统”，如果没有“长连接”这个限制，只是普通的连接，那么问题的解决方案会有什么不同？
* 哪些因素限制了系统与客户端保持长连接的数量？

# 问题分析结果

* 什么是长连接？
  * [HTTP长连接](https://zh.wikipedia.org/wiki/HTTP%E6%8C%81%E4%B9%85%E8%BF%9E%E6%8E%A5)
  >**HTTP持久连接**（**HTTP persistent connection**，也称作**HTTP keep-alive**或**HTTP connection reuse**）是使用同一个[TCP](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE "传输控制协议")连接来发送和接收多个HTTP请求/应答，而不是为每一个新的请求/应答打开新的连接的方法。

  * [TCP长连接](https://en.wikipedia.org/wiki/Keepalive#TCP_keepalive)
  客户端与服务端建立TCP连接后，完成此次数据传输后，不断开连接，通过发送ACK包（比如每1s发送一个ACK包，没收到回复前重试5次）保持连接，直到达到指定的超时时间（默认是2h），接收ACK包的一方回复RST包，双方才断开TCP连接。



* 为什么需要使用长连接？
  * 不论是HTTP长连接还是TCP长连接，它们的目的都是——**让TCP连接保持更长的时间”，以便将来能够复用，从而减少TCP连接的建立次数，提高了数据传输的效率，减轻了服务端的压力**。
  * 使用长连接的原因除了为了利用其本身能够带来的对服务性能的提升外，还有一个原因是长连接能够在这样一种场景下派上用场——**客户端在某一局域网内，客户端能够访问服务端，但服务端无法主动访问客户端，而服务端需要持续地或不定期地向客户端传送数据。这种场景下，连接的建立只能由客户端主动发起，并长期保持该连接，服务端才能够随时向客户端传送数据**。


* 如何启用长连接？
  * 启用HTTP长连接
    Web浏览器向后端请求静态资源时，每申请一个资源需要发送一个HTTP请求，每个HTTP请求中都包含一个TCP连接的建立和断开。通过在HTTP请求中添加请求头`Connection: Keep-Alive`启用HTTP长连接后，请求第一个资源的HTTP请求建立的TCP连接会启用TCP keep-alive，在完成第一个资源的数据传输后，不会将该TCP连接断开，后续HTTP请求将复用该TCP连接（对HTTP Keep-Alive的支持依赖于浏览器）。
  * 启用TCP长连接
    * 修改操作系统配置
        * 法一
         ```
         # sysctl -w \
        > net.ipv4.tcp_keepalive_time=600 \
        > net.ipv4.tcp_keepalive_intvl=60 \
        > net.ipv4.tcp_keepalive_probes=20
        net.ipv4.tcp_keepalive_time = 600
        net.ipv4.tcp_keepalive_intvl = 60
        net.ipv4.tcp_keepalive_probes = 20
        ```  
        * 法二
        ```
      # echo 600 > /proc/sys/net/ipv4/tcp_keepalive_time
      # echo 60 > /proc/sys/net/ipv4/tcp_keepalive_intvl
      # echo 20 > /proc/sys/net/ipv4/tcp_keepalive_probes
        ```
    * 编程中指定
    服务端配置socket对象的TCP连接属性，包括：是否启用keepalive、keepalive的时长、发送ACK探测包的时间间隔、允许的没有收到回复的ACK探测包的个数。

* 长连接有TCP长连接和HTTP长连接，问题定义中的长连接是指哪一种？
指的是TCP长连接。因为在题目定义的场景下，客户端与服务器之间的通信不一定使用HTTP协议（或者没必要），使用socket即可满足需求。使用HTTP长连接的场景除了通过浏览器访问web服务外，系统内外服务之间或与第三方外部服务进行频繁的API接口请求也是一种使用场景。
HTTP是应用层的协议，而TCP是传输层的协议，所以HTTP长连接的基础是TCP长连接。

* 什么场景下一个系统需要与50万个客户端同时保持长连接？
  * 在线直播/教学：荔枝FM、画啦啦
  * 社交软件：微信、QQ
  * 物联网：智慧园区解决方案（海量监控摄像头与IoT网关服务器的连接）
  * 分布式集群系统：k8s master与slave节点之间的通信（slave节点上报心跳、节点状态信息，master节点下发控制信息），当master节点与slave节点之间的网络是单向的时候，需要使用长连接（但集群支持的规模远达不到50万，v1.14官方的说法是支持5000节点）。

* 为什么问题不是“如何构建一个支持与50万个客户端同时保持连接的系统”，如果没有“长连接”这个限制，只是普通的连接，那么问题的解决方案会有什么不同？
是否选择使用长连接，取决于解决的问题。之所以要使用长连接，原因是包括：复用TCP连接提高效率、减轻服务端压力、保持由单向发起建立的连接（见前文“为什么需要使用长连接？”）。那么什么时候没有必要使用长连接呢？如果客户端与服务端之间的数据传输频率不高就没有必要使用TCP长连接，减少没有必要存在的连接。

* 哪些因素限制了系统与客户端保持长连接的数量？
  * 单台服务器的限制
    * 系统支持的端口
      这个限制是不存在的。服务端监听一个端口后，所有服务端都可以通过这个端口与服务端建立连接，在问题定义的场景中，客户端是与服务端的同一个端口建立TCP连接的。
    * 文件系统支持的文件数目
      因为每建立一个TCP连接，操作系统都会为之创建一个文件文件系统是建立在磁盘包括（可能是物理的，也可能是逻辑的）上的多个块组成的，
  * 多台服务器组成的集群的限制
    * 服务器数目
    * 负载均衡器
  * 具体业务类型
    * 建立在连接之上的业务本身对服务器计算、I/O的需求

# 解决方案

# 扩展学习
* MQTT

# 参考链接
* [TCP Keepalive HOWTO](https://www.tldp.org/HOWTO/html_single/TCP-Keepalive-HOWTO/)
* [Configuring TCP keepalive after accept](https://stackoverflow.com/questions/31426420/configuring-tcp-keepalive-after-accept)
* [Breaking Kubernetes: How We Broke and Fixed our K8s Cluster](https://medium.com/civis-analytics/https-medium-com-civis-analytics-breaking-kubernetes-how-we-broke-and-fixed-our-k8s-cluster-adfa6fbade61)
* [How many socket connections can a web server handle?](https://stackoverflow.com/questions/1575453/how-many-socket-connections-can-a-web-server-handle)
* [Is a TCP server limited to 65535 clients?](https://networkengineering.stackexchange.com/questions/48283/is-a-tcp-server-limited-to-65535-clients)
* [The Secret To 10 Million Concurrent Connections -The Kernel Is The Problem, Not The Solution](http://highscalability.com/blog/2013/5/13/the-secret-to-10-million-concurrent-connections-the-kernel-i.html)
# 目录
* 问题定义
* 问题分析
* 问题分析结果
* 解决方案

# 问题定义

1. 如何构建一个支持与50万个客户端同时保持长连接的系统？
2. 当客户端的数目增加到100万、200万、500万，甚至1000万时如何实现？

# 问题分析

* 什么是长连接？
* 为什么需要长连接？
* 如何启用长连接？
* 长连接有TCP长连接和HTTP长连接，问题定义中的长连接是指哪一种？
* 什么场景下一个系统需要与50万个客户端同时保持长连接？
* 为什么问题不是“如何构建一个支持与50万个客户端同时保持连接的系统”，如果没有“长连接”这个限制，只是普通的连接，那么问题的解决方案会有什么不同？
* 哪些因素限制了系统与客户端保持长连接的数量？

# 问题分析结果

* 什么是长连接？
  * [HTTP长连接](https://zh.wikipedia.org/wiki/HTTP%E6%8C%81%E4%B9%85%E8%BF%9E%E6%8E%A5)
  >**HTTP持久连接**（**HTTP persistent connection**，也称作**HTTP keep-alive**或**HTTP connection reuse**）是使用同一个[TCP](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE "传输控制协议")连接来发送和接收多个HTTP请求/应答，而不是为每一个新的请求/应答打开新的连接的方法。

  * [TCP长连接](https://en.wikipedia.org/wiki/Keepalive#TCP_keepalive)
  客户端与服务端建立TCP连接后，完成此次数据传输后，不断开连接，通过发送ACK包（比如每1s发送一个ACK包，没收到回复前重试5次）保持连接，直到达到指定的超时时间（默认是2h），接收ACK包的一方回复RST包，双方才断开TCP连接。



* 为什么需要使用长连接？
  * 不论是HTTP长连接还是TCP长连接，它们的目的都是——**让TCP连接保持更长的时间”，以便将来能够复用，从而减少TCP连接的建立次数，提高了数据传输的效率，减轻了服务端的压力**。
  * 使用长连接的原因除了为了利用其本身能够带来的对服务性能的提升外，还有一个原因是长连接能够在这样一种场景下派上用场——**客户端在某一局域网内，客户端能够访问服务端，但服务端无法主动访问客户端，而服务端需要持续地或不定期地向客户端传送数据。这种场景下，连接的建立只能由客户端主动发起，并长期保持该连接，服务端才能够随时向客户端传送数据**。


* 如何启用长连接？
  * 启用HTTP长连接
    Web浏览器向后端请求静态资源时，每申请一个资源需要发送一个HTTP请求，每个HTTP请求中都包含一个TCP连接的建立和断开。通过在HTTP请求中添加请求头`Connection: Keep-Alive`启用HTTP长连接后，请求第一个资源的HTTP请求建立的TCP连接会启用TCP keep-alive，在完成第一个资源的数据传输后，不会将该TCP连接断开，后续HTTP请求将复用该TCP连接（对HTTP Keep-Alive的支持依赖于浏览器）。
  * 启用TCP长连接
    * 修改操作系统配置
        * 法一
         ```
         # sysctl -w \
        > net.ipv4.tcp_keepalive_time=600 \
        > net.ipv4.tcp_keepalive_intvl=60 \
        > net.ipv4.tcp_keepalive_probes=20
        net.ipv4.tcp_keepalive_time = 600
        net.ipv4.tcp_keepalive_intvl = 60
        net.ipv4.tcp_keepalive_probes = 20
        ```  
        * 法二
        ```
      # echo 600 > /proc/sys/net/ipv4/tcp_keepalive_time
      # echo 60 > /proc/sys/net/ipv4/tcp_keepalive_intvl
      # echo 20 > /proc/sys/net/ipv4/tcp_keepalive_probes
        ```
    * 编程中指定
    服务端配置socket对象的TCP连接属性，包括：是否启用keepalive、keepalive的时长、发送ACK探测包的时间间隔、允许的没有收到回复的ACK探测包的个数。

* 长连接有TCP长连接和HTTP长连接，问题定义中的长连接是指哪一种？
指的是TCP长连接。因为在题目定义的场景下，客户端与服务器之间的通信不一定使用HTTP协议（或者没必要），使用socket即可满足需求。使用HTTP长连接的场景除了通过浏览器访问web服务外，系统内外服务之间或与第三方外部服务进行频繁的API接口请求也是一种使用场景。
HTTP是应用层的协议，而TCP是传输层的协议，所以HTTP长连接的基础是TCP长连接。

* 什么场景下一个系统需要与50万个客户端同时保持长连接？
  * 在线直播/教学：荔枝FM、画啦啦
  * 社交软件：微信、QQ
  * 物联网：智慧园区解决方案（海量监控摄像头与IoT网关服务器的连接）
  * 分布式集群系统：k8s master与slave节点之间的通信（slave节点上报心跳、节点状态信息，master节点下发控制信息），当master节点与slave节点之间的网络是单向的时候，需要使用长连接（但集群支持的规模远达不到50万，v1.14官方的说法是支持5000节点）。

* 为什么问题不是“如何构建一个支持与50万个客户端同时保持连接的系统”，如果没有“长连接”这个限制，只是普通的连接，那么问题的解决方案会有什么不同？
是否选择使用长连接，取决于解决的问题。之所以要使用长连接，原因是包括：复用TCP连接提高效率、减轻服务端压力、保持由单向发起建立的连接（见前文“为什么需要使用长连接？”）。那么什么时候没有必要使用长连接呢？如果客户端与服务端之间的数据传输频率不高就没有必要使用TCP长连接，减少没有必要存在的连接。

* 哪些因素限制了系统与客户端保持长连接的数量？
  * 单台服务器的限制
    * 系统支持的端口
      这个限制是不存在的。服务端监听一个端口后，所有服务端都可以通过这个端口与服务端建立连接，在问题定义的场景中，客户端是与服务端的同一个端口建立TCP连接的。
    * 文件系统支持的文件数目
      因为每建立一个TCP连接，操作系统都会为之创建一个文件文件系统是建立在磁盘包括（可能是物理的，也可能是逻辑的）上的多个块组成的，
  * 多台服务器组成的集群的限制
    * 服务器数目
    * 负载均衡器
  * 具体业务类型
    * 建立在连接之上的业务本身对服务器计算、I/O的需求

# 解决方案

# 扩展学习
* MQTT

# 参考链接
* [TCP Keepalive HOWTO](https://www.tldp.org/HOWTO/html_single/TCP-Keepalive-HOWTO/)
* [Configuring TCP keepalive after accept](https://stackoverflow.com/questions/31426420/configuring-tcp-keepalive-after-accept)
* [Breaking Kubernetes: How We Broke and Fixed our K8s Cluster](https://medium.com/civis-analytics/https-medium-com-civis-analytics-breaking-kubernetes-how-we-broke-and-fixed-our-k8s-cluster-adfa6fbade61)
* [How many socket connections can a web server handle?](https://stackoverflow.com/questions/1575453/how-many-socket-connections-can-a-web-server-handle)
* [Is a TCP server limited to 65535 clients?](https://networkengineering.stackexchange.com/questions/48283/is-a-tcp-server-limited-to-65535-clients)
* [The Secret To 10 Million Concurrent Connections -The Kernel Is The Problem, Not The Solution](http://highscalability.com/blog/2013/5/13/the-secret-to-10-million-concurrent-connections-the-kernel-i.html)
