# JWT 学习知识体系

## 一、JWT 基础

- [x] JWT 概念
  - 了解 JSON Web Token 的定义、作用以及解决的问题。

- [x] JWT 应用场景
  - 掌握 JWT 在登录认证、接口鉴权、单点登录、微服务认证中的使用。

- [x] JWT 与 Session 对比
  - 理解有状态 Session 和无状态 JWT 的区别。

- [x] JWT 优缺点
  - 了解 JWT 的扩展性优势以及 Token 泄露、无法主动失效等问题。

## 二、JWT 结构

- [x] JWT 三段式结构
- 理解 JWT 由 Header、Payload、Signature 三部分组成。

- [x] Header（头部）
- 存储 Token 类型和签名算法信息。

- [x] Payload（载荷）
- 存储用户相关信息，例如用户 ID、角色、权限等。

- [x] Signature（签名）
- 用于验证 Token 是否被篡改。


## 三、JWT 工作流程

- [x] 用户登录
- 用户提交账号密码，服务器完成身份验证。

- [x] 生成 JWT
- 服务端根据用户信息生成 Token 并返回。

- [x] 客户端保存 Token
- 了解 Token 在 Cookie、LocalStorage 等位置的存储方式。

- [x] 请求携带 Token
- 学习客户端如何通过 HTTP Header 发送 JWT。

- [x] 服务端验证 Token
- 服务端解析 JWT，验证签名和有效期。


## 四、JWT 核心字段

- [x] iss（Issuer）
- Token 签发者。

- [x] sub（Subject）
- Token 主题，一般表示用户身份。

- [x] exp（Expiration）
- Token 过期时间。

- [x] iat（Issued At）
- Token 创建时间。

- [x] nbf（Not Before）
- Token 生效时间。

- [x] jti（JWT ID）
- Token 唯一标识。


## 五、JWT 编码与算法

- [x] Base64URL 编码
- 理解 JWT 为什么可以被直接解析查看。

- [x] HS256 对称算法
- 使用同一个密钥完成签名和验证。

- [x] RS256 非对称算法
- 私钥签名，公钥验证。

- [x] 签名机制
- 理解签名如何保证 Token 完整性。

- [x] 密钥管理
- 学习生产环境中的密钥保存和轮换。


## 六、Spring Boot 集成 JWT

- [x] JWT 工具类
- 封装 Token 创建、解析、验证方法。

- [x] 登录接口生成 Token
- 用户登录成功后返回 JWT。

- [ ] Filter 过滤器
- 使用过滤器拦截请求并校验 Token。

- [ ] Spring Security 集成
- 使用 JWT 替代传统 Session 认证。

- [ ] SecurityContext
- 验证成功后保存当前用户身份。


## 七、JWT 权限认证

- [ ] Authentication（认证）
- 判断当前用户是谁。

- [ ] Authorization（授权）
- 判断用户是否有访问权限。

- [ ] 角色管理
- 在 JWT 中保存用户角色信息。

- [ ] 权限管理
- 在 JWT 中保存具体操作权限。

- [ ] RBAC 模型
- 理解用户、角色、权限之间的关系。


## 八、JWT 安全

- [x] Token 泄露风险
- 理解 Token 被盗后的安全问题。

- [x] Token 过期
- 使用 exp 控制 Token 生命周期。

- [x] Refresh Token
- 使用刷新 Token 获取新的访问 Token。

- [x] Token 黑名单
- 实现 JWT 主动失效。

- [x] 防止重放攻击
- 防止攻击者重复使用 Token。

- [x] HTTPS
- 保证 Token 传输过程安全。


## 九、JWT 企业实践

- [ ] 登录认证流程
- 完整实现用户登录到接口访问流程。

- [ ] 退出登录
- 解决 JWT 无状态情况下的注销问题。

- [ ] 多设备登录
- 管理不同设备产生的 Token。

- [ ] Token 刷新机制
- 设计 Access Token + Refresh Token 方案。

- [ ] 微服务认证
- 理解网关统一认证和服务内部认证。


## 十、JWT 常见问题

- [ ] JWT 是否加密
- 理解 JWT 默认只是编码，不代表数据加密。

- [ ] Payload 是否安全
- 了解敏感信息不能直接存放在 JWT 中。

- [ ] JWT 是否需要存数据库
- 理解无状态设计和数据库存储的区别。

- [ ] JWT 如何注销
- 掌握黑名单、Redis 等解决方案。

- [ ] JWT 与 OAuth2
- 理解 JWT 是 Token 格式，OAuth2 是授权协议。


## 十一、企业开发进阶

- [ ] JWT + Spring Security 实战
- 完成企业常见认证方案。

- [ ] JWT + Redis
- 使用 Redis 管理 Token 状态。

- [ ] JWT + Gateway
- 实现微服务统一认证。

- [ ] JWT 安全规范
- 掌握生产环境 Token 设计原则。

Token 类型解析方法载荷返回类型签名 + 声明字段`parseSignedClaims()``Claims`签名 + 普通内容`parseSignedContent()``byte[]`加密 + 声明字段`parseEncryptedClaims()``Claims`加密 + 普通内容`parseEncryptedContent()``byte[]`