# Huskie大数据平台开发手册

# 产品矩阵

![输入图片说明](https://images.gitee.com/uploads/images/2020/1103/205450_f36ea591_7955977.jpeg "全局项目架构.jpg")
# 1.项目简介

## 1.1AP数据采集平台

通过编写和配置数据提取脚本,实现将数据库数据和日志埋点数据统一拉取到Hadoop集群上

![输入图片说明](https://images.gitee.com/uploads/images/2020/1103/205543_5243ec32_7955977.png "AP数据采集平台架构图.png")



## 1.2DW离线数据仓库

是为集团所有级别的决策制定过程，提供所有类型数据支持的战略集合。



## 1.3RTDW实时数据仓库

市面上对实时数仓没有范式的定义,像一线大厂,尤其是阿里,付费购买的实时服务,全部SQL化开发,功能异常强大,当然我们这边是做不到完全SQL化的,还是需要使用代码来实现部分的功能

## 1.4DMP用户画像系统

为集团实现精准营销提供技术支持

## 1.5MSDQAP多源数据查询平台

由于集团早期没有统一规划数据源，导致全局混乱,数据杂乱存储在:多台台机器、第三方、公司内部多个系统,并且还存储在多种不同引擎如：redis、sql Server、hbase、hdfs、elasticsearch、kafka上，所以不好规划统一 ，导致查询关联及其麻烦,要各种预先抽取多个数据源到同一个地方，然后在做统一处理，最后出报表 ，而且查询及其缓慢.为了解决此问题,我针对性的设计了MSDQAP多源数据查询平台.

[逻辑架构]

![1605501033902](C:\Users\monster\AppData\Local\Temp\1605501033902.png)

[项目架构]

![1605500964377](C:\Users\monster\AppData\Local\Temp\1605500964377.png)



## 1.6Naga数据分析平台

给集团数据挖掘、数据分析师提供一个:查询引擎可自定选择(MR、Spark)、集群高可用(随便折腾)、查询数据有权限控制,资源使用有控制、查询SQL语句模板化、傻瓜式操作、拖拽式操作这种查询OA系统,实现简单操作快速检索查询,实时生成数据分析结果报表,实现查询即产出

## 1.7用户兴趣取向性分析



## 1.8.ELK日志检索平台



## 1.8延伸项目

### 1.8.1Hadoop源码二次开发

1)Hadoop 集群在高并发情况下不工作现象

2)在大数据集情况下 NameNode 会因为 fullGC 直接退出

3)Hadoop 锁的性能不高

4)NameNode 写数据流程性能不高
我们要实现对Hadoop进行二次开发,通过修改 Hadoop 源码实现对 NameNode 的 Bug 修复以及对 NameNode 的优化,使 HDFS 集群稳定性达到 9999,一年 365 天稳定运行时间 99.99%

### 1.8.2**海量数据的接收和落地**

初期业务的订单是以Mysql、sql Server作为业务库，但是随着业务线增多，每日新增数据指数上涨，几乎在每天的高峰期期间，都会出现业务库所在服务器的CPU、IO、内存等跑满。因此，需要将每日的azkaban定时采集数据任务，从业务库迁移出来到Hbase上,即要实现通过mysql的binlog日志使业务库和Hbase的采集库数据实现实时同步

### 1.8.3电商微信小程序

### 1.8.4Spark的离线以及流式数据处理的细粒度监控

在Spark的4040页面所提供的监控面板上监控指标并不多,为了满足后期提交job以及定位Spark离线计算以及流计算过程中所出现的Bug我们通过开发SparkListen和StreamListen这两个回调接口,实现Spark执行的细粒度监控大屏

# 2.前期准备工作

## 2.1版本选型

Apache √

CHD 

依据:考虑到2020年CDH开始变为论节点付费模式,一个节点1万美金,大部分公司选择不升级CDH或者转为Apache系统,Apache系统开源免费,最大问题就是不易维护和版本不兼容性问题,尤为突出

