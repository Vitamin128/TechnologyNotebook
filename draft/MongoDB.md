# MongoDB企业开发知识体系

## 1. MongoDB基础

- [x] 定位
  - [x] NoSQL
  - [x] 文档型数据库
  - [x] BSON存储
- [x] 核心概念
  - [x] Database
  - [x] Collection
  - [x] Document
  - [x] Field
  - [x] ObjectId(_id)
- [x] 对比MySQL
  - [x] Table→Collection
  - [x] Row→Document
  - [x] Column→Field
  - [x] PK→_id
  - [x] Join→引用/$lookup

## 2. CRUD基础

- [x] 插入
  - [x] insertOne()
  - [x] insertMany()
- [x] 查询
  - [x] find()
  - [x] findOne()
  - [x] 条件查询
    - [x] $eq
    - [x] $gt/$gte
    - [x] $lt/$lte
    - [x] $in/$nin
    - [x] $and/$or
- [x] 更新
  - [x] updateOne()
  - [x] updateMany()
  - [x] 更新操作符
    - [x] $set 修改/新增字段
    - [x] $inc 数值增减
    - [x] $unset 删除字段
    - [x] $push 数组追加
    - [x] $pull 数组删除
    - [x] $addToSet 数组去重追加
- [x] 删除
  - [x] deleteOne()
  - [x] deleteMany()

## 3. 查询进阶

- [x] 投影 projection
- [x] 排序 sort()
- [x] 分页
  - [x] skip()
  - [x] limit()
- [x] 嵌套对象查询
- [x] 数组查询
  - [x] $elemMatch

## 4. 数据建模

- [x] 目的
  - [x] 根据查询方式设计Document结构
- [x] 嵌入式模型
  - [x] 数据直接嵌套
  - [x] 适合
    - [x] 数据量小
    - [x] 生命周期一致
    - [x] 经常一起查询
- [x] 引用式模型
  - [x] 保存关联_id
  - [x] 类似外键
  - [x] 适合
    - [x] 数据量大
    - [x] 独立维护
    - [x] 多处共享
- [ ] 关系设计
  - [ ] 一对一
  - [ ] 一对多
  - [ ] 多对多
- [ ] 数据冗余
  - [ ] 空间换查询速度

## 5. 索引

- [ ] 作用
  - [ ] 避免全表扫描
  - [ ] 提升查询速度
- [ ] 原理
  - [ ] B树/B+树思想
  - [ ] 字段值→Document位置
- [ ] 类型
  - [ ] 单字段索引
  - [ ] 复合索引
  - [ ] 唯一索引
  - [ ] TTL索引
- [ ] 索引分析
  - [ ] explain()
  - [ ] executionTimeMillis
  - [ ] totalDocsExamined
  - [ ] totalKeysExamined
  - [ ] IXSCAN/COLLSCAN
- [ ] 优化
  - [ ] 合理建索引
  - [ ] 最左匹配
  - [ ] 避免索引过多

## 6. Aggregation聚合

- [ ] Pipeline模型
- [ ] 常用阶段
  - [ ] $match 过滤
  - [ ] $project 字段处理
  - [ ] $group 分组
  - [x] $sort 排序
  - [x] $limit 限制
  - [x] $lookup 关联
- [ ] 应用
  - [ ] 统计
  - [ ] 报表
  - [ ] 数据分析

## 7. 事务

- [ ] 单Document原子操作
- [ ] 多Document事务
- [ ] 使用场景
  - [ ] 支付
  - [ ] 库存
  - [ ] 账户

## 8. 高可用与扩展

- [ ] Replica Set
  - [ ] Primary
  - [ ] Secondary
  - [ ] oplog
  - [ ] 自动故障转移
- [ ] Sharding
  - [ ] Shard
  - [ ] Config Server
  - [ ] Mongos
  - [ ] Shard Key

## 9. 性能优化

- [ ] 查询优化
  - [ ] 索引
  - [ ] explain分析
  - [ ] 减少扫描
- [ ] 写入优化
  - [ ] 批量写
  - [ ] 控制索引数量
- [ ] 数据设计
  - [ ] 避免超大Document
  - [ ] 避免无限增长数组

## 10. Spring Boot整合

- [ ] 依赖
  - [ ] spring-boot-starter-data-mongodb
- [ ] 配置
  - [ ] uri
  - [ ] database
- [ ] 映射
  - [ ] @Document
  - [ ] @Id
  - [ ] @Field
- [ ] 数据访问
  - [ ] MongoRepository
  - [ ] MongoTemplate

## 11. Spring Data MongoDB

- [ ] 方法查询
  - [ ] findByXXX()
- [ ] Query
  - [ ] Criteria
  - [ ] Sort
  - [ ] Pageable
- [ ] Update
  - [ ] Update对象
- [ ] Aggregation API

## 12. 企业常见场景

- [ ] 用户画像
- [ ] 商品信息
- [ ] 内容系统
- [ ] 评论系统
- [ ] 日志系统
- [ ] 消息记录