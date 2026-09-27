# MongoDB 知识体系

## 1. MongoDB基础
- MongoDB定位
  - NoSQL文档型数据库
  - 数据以Document形式存储
  - BSON格式（二进制JSON）

- 核心概念
  - Database（数据库）
  - Collection（集合）
  - Document（文档）
  - Field（字段）
  - _id（唯一标识）

- MongoDB vs MySQL
  - Table → Collection
  - Row → Document
  - Column → Field
  - Primary Key → _id
  - SQL → MongoDB Query


# 2. CRUD基础

## 插入
- insertOne()
- insertMany()

## 查询
- find()
- findOne()
- 条件查询
  - $eq
  - $ne
  - $gt
  - $gte
  - $lt
  - $lte
  - $in
  - $nin

## 更新
- updateOne()
- updateMany()

更新操作符：
- $set
- $inc
- $unset
- $push
- $pull
- $addToSet

## 删除
- deleteOne()
- deleteMany()


# 3. 查询深入

## 查询条件组合
- $and
- $or
- $not
- $nor

## 数组查询
- 查询数组元素
- $elemMatch
- 数组更新

## 嵌套对象查询
- 点号访问
  - user.address.city

## 字段控制
- projection
  - 指定返回字段
  - 排除字段


# 4. 数据建模

## 两种模型

### 嵌入式建模
特点：
- 数据直接嵌套Document
- 减少查询次数
- 适合：
  - 数据量小
  - 生命周期一致
  - 经常一起查询


### 引用式建模
特点：
- 保存关联ID
- 类似MySQL外键
- 适合：
  - 数据量大
  - 独立维护
  - 多处共享


## 关系设计

### 一对一
例：
- 用户-用户资料

选择：
- 通常嵌入


### 一对多
例：
- 用户-订单

选择：
- 少量 → 嵌入
- 大量 → 引用


### 多对多
例：
- 学生-课程

选择：
- 双向引用
- 中间关系Document


# 5. 索引

## 为什么需要索引
- 提高查询速度
- 减少扫描Document数量


## 索引类型

- 单字段索引
- 复合索引
- 唯一索引
- TTL索引
- 全文索引


## 索引原则

- 最左匹配原则
- 查询字段建立索引
- 避免过多索引


## 索引分析
- explain()

查看：
- 查询计划
- 是否走索引
- 扫描数量


# 6. 聚合查询 Aggregation

作用：
- 数据统计
- 数据转换
- 多阶段处理


核心Pipeline：

## $match
- 条件过滤

## $project
- 字段映射

## $group
- 分组统计

## $sort
- 排序

## $limit
- 限制数量

## $lookup
- 类似Join


示例流程：

match
 ↓
group
 ↓
sort
 ↓
project


# 7. MongoDB事务

## 单Document操作
- 天然原子性


## 多Document事务
支持：
- Replica Set
- Sharded Cluster


使用场景：
- 金融交易
- 库存扣减


# 8. MongoDB存储结构

## BSON类型

常见：
- String
- Integer
- Double
- Boolean
- Date
- Array
- Object
- ObjectId


## ObjectId

组成：
- 时间戳
- 机器标识
- 进程标识
- 自增计数


# 9. MongoDB架构

## 单节点
- 一个MongoDB实例


## Replica Set（副本集）

组成：

Primary
 |
Secondary


作用：
- 高可用
- 数据备份
- 自动故障转移


## Sharding（分片）

作用：
- 海量数据水平扩展


组成：

Shard
- 数据存储节点

Config Server
- 元数据管理

Mongos
- 路由服务


# 10. MongoDB性能优化

## 查询优化
- 合理设计索引
- 避免全表扫描
- 控制返回字段


## 数据设计优化
- 避免超大Document
- 控制数组增长
- 合理选择嵌入/引用


## 写入优化
- 批量写入
- 减少索引数量


# 11. Spring Boot整合MongoDB

## 依赖

spring-boot-starter-data-mongodb


## 配置

application.yml

- uri
- database


## 核心注解

@Document
- 映射Collection


@Id
- 映射_id


@Field
- 字段映射


## 数据访问方式

### MongoRepository

特点：
- 类似JpaRepository
- 简单CRUD


### MongoTemplate

特点：
- 灵活查询
- 复杂条件
- 聚合操作


# 12. Spring Data MongoDB查询

## 方法命名查询

例如：

findByName()

findByAgeGreaterThan()


## Query对象

支持：
- Criteria
- Sort
- Pageable


## 聚合

Aggregation:
- MatchOperation
- GroupOperation
- ProjectionOperation


# 13. MongoDB应用场景

适合：

- 内容管理系统
- 日志系统
- 用户画像
- IoT数据
- 商品信息
- 社交数据


不适合：

- 高度依赖复杂Join
- 强事务系统
- 高度规范化数据


# 14. 企业开发关注点

## 数据设计
- Document边界设计
- 嵌入还是引用


## 性能
- 索引设计
- 查询优化


## 高可用
- Replica Set


## 扩展
- Sharding


## Java应用
- MongoTemplate
- Repository
- 聚合查询