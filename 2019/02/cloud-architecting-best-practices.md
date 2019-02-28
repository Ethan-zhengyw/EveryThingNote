# AWS云架构最佳实践

## 传统IT环境 vs 云计算环境

### IT资产供给

#### 灵活按需、高利用率、全球性

### 高层抽象服务化

### 内建安全

### 成本控制

### 操作方式

## 设计原则

### 可扩展性

#### 水平扩展与横向扩展

#### 无状态应用与有状态应用

##### 会话数据

###### 客户端浏览器Cookie

####### 网络带宽性能损耗

####### 数据不可信

###### 服务端SessionID + Session

####### 分支主题

##### 中心化存储会话数据，避免引入有状态应用

###### DB

###### S3、EFS

##### 有状态应用LB

###### 按sessionID进行负载均衡，与计算资源配置亲和性规则

####### 已存在的session无法被重新分配（可改进）

####### 指定了计算资源，可靠性下降

###### 客户端负载均衡

####### 请求获取服务endpoint

#### 请求任务分发模型 Push vs Pull

##### Push —— LB

##### Pull

###### DB

###### MQ

###### DB vs MQ

####### 通常选择数据库作为任务队列的一个原因是——环境里已经有数据库了，不需要再引入新的MQ组件，涉及到增加维护工作的问题

####### 使用DB进行开发项目进展会更快？

####### 但是，使用数据库作为消息队列不是一个可扩展的解决方案？

######## 可扩展性当前是否真的那么重要？

######## MQ是否可以在项目后续有真正的可扩展性需求时再通过架构演进引入

### 一次性的资源替代固定的服务器

#### 降低成本

#### 避免“配置漂移”

#### 基础设施安全性得到提高

### 自动化

### 松耦合

#### 好的接口设计

#### 服务发现

#### 异步集成与处理

#### 分布式系统最佳实践

##### 优雅失败

###### 重试

###### 转移请求/重定向

### 服务，而不是服务器

#### 充分利用云服务提供的能力提高系统的可靠性、安全性、可运维性

### 数据库

#### 传统企业组织基础设施使用数据库时存在的限制

##### LICENSE

##### 维护

#### 如何选择工作负载使用的数据库

##### IOPS & Throughout

###### Is this a read-heavy, write-heavy, or balanced workload? 

###### How many reads and writes per second are you going to need? 

###### How will those values change if the number of users increases?

##### 数据量与存储时长及增长速度

###### How much data will you need to store and for how long? 

###### How quickly will this grow? Is there an upper limit in the near future? 

###### What is the size of each object (average, min, max)? How will these objects be accessed?

##### 可靠性要求

##### 数据来源是否可信

##### 对时延的要求

##### 需要支持多少用户并发访问

##### 数据模型与查询方式，是否支持平滑扩展

##### 功能性需求

###### 严格关系型？注重灵活性？团队技术栈？

##### 预算

###### LICENSE模式是否支撑数据增长？

#### 关系型数据库

##### 特点

###### 定义行、列的表格结构

###### 灵活、强大的索引能力、查询语言

###### 合并不同的表格操作高效

###### 事务能力

##### 可扩展性

###### 垂直扩展

####### 扩大实例规格

####### 增强存储资源规格

####### 使用Amazon Aurora获取比标准MySQL更大的吞吐量

###### 水平扩展

####### 分库分表

######## 业务逻辑代码需要感知怎么“分”的

##### 高可用

###### 多AZ

###### 优雅失败

##### 反模式

###### nosql

####### no need for joins or complex transactions

####### expect a write throughput beyond the constraints of a single instance

###### 关系型数据库

####### 关系型数据库的限制

######## 单实例工作

####### 连接操作、事务性要求高

###### S3

####### 存储二进制文件

#### NoSQL

##### 特点

###### 牺牲查询性能与事务能力

###### 有更灵活的数据结构和极好的水平扩展性

###### 支持多种数据模型

####### 图

####### 键值对

####### json文档

###### 公认特点

####### 易部署

####### 高效伸缩

####### 高可用

####### 容错

###### AWS云服务：Amazon DynamoDB

##### 可扩展性

###### 自动地做数据分区、拷贝，支持可水平扩展，业务数据访问层逻辑不需要考虑

##### 可靠性

###### 全局表，数据备份在所选择的所有局点中

##### 反模式

###### nosql

