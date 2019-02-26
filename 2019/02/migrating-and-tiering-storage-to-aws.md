# 数据迁移与分层存储

## What

### 分层存储

#### 按照业务价值、数据重要程度、数据访问频率、数据类型等因素决定不同数据的存储媒介，以降低成本、并满足业务需求

#### Tier0

##### Tier 0 storage is the fastest and most expensive layer in the hierarchy and is suited for applications with little tolerance for downtime or latency. Data placed in a "zero tier" often includes scale-up transactional databases for analytics, financials, healthcare and security.

#### Tier1

##### Tier 1 data includes mission-critical applications, recently accessed data or top-secret files. This data might be stored on expensive, high-quality media, such as double-parity RAID.

#### Tier2

##### Data on Tier 2 storage usually contains historical financial information, cold data and classified files. This data is preserved on lower-cost media in a conventional storage area network (SAN).

##### Tier 2 storage protects application data that requires high reliability and security but doesn't need submillisecond latency.

### 数据迁移

## Why

### 为什么要做数据迁移/分层存储？

#### 技术更新

#### 变更供应商

#### 数据中心迁移/退役

#### 存储分层？

## When

### 数据中心迁移上云

### 数据中心合并

### 数据灾难恢复，数据分发

### 远端数据采集

## How

### 详细的数据迁移计划是成功的关键

#### 初始化计划--> 应用投资组合发现 --> 应用分析 --> 设计 --> 迁移 --> 集成 --> 验证 --> 操作 --> 优

### 考虑要素

#### 数据要迁移到哪里去？

#### 启动迁移前后服务是否中断，以及中断时长？

#### 有多少数据量要迁移？

#### 待迁移的数据有什么类型/格式的？

#### 迁移的表现形象如何？

#### 迁移依赖的网络路径，带宽限制是？

#### 怎么罗列、盘点待迁移的数据？

### AWS提供的数据迁移工具

#### 传输数据到S3

##### 离线传输

###### Snow Family

####### snowball（PB）

######## 80TB

######## 依赖snowball client工作

####### snowball edge

######## 100TB

######## 带计算存储能力

######## 支持S3接口/NFS

######## 自带加密功能

####### snowmobile（EB）(>10PB)

##### 线上传输

###### S3传输加速，AWS edge location

####### 修改TCP windows size

####### 使用方式，编辑bucket属性，enable传输加速

#### 存储网关

##### 定义：将云存储资源前置到客户本地

###### 用户本地写入数据，数据会自动同步到AWS数据中心

##### 作用

###### 使用标准的存储协议访问不同的AWS云存储服务

##### 类型

###### 文件网关

####### 一台虚拟机，使用NFS协议提供文件服务

######## 后端是多个S3存储桶

####### 写入路径

######## 本地（app -NFSv3/v4.1-> 存储网关服务器 ）--> 云端（存储网关 --> S3 --> Glaicer）

####### 定义：本地的文件以对象存储的形式储存在S3中

####### 文件 —— 对象 一一对应

####### 文件的元数据以key-val的形式储存在对象元数据

######## 权限

######## 用户

######## 用户组

######## 最近修改时间

######## 创建时间

###### 卷网关

####### 定义：本地使用的卷其实是云上的S3服务、EBS服务

####### PB级容量

####### 写入路径

######## 本地（app -iSCSI-> 存储网关服务器 ）--> 云端（存储网关 --> S3 --> EBS快照）

####### 存储选项

######## 缓存卷

######### 保留经常访问的数据子集的副本

######### 节省大量主存储成本，而且最大程度地减小了本地扩展存储的需求。您还可以保留对经常访问的数据的低延迟访问。

######## 存储卷

######### 所有数据都要求低时延访问，数据全集保留在本地

######### 或进行数据传输场景，完成传输后，使用缓存卷

####### 利用卷存储网关进行数据迁移

######## 把卷挂载到存储网关服务器上，数据同步完成后即可创建EBS快照，快照可以创建EBS磁盘，挂载到云上的虚拟机

###### 磁带网关

####### 定义

######## 本地使用的虚拟磁带存储其实是云上的S3（虚拟磁带）、Glacier（归档磁带）

####### VTL 虚拟磁带库

####### 写入路径

######## 本地（app -iSCSI-> -磁带驱动或转换器-> 磁带网关服务器 ）--> 云端（磁带网关 --> S3 --> Glacier）

#### EFS+Direct Connect

### 业务伙伴，更多解决方案

#### 主存储

#### 备份与恢复

#### 归档（利用Glacier）

#### BCDR（业务连续性与灾难恢复）
