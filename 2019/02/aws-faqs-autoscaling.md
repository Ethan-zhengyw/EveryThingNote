# AWS常见问题-AWSAutoScaling

## AWS Auto Scaling

### 一般性问题

#### （What）什么是AWS Auto Scaling？

##### 自动根据需要伸缩AWS服务资源的规格，以满足变化中的业务量需求，提高IT资源的利用率，兼顾性能与成本

#### （Why）使用AWS Auto Scaling有什么好处？

##### 基本作用

###### 保障服务性能

####### 监控相关指标，一旦达到阈值，自动伸缩资源适应变化的需求

###### 降低成本

####### 确保只使用满足需要的资源

##### 竞争力

###### 易配置

####### 不同服务，一致体验

###### 灵活性

####### 提供了不同的伸缩策略，按照用户实际需要配置，实现资源按需自动伸缩

#### （When）什么时候应该使用AWS Auto Scaling？

##### 例如：e-commerce web application that receives variable traffic through the day

#### （How）怎么使用？

##### 1. 选择应用（包含一个或多个资源）

###### 根据标签选择

###### 根据CloudFormation Stacks选择

##### 2. 创建一个伸缩计划，Scaling Plan

###### 伸缩计划里定义了应用里的每一个资源应该怎么伸缩

###### 目标跟踪伸缩策略

####### AWS Auto Scaling会为每一个资源创建一个目标跟踪伸缩策略，策略里包含资源所属服务中比较常用的指标，用户为每个指标设置策略（成本优先、性能优先或者均衡两者）

###### 预测性伸缩

### 伸缩选项

#### 伸缩AWS资源时有什么不同的方式可供选择？

##### EC2

###### 数量增减

###### 替换不健康的实例

#### 什么时候使用AWS Auto Scaling，什么时候使用Amazon EC2 Auto Scaling？

#### 什么时候使用AWS Auto Scaling，什么时候使用不同服务提供独立的Auto Scaling能力？

#### 什么是预测性伸缩，predictive scaling？

##### 基于历史流量数据，通过机器学习算法进行数据挖掘，预测应用在每日或每周的流量变化模式，按照预测结果进行资源伸缩，可以做到在流量发生变化之前提前完成伸缩操作，响应更及时

#### 哪些服务支持预测性伸缩？

##### EC2 only

#### 如何结合目标追踪使用预测性伸缩？

#### 什么是伸缩计划？

#### 如何使用伸缩计划，并且不使用预测性伸缩？

#### 为了生成伸缩计划，需要使用多少历史数据来进行预测性伸缩？

##### 最多2周，最少1天

#### 是否能够通过配置预测性伸缩，在流量峰值达到之前增大资源实例规格？

#### AWS Auto Scaling与其他服务的Auto Scaling在能力上有什么差别？

##### 支持的资源、操作方式、预测性伸缩、伸缩资源组...

### 特性

#### AWS Auto Scaling支持哪些服务？

##### EC2、ECS、DynamicDB

##### EC2 Spot、Aurora

#### AWS Auto Scaling如何做出伸缩建议的？

##### 使用最受欢迎的指标阈值，便于用户快速使用，后续再根据实际需要进行调整

#### 怎么选在一个在AWS Auto Scaling里的应用栈？

##### 按标签选择

##### 按CloudFormation Stacks选择（ECS只支持按标签选择）

#### AWS Auto Scaling怎么发现有哪些资源能够伸缩？

##### 同上

### 可用性与价格

#### 哪些Region支持AWS Auto Scaling？

##### US East (Northern Virginia), US East (Ohio), US West (Oregon), EU (Ireland), and Asia Pacific (Singapore)

#### AWS Auto Scaling怎么收费？

##### AWS Auto Scaling是免费的，只收取监控、以及所伸缩的资源服务本身的费用
