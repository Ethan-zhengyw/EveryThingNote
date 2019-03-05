# microservices-on-aws

## Before Reading

### 基础：微服务架构

#### Why

##### 微服务之间相互解耦，有助于提升系统可靠性、可扩展性和可运维性，HA & failure tolerance

###### SRE作为支撑

##### 微服务独立交付，提高变更发布效率，支持更快交付业务价值

###### DevOps/CICD作为支撑

#### What

##### 云计算系统中的领先的架构模式，通过开发复杂度来换取运维复杂度，并需要自动化测试、持续交付和DevOps文化提供坚实的支撑

###### （并没有简化系统架构）

###### （并不总是降低了运维复杂度，微服务个数如果过多，运维复杂度也会相应上升）

##### 尚无精确的定义，但其在下述方面还是存在一定的共性，即围绕业务功能的组织、自动化部署、端点智能、和在编程语言和数据方面进行去中心化的控制。

###### 业务功能的组织

###### 自动化部署

###### 端点智能

###### 在（技术）编程语言和数据方面进行去中心化的控制

###### more：支持演进的架构

##### 特点

######    特性一：“组件化”与“多服务”

######    特性二：围绕“业务功能”组织团队

######    特性三：“做产品”而不是“做项目”

####### "you build, you run it"

######    特性四：“智能端点”与“傻瓜管道”

######    特性五：“去中心化”地治理技术

######    特性六：“去中心化”地管理数据

######    特性七：“基础设施”自动化

######    特性八：“容错”设计

######    特性九：“演进式”设计

#### How

##### 微服务框架

###### 服务治理

####### 服务拆分

####### 服务部署

######## 服务配置/配置中心

###### 服务调用

####### API网关

####### 负载均衡

####### 断路器/限流

####### 服务发现/注册中心

####### 消息总线

###### 服务运维

####### 分布式系统SRE

######## 调用链

#### Extra(By Thoughtworks tech radar)

##### 微服务架构的一个显著特征是系统组件和服务是围绕业务 能力进行组织的。无论系统规模大小，微服务都需要将系统 功能和信息进行有意义的分组和封装，以便拆分后的微服 务能彼此独立地交付业务价值。而以前的服务架构方式会 根据技术特性组织服务。我们观察过很多采用分层式微服 务架构(LAYERED MICROSERVICES ARCHITECTURE) 的组织，他们在某些方面存在着明显的矛盾。这些组织都 陷入了以技术角色为主来划分服务的误区，比如，用户体验 API、进程 API 或系统 API等。我们会很自然地将技术团队按 层划分，这会导致想要交付任何有价值的业务变更，都需要缓慢而昂贵的多团队合作。请务必小心这种分层方式带来 的影响，我们更推荐根据业务能力来划分服务和团队。

##### 微服务已成为现代云计算系统中的领先的架构模式，但我 们依旧认为团队在使用该架构时应谨慎。MICROSERVICE ENVY特指那些盲目追赶微服务潮流的现象，很多团队在实践微服务时并没有简化其系统架构，大多数的实践方案只 是将一些简单的服务聚合在一起而已。目前，Kubernetes等 平台简化了复杂的微服务系统的部署问题，其他服务提供 商们也正在推进他们的微服务治理方案，这些强大的工具 都可能裹挟团队走上微服务之路。但请千万谨记，微服务是 通过开发复杂度来换取运维复杂度，并需要自动化测试、持 续交付和DevOps文化提供坚实的支撑。

## 白皮书内容

### 白皮书目的

#### 总结微服务的特性

#### 讨论实施微服务架构的困难之处

#### 生产团队能够如何利用AWS实施微服务架构

### 微服务简介

#### What

##### 软件开发的架构和组织方式

##### 或者说是各种在实践中被验证为有效的经验方法的结合

###### agile software development

###### service-oriented architectures

###### API-first design

###### Continuous Delivery (CD)

###### design patterns of the Twelve-Factor App

#### Why

##### 缩短软件交付周期

##### 促进创新、增进主人公意识

##### 提高可维护性、可扩展性

#### How

##### 围绕业务能力组织：系统组件和服务，团队

###### 系统按业务能力拆分

###### 团队按业务能力组织

### 微服务的6个特点

#### 去中心化

##### 去中心化管理数据

##### 去中心化地开发/部署/管理/运维

#### 独立

##### 微服务团队能够灵活独立控制针对微服务的变更，而不依赖、影响其他微服务

#### 把一件事做好

##### 保持简单，如果单个微服务过于复杂，可能意味着需要拆分为2个或多个微服务

#### 技术开放

##### 各个微服务团队按需选择技术，包括：编程语言/数据库/工具

#### 黑盒

##### 隐藏内部细节，通过预先定义好的API进行通信

#### You build it, you run it

##### 团队负责开发到上线生产环境、以及后续的运维全流程（DevOps）

### 采用微服务架构的好处

#### 敏捷

##### 交付多个微服务 vs 交付一个“巨石”软件

