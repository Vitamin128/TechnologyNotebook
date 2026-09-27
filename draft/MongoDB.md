# MongoDB企业开发知识体系

## 1. MongoDB基础
- 定位
  - NoSQL
  - 文档型数据库
  - BSON存储
- 核心概念
  - Database
  - Collection
  - Document
  - Field
  - ObjectId(_id)
- 对比MySQL
  - Table→Collection
  - Row→Document
  - Column→Field
  - PK→_id
  - Join→引用/$lookup
## 2. CRUD基础
- 插入
  - insertOne()
  - insertMany()
- 查询
  - find()
  - findOne()
  - 条件查询
    - $eq
    - $gt/$gte
    - $lt/$lte
    - $in/$nin
    - $and/$or
- 更新
  - updateOne()
  - updateMany()
  - 更新操作符
    - $set 修改/新增字段
    - $inc 数值增减
    - $unset 删除字段
    - $push 数组追加
    - $pull 数组删除
    - $addToSet 数组去重追加
- 删除
  - deleteOne()
  - deleteMany()
## 3. 查询进阶
- 投影 projection
- 排序 sort()
- 分页
  - skip()
  - limit()
- 嵌套对象查询
- 数组查询
  - $elemMatch
## 4. 数据建模
- 目的
  - 根据查询方式设计Document结构
- 嵌入式模型
  - 数据直接嵌套
  - 适合
    - 数据量小
    - 生命周期一致
    - 经常一起查询
- 引用式模型
  - 保存关联_id
  - 类似外键
  - 适合
    - 数据量大
    - 独立维护
    - 多处共享
- 关系设计
  - 一对一
  - 一对多
  - 多对多
- 数据冗余
  - 空间换查询速度
## 5. 索引
- 作用
  - 避免全表扫描
  - 提升查询速度
- 原理
  - B树/B+树思想
  - 字段值→Document位置
- 类型
  - 单字段索引
  - 复合索引
  - 唯一索引
  - TTL索引
- 索引分析
  - explain()
  - executionTimeMillis
  - totalDocsExamined
  - totalKeysExamined
  - IXSCAN/COLLSCAN
- 优化
  - 合理建索引
  - 最左匹配
  - 避免索引过多
## 6. Aggregation聚合
- Pipeline模型
- 常用阶段
  - $match 过滤
  - $project 字段处理
  - $group 分组
  - $sort 排序
  - $limit 限制
  - $lookup 关联
- 应用
  - 统计
  - 报表
  - 数据分析
## 7. 事务
- 单Document原子操作
- 多Document事务
- 使用场景
  - 支付
  - 库存
  - 账户
## 8. 高可用与扩展
- Replica Set
  - Primary
  - Secondary
  - oplog
  - 自动故障转移
- Sharding
  - Shard
  - Config Server
  - Mongos
  - Shard Key
## 9. 性能优化
- 查询优化
  - 索引
  - explain分析
  - 减少扫描
- 写入优化
  - 批量写
  - 控制索引数量
- 数据设计
  - 避免超大Document
  - 避免无限增长数组
## 10. Spring Boot整合
- 依赖
  - spring-boot-starter-data-mongodb
- 配置
  - uri
  - database
- 映射
  - @Document
  - @Id
  - @Field
- 数据访问
  - MongoRepository
  - MongoTemplate
## 11. Spring Data MongoDB
- 方法查询
  - findByXXX()
- Query
  - Criteria
  - Sort
  - Pageable
- Update
  - Update对象
- Aggregation API
## 12. 企业常见场景
- 用户画像
- 商品信息
- 内容系统
- 评论系统
- 日志系统
- 消息记录
## 学习优先级
1. CRUD
2. 数据建模
3. 索引
4. 查询优化
5. Spring Data MongoDB
6. Aggregation
7. 事务
8. Replica Set
9. Sharding