## 2.2服务器选型

物理机

阿里云主机(标准版:128G 8T) √

依据:考虑到前期业务量比较小,业务规模呈现线性增长,考虑到阿里云简单,便宜,易扩展,易于维护,所以我们选择阿里云进行大数据集群的搭建

## 2.3集群资源规划设计

假设:每台服务器8T磁盘,128G内存

(1)每天日或跃用户100万,每人一天平均100条:100万*100条=1亿条

(2)每条日志1K作用,每条一亿条:100000000/1024/1024=约100G

(3)半年内不扩容服务器来算:100G*180天=约18T

(4)保存3副本:18T*3=54T

(5)预留20%-30%余量=54T/0.7=77T

(6)约8T*10台服务器

## 2.4全局集群服务规划

|           | 服务器1          | 服务器2                    | 服务器3                   |      |      |
| --------- | ---------------- | -------------------------- | ------------------------- | ---- | ---- |
| HDFS      | NameNodeDataNode | DataNode                   | DataNodeSecondaryNameNode |      |      |
| Yarn      | NodeManager      | ResourcemanagerNodeManager | NodeManager               |      |      |
| Zookeeper | Zookeeper        | Zookeeper                  | Zookeeper                 |      |      |
| Kafka     | Kafka            | Kafka                      | Kafka                     |      |      |
| Flume     | Flume负责采集    | Flume负责采集              | Flume负责消费             |      |      |
| Hive      |                  |                            |                           |      |      |
| Mysql     |                  |                            |                           |      |      |
| Presto    |                  |                            |                           |      |      |
| Kylin     |                  |                            |                           |      |      |



3.5

# 3.AP数据采集平台

## 3.1项目需求分析

日志采集系统-->文件

业务系统-->mysql数据库   --->AP数据采集平台-->把各路数据汇总到DW数仓

爬虫-->mongodb数据库



## 3.2技术选型

日志采集:Flume

业务库数据迁移:Sqoop、DataX

中间件:Kafka

## 3.3AP数据平台架构

![输入图片说明](https://images.gitee.com/uploads/images/2020/1103/205543_5243ec32_7955977.png "AP数据采集平台架构图.png")





#### 3.8.9Kafka的压测

Kafka官方自带压力测试脚本（kafka-consumer-perf-test.sh、kafka-producer-perf-test.sh）。Kafka压测时，可以查看到哪个地方出现了瓶颈（CPU，内存，网络IO）。一般都是网络IO达到瓶颈。

#### 3.8.10Kafak的集群数据规划

Kafka机器数量（经验公式）=2*（峰值生产速度*副本数/100）+1

先拿到峰值生产速度，再根据设定的副本数，就能预估出需要部署Kafka的数量。

假设我们的峰值生产速度是50M/s。副本数为2。

Kafka机器数量=2*（50*2/100）+ 1=3台

#### 3.8.11Flume消费Kafka数据测试数据管道是否打通

### 

# 4.DW离线数据仓库建设

## 4.1项目需求

1.数仓维度建模

2.分析:用户、流量、会员、销售、地区、活动统计指标

3.采用即席查询工具,随时进行指标分析

4.对集群性能进行监控,发送异常需要报警

5.元数据管理

6.质量监控

## 4.2技术选型



## 4.3DW平台架构

## 4.4

# 5.RTDW实时数据仓库建设

## 5.2项目需求分析



## 5.3技术选型



## 5.4DMP平台架构

# 6.DMP用户画像系统

## 6.2项目需求分析



## 6.3技术选型



## 6.4DMP平台架构

# 7.MSDQAP多源数据查询平台

## 7.2项目需求分析



## 7.3技术选型



## 7.4DMP平台架构

# 8.Naga数据分析平台

## 8.2项目需求分析



## 8.3技术选型



## 8.4DMP平台架构

## 

## 