#### 创新

##### 技术开放 + 独立 + CICD(快速验证新方案/回滚) --> 有助于创新（灵活性）

#### 质量

##### 系统拆分为微服务，代码自然跟着拆解为不同的“模块”，在一定程度上提高了可复用性、可维护性

#### 可扩展性

##### 小粒度送耦合的微服务是建立大规模分布式系统的最佳实践，支持水平伸缩

#### 可用性

##### 更容易进行故障隔离，减小故障影响范围

### 实施微服务架构的困难之处

#### 问题

##### 分布式系统

###### 一堆的问题“Fallacies of Distributed Computing”

####### Fallacies

######## The network is reliable.

######## Latency is zero.

######## Bandwidth is infinite.

######## The network is secure.

######## Topology doesn't change.

######## There is one administrator.

######## Transport cost is zero.

######## The network is homogeneous.

##### 迁移

###### 从单一“巨石”软件系统演进到微服务架构，需要投入精力划分微服务，明确边界，拆分代码、数据库依赖

##### 版本

###### 不同版本的微服务支持的API有差异

##### 组织

###### 如何组织高效的微服务团队，应用敏捷开发方法，实践DevOps

#### 架构复杂度

##### “巨石”软件系统架构复杂度取决于代码量

##### 微服务架构软件系统取决于微服务之间相互交互的复杂度

###### 异步通信

###### 级连失败

###### 数据一致性问题

###### 服务发现与认证

#### 运维复杂度

##### 不再运维单一的服务，而是几十、上百个微服务，需要考虑的问题

###### • How to provision resources in a scalable and cost-efficient way?

####### 获得可扩展性，考虑成本

###### • How to operate dozens or hundreds of microservice components effectively without multiplying efforts?

####### 自动化

###### • How to avoid reinventing the wheel across different teams and duplicating tools and processes?

###### • How to keep track of hundreds of pipelines of code deployments and their interdependencies?

###### • How to monitor overall system health and identify potential hotspots early on?

####### SRE/监控告警

###### • How to track and debug interactions across the whole system?

####### SRE/日志/调用链

###### • How to analyze high amounts of log data in a distributed application that quickly grows and scales beyond anticipated demand?

####### ELK

###### • How to deal with a lack of standards and heterogeneous environments that include different technologies and people with differing skill sets?

###### • How to value diversity without locking into a multiplicity of different technologies that need to be maintained and upgraded over time?

###### • How to deal with versioning?

###### • How to ensure that services are still in use especially if the usage pattern isn’t consistent?

###### • How to ensure the proper level of decoupling and communication between services?

### 微服务与云

#### 使用云计算能够在以下方面助力微服务架构的实施

##### 资源按需使用、按使用付费

##### 低成本、低风险进行实验

##### 可编程

###### 云上服务提供了API接口/命令行工具/SDK，支持用户编程实现自定义功能

##### 基础设施即代码

###### 用代码描述基础设施，并使用版本控制系统进行存放

##### 持续交付

##### 托管的服务

###### 使用托管服务可降低运维复杂度，终极方案——serverless

##### 服务导向

##### 技术开放/多元化

#### 基于AWS的微服务架构

##### 降低运维复杂度

###### 使用托管服务可降低运维复杂度

####### 容器

####### 终极方案——serverless

####### 数据库

######## RDS

######## DynamoDB

######## Cache

##### 分布式系统组件

###### 服务发现

####### 客户端服务发现（硬编码）

####### 基于ALB的服务发现

####### 基于DNS的服务发现

####### 基于监控，自动化变更后端配置，CloudWatch + Lambda + Route53

####### 基于配置管理工具，如：Chef, Puppet, or Ansible

######## AWS OpsWork

####### 基于key-value存储的服务发现

###### 分布式数据管理

#######  CAP Theorem

######## 分布式系统权衡一致性与可用性、性能

####### 把系统的日志流存储起来，出现问题时可以根据日志进行恢复，类似（ECTD wal，write ahead log）

######## Kinesis

###### 异步通信与轻量化的消息

####### Restful + SNS + SQS（消息总线）

####### 编排与状态管理

######## Step functions

###### 分布式系统监控

####### 中心化管理日志

######## CloudWatch Logs

####### 分布式追踪（调用链）

######## X-Ray

######### X-Amzn-Trace-Id/X-Ray-integrated service 

####### 日志分析

######## ElasticSearch + Kibana

######## Kinesis + RedShift + QuickSight

###### 通信过于繁琐？

####### 考虑协议与缓存

###### 审计

####### AWS CloudTrail VS AWS Config

## 参考链接

### 1. [Microservices on AWS](https://d1.awsstatic-china.com/whitepapers/microservices-on-aws.pdf)

### 2. [Martin Fowler介绍微服务的博文](https://martinfowler.com/articles/microservices.html)

### 3. [Thoughtworks技术雷达中关于微服务架构的描述](https://assets.thoughtworks.com/assets/technology-radar-vol-19-cn.pdf)