####### no need for joins or complex transactions

####### expect a write throughput beyond the constraints of a single instance

###### 关系型数据库

####### 关系型数据库的限制

######## 单实例工作

####### 连接操作、事务性要求高

###### S3

####### 存储二进制文件

#### 数据仓库，Data Warehouse

##### 可扩展性

###### 大规模并行处理MPP

###### 列存储

###### 目标数据压缩

##### 可靠性

###### 多副本

###### 转储到S3

##### 反模式

###### RedShift 不适用于高并发场景

##### AWS云服务

###### Amazon RedShift（特殊的关系型数据库，针对数据分析）

#### 搜索

##### 搜索!=查询

##### 可扩展性

###### 分区、副本拷贝

##### 可靠性

###### 数据多AZ冗余存储

##### AWS云服务

###### Amazon CloudSearch

####### 配置少

####### 可自动伸缩

###### Amazon ElasticSearch

####### 开放API，参数控制更全面

####### 不止“搜索”，还有分析

#### 图数据库

##### 可扩展性

##### 使用场景（be able to create relationships between data and quickly query these relationships）

###### social networking

###### recommendation engines

###### fraud detection

##### AWS云服务

###### Amazon Neptune		

##### 可靠性

###### 多副本

###### 转储到S3

### 处理增长的数据

#### 数据湖架构

##### store massive amounts of data in a central location so that it's readily available to be categorized, processed, analyzed, and consumed by diverse groups within your organization.

### 消除单点故障

#### 法一、引入冗余（有状态应用）

##### 主备模式

###### 常用于有状态应用，如：关系型数据库

##### 主主模式

###### 影响更小

#### 法二、检测故障（无状态应用，流量分发）

##### 尽可能多地自动检测故障并自动恢复

###### ELB health check

###### Amazon Route 53

###### AutoScaling

##### 采集足够的日志以备分析

##### 设计好的健康检查机制

###### TCP连通性不足以说明OK，需要发送简单的http请求，看看是否能返回200

###### 避免过于深度健康检查

####### 请求依赖数据库（数据库短暂不可用时，节点会被标记为不可用）

#### 法三、耐用的数据存储

##### 多副本（冗余）

###### 同步副本

####### 数据一致性

####### 写事务

####### 缺点

######## 性能开销大、网络条件强依赖-->性能、可靠性降低-->不建议维护太多的副本同步

###### 异步副本

####### 提升数据可用性、可伸缩能力，同时保证性能、适用于能够忍受在故障时丢失部分事务的场景

###### 基于仲裁的多副本

####### 至少多少个节点写成功即认为事务完成，综合同步/异步副本机制的优点

##### 备份

###### RTO/RPO

##### 思考

###### 能够接受多少数据丢失？

###### 故障后多长时间内需要恢复业务？

#### 法四、自动化多数据中心还原能力

##### 使用云平台的多AZ进行灾备

##### 举例

###### Amazon RDS数据库实例冗余部署在多个AZ

###### Amazon S3/DynamoDB数据冗余存储在多个基础设施（数据中心/AZ）

#### 法五、故障隔离与传统水平扩展

##### 场景：某个请求触发实例故障，若继续重试该请求，可能进而导致整个系统奔溃

##### 类似数据库sharding，把节点分组，把用户的请求分配到指定节点，实现一定的故障隔离能力

### 成本优化

#### 弹性资源

#### 合适的规格与计价模式

##### 资源预留型实例

##### 竞价型实例

### 缓存

#### 应用缓存

##### ElasticCache

###### Memcached

####### 纯缓存解决方案

####### 支持多线程访问

####### 应用：内存缓存池

###### Redis

####### 支持多副本/多AZ冗余部署/数据持久化

####### 支持复杂数据类型

####### 支持pub/sub

#### 边缘缓存

##### Amazon Cloud Front

### 安全性

#### 共享安全责任模型

#### 身份认证与访问控制，单一权限原则/保护凭证

#### 审计检测

#### 纵深防御

#### 数据保护

#### ...

## 参考链接

### https://d1.awsstatic-china.com/whitepapers/AWS_Cloud_Best_Practices.pdf

### https://www.cloudamqp.com/blog/2015-11-23-why-is-a-database-not-the-right-tool-for-a-queue-based-system.html

### https://dev.to/matteojoliveau/microservices-communications-why-you-should-switch-to-message-queues--48ia
