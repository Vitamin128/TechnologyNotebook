# Java 常见类设计形态学习清单

> 说明：这里的“类设计形态”指一个 Java 类在系统中的**职责角色与设计定位**，并不等同于 GoF 23 种设计模式。很多类在语法上都是 `class`，但它们承担的数据表达、业务行为、基础设施、对象创建、流程编排等职责不同。

---

# 学习进度

## 数据表达类

- [ ] Entity（实体）
- [ ] Value Object（值对象）
- [ ] DTO（数据传输对象）
- [ ] VO（视图对象）
- [ ] Request / Command（请求对象 / 命令对象）
- [ ] Response（响应对象）
- [ ] Context（上下文对象）
- [ ] Configuration Properties（配置数据对象）

## 行为与业务类

- [ ] Service（服务类）
- [ ] Domain Service（领域服务）
- [ ] Strategy（策略类）
- [ ] Handler（处理器）
- [ ] Validator（校验器）
- [ ] Listener（监听器）
- [ ] Task / Job（任务类）
- [ ] Policy / Rule（规则类）

## 数据访问与外部集成类

- [ ] Repository（仓储）
- [ ] DAO / Mapper（数据访问类）
- [ ] Client（外部客户端）
- [ ] Adapter（适配器）
- [ ] Gateway（网关）
- [ ] Converter / Mapper（转换器）

## 对象创建与装配类

- [ ] Factory（工厂）
- [ ] Builder（构建器）
- [ ] Configuration（配置类）

## 复用与扩展类

- [ ] Abstract Base Class（抽象基类）
- [ ] Template（模板类）
- [ ] Decorator（装饰器）
- [ ] Wrapper（包装器）
- [ ] Proxy（代理类）

## 辅助与特殊类

- [ ] Utility Class（工具类）
- [ ] Constants Class（常量类）
- [ ] Exception（异常类）
- [ ] Enum（枚举类型）
- [ ] Immutable Class（不可变类）
- [ ] Record（记录类）

---

# 一、数据表达类

## - [ ] Entity（实体）

### 一句话定义

表示一个具有**业务身份、生命周期和状态变化**的对象。

### 核心职责

承载业务实体的身份和状态，例如用户、订单、账户、商品。

### 核心特征

- 通常有唯一标识，例如 `id`。
- 即使属性发生变化，只要身份没有变化，仍然认为是同一个实体。
- 经常具有明确生命周期。
- 可能会被数据库持久化，但“Entity”本身不等于“数据库表对象”。

### 典型场景

- 用户 `User`
- 订单 `Order`
- 钱包账户 `WalletAccount`
- 商品 `Product`

### 示例

```java
public class User {
    private Long id;
    private String name;
    private Integer age;
}
```

### 如何识别

问自己：

> 这个对象最重要的是“它是谁”，还是“它的值是什么”？

如果最重要的是身份，通常属于 Entity。

### 与相似类型的区别

- Entity 强调身份。
- Value Object 强调值。
- DTO 主要负责传输数据，不应该天然承担完整业务生命周期。

### 常见误区

- 不要把所有数据库 POJO 都简单理解成领域 Entity。
- Entity 不一定必须和数据库表一一对应。

---

## - [ ] Value Object（值对象）

### 一句话定义

表示一个**没有独立业务身份、主要由自身值决定意义**的对象。

### 核心职责

把一组有业务含义的数据封装成一个明确类型。

### 核心特征

- 不强调对象身份。
- 通常根据值判断相等。
- 通常设计成不可变对象。
- 可以带有与该值紧密相关的行为。

### 典型场景

- 金额 `Money`
- 时间点 `Instant`
- 日期 `LocalDate`
- 邮箱地址 `EmailAddress`
- 坐标 `Point`

### 示例

```java
public record Money(BigDecimal amount, String currency) {
}
```

### JDK 示例

- `java.time.Instant`
- `java.time.LocalDate`
- `java.math.BigDecimal`
- `java.util.UUID`

### 如何识别

问自己：

> 两个对象只要内部值相同，在业务上是否就可以视为相同？

如果是，通常属于 Value Object。

### 与相似类型的区别

- Entity 看身份。
- Value Object 看值。
- Utility Class 不代表一个值，只提供方法。

### 常见误区

`Instant` 不是传统意义上的工具类，它是一个不可变的时间值对象。

---

## - [ ] DTO（Data Transfer Object）

### 一句话定义

用于在不同层、不同模块或不同系统之间**传输数据**的对象。

### 核心职责

减少跨层传输时对领域对象、数据库对象或第三方对象的直接暴露。

### 核心特征

- 以数据承载为主。
- 通常业务逻辑较少。
- 字段结构由传输需求决定。
- 可以使用普通 `class`，也可以使用 `record`。

### 典型场景

- Service 与 Controller 之间传递数据。
- RPC 接口参数。
- 第三方 API 响应映射。

### 示例

```java
public record UserDTO(Long id, String name, Integer age) {
}
```

### 如何识别

问自己：

> 这个类主要是不是为了把一组数据从 A 搬到 B？

如果是，通常属于 DTO。

### 与相似类型的区别

DTO 是泛称；Request、Response、VO 都可能是更具体的数据传输角色。

### 常见误区

DTO 不应该因为“方便”就逐渐变成承载大量业务逻辑的万能对象。

---

## - [ ] VO（View Object）

### 一句话定义

面向展示层或客户端返回结构设计的数据对象。

### 核心职责

组织前端、页面、App 或接口消费者真正需要看到的数据。

### 核心特征

- 字段通常按展示需求组织。
- 可能聚合多个实体的数据。
- 不一定和数据库表字段一致。

### 典型场景

- 用户详情页返回对象。
- 订单详情聚合展示对象。
- 管理后台列表返回对象。

### 示例

```java
public record UserVO(
        Long id,
        String displayName,
        String avatarUrl
) {
}
```

### 如何识别

问自己：

> 这个结构是不是主要为了“给前端看”？

### 与相似类型的区别

- DTO 强调传输。
- VO 强调展示结构。
- Entity 强调业务身份。

### 常见误区

不同团队对 VO 的命名约定可能不同，应以项目规范为准。

---

## - [ ] Request / Command（请求对象 / 命令对象）

### 一句话定义

表示调用方希望系统执行某个操作时提交的一组输入数据。

### 核心职责

承载接口输入，并明确表达“要做什么”。

### 核心特征

- 常用于 Controller、RPC、消息消费入口。
- 往往配合参数校验。
- Command 比普通 Request 更强调业务意图。

### 典型场景

- `CreateOrderRequest`
- `RegisterUserRequest`
- `TransferCommand`

### 示例

```java
public record CreateOrderRequest(
        Long userId,
        Long productId,
        Integer quantity
) {
}
```

### 如何识别

类名通常带有动作：`Create`、`Update`、`Delete`、`Transfer`、`Submit`。

### 与相似类型的区别

Request 更偏接口输入；Command 更偏明确业务动作。

### 常见误区

不要直接把数据库 Entity 当作所有接口的 Request。

---

## - [ ] Response（响应对象）

### 一句话定义

表示系统对外输出的数据结构。

### 核心职责

把内部结果转换成调用方需要的稳定协议结构。

### 核心特征

- 面向调用方。
- 经常和内部领域模型隔离。
- 可以是统一响应包装，也可以是具体业务响应对象。

### 示例

```java
public record UserResponse(
        Long id,
        String name
) {
}
```

### 如何识别

通常出现在 API 返回值、RPC 返回值或消息处理结果中。

### 与相似类型的区别

VO 更强调展示；Response 更强调“接口输出协议”。两者在实际项目中可能重叠。

### 常见误区

不要为了少写一个类就直接把 Entity 暴露给外部 API。

---

## - [ ] Context（上下文对象）

### 一句话定义

承载一次请求、一次任务或一次业务流程中需要共享的上下文信息。

### 核心职责

在多个处理步骤之间传递公共信息。

### 核心特征

- 生命周期通常和一次流程绑定。
- 可能包含用户、traceId、租户、权限、环境等信息。
- 不应该成为“什么都往里塞”的全局垃圾桶。

### 示例

```java
public class RequestContext {
    private Long userId;
    private String traceId;
    private String tenantId;
}
```

### 如何识别

问自己：

> 这些数据是不是由多个步骤共同需要，并且只在当前流程中有效？

### 与相似类型的区别

Context 强调流程共享状态；DTO 强调数据传输。

### 常见误区

Context 过度膨胀会造成隐式依赖和代码难以测试。

---

## - [ ] Configuration Properties（配置数据对象）

### 一句话定义

将外部配置映射成结构化 Java 对象。

### 核心职责

集中管理应用配置，并提供类型安全访问。

### 典型场景

Spring Boot 的 `@ConfigurationProperties`。

### 示例

```java
@ConfigurationProperties(prefix = "jwt")
public record JwtProperties(
        String issuer,
        String audience,
        long ttlSeconds
) {
}
```

### 如何识别

字段来源不是数据库或请求，而是 `application.yml`、环境变量或配置中心。

### 与相似类型的区别

它本质上也是数据对象，但数据来源和生命周期是“应用配置”。

### 常见误区

不要把配置读取散落到大量 `@Value` 中而失去统一管理能力。

---

# 二、行为与业务类

## - [ ] Service（服务类）

### 一句话定义

负责完成某一类业务能力或应用用例的行为类。

### 核心职责

组织业务规则、调用 Repository、Client、Strategy 等组件完成业务流程。

### 典型场景

- `UserService`
- `OrderService`
- `PaymentService`

### 示例

```java
@Service
public class OrderService {
    public void createOrder() {
        // 业务流程
    }
}
```

### 如何识别

问自己：

> 这个类的主要价值是不是“执行一类业务行为”？

### 与相似类型的区别

Service 通常负责业务；Utility 只是通用辅助方法。

### 常见误区

Service 不应无限膨胀成几千行“上帝类”。

---

## - [ ] Domain Service（领域服务）

### 一句话定义

承载无法自然归属于单个 Entity 或 Value Object 的领域规则。

### 核心职责

表达跨多个领域对象的核心业务规则。

### 典型场景

- 定价规则
- 转账规则
- 风控判断

### 示例

```java
public class TransferDomainService {
    public void transfer(Account from, Account to, Money money) {
        // 领域规则
    }
}
```

### 如何识别

如果逻辑是核心业务规则，但放到任何单个实体里都不自然，可以考虑 Domain Service。

### 与相似类型的区别

Application Service 更偏流程编排；Domain Service 更偏领域规则本身。

### 常见误区

不要把所有业务代码都叫 Domain Service。

---

## - [ ] Strategy（策略类）

### 一句话定义

把一组可互换算法或业务方案抽象成统一接口的实现类。

### 核心职责

允许系统在运行时或配置阶段切换不同处理方案。

### 典型场景

- 不同支付渠道
- 不同折扣算法
- 不同路由策略

### 示例

```java
public interface PaymentStrategy {
    void pay();
}

public class AlipayStrategy implements PaymentStrategy {
    public void pay() {
    }
}
```

### 如何识别

多个类实现同一接口，但“做法不同、目标相同”。

### 与相似类型的区别

Strategy 强调可替换算法；Handler 更强调处理某种请求或节点。

### 常见误区

只有一个实现且没有替换需求时，不必为了模式而模式。

---

## - [ ] Handler（处理器）

### 一句话定义

负责处理某种事件、请求、命令或流程节点的类。

### 核心职责

把不同处理职责拆开，让每个处理器聚焦一个输入或步骤。

### 典型场景

- 消息处理器
- 审批节点处理器
- 支付回调处理器
- 责任链节点

### 示例

```java
public interface Handler<T> {
    void handle(T input);
}
```

### 如何识别

类名经常包含 `Handler`，且入口通常是 `handle(...)`。

### 与相似类型的区别

Handler 通常聚焦单次处理；Service 更偏完整业务能力。

### 常见误区

不要让 Handler 同时承担持久化、远程调用、复杂业务编排等所有职责。

---

## - [ ] Validator（校验器）

### 一句话定义

专门负责检查输入、状态或业务规则是否满足约束。

### 核心职责

把校验逻辑从主业务流程中拆出来。

### 示例

```java
public class OrderValidator {
    public void validate(Order order) {
        if (order == null) {
            throw new IllegalArgumentException("order不能为空");
        }
    }
}
```

### 如何识别

主要方法通常围绕 `validate`、`check`、`verify`。

### 与相似类型的区别

Validator 只负责判断合法性；Service 负责完成业务流程。

### 常见误区

不要把简单的单字段校验拆成过度复杂的类体系。

---

## - [ ] Listener（监听器）

### 一句话定义

监听事件发生，并在事件触发后执行对应逻辑。

### 核心职责

解耦“事件产生者”和“事件响应者”。

### 典型场景

- Spring ApplicationEvent
- MQ 消费
- GUI 事件
- 生命周期事件

### 示例

```java
@Component
public class UserRegisteredListener {
    @EventListener
    public void onUserRegistered(UserRegisteredEvent event) {
    }
}
```

### 如何识别

被动等待事件触发，而不是由业务方直接调用完成主要流程。

### 与相似类型的区别

Listener 强调事件驱动；Handler 强调处理职责。

### 常见误区

事件链过深会降低流程可追踪性。

---

## - [ ] Task / Job（任务类）

### 一句话定义

表示一段可调度、可异步或可批量执行的工作。

### 核心职责

封装定时任务、批处理、后台任务。

### 示例

```java
@Component
public class OrderCleanupJob {
    @Scheduled(cron = "0 0 2 * * ?")
    public void execute() {
    }
}
```

### 如何识别

生命周期由调度器、线程池或任务框架驱动。

### 与相似类型的区别

Job 负责“什么时候执行”；Service 负责“业务怎么执行”。

### 常见误区

任务类中不要堆大量业务细节，通常应该调用 Service。

---

## - [ ] Policy / Rule（规则类）

### 一句话定义

封装一条或一组明确的业务规则。

### 核心职责

让复杂判断逻辑可组合、可复用、可单测。

### 示例

```java
public class WithdrawalRule {
    public boolean canWithdraw(Account account, Money money) {
        return true;
    }
}
```

### 如何识别

类的核心产出通常是“是否满足规则”或“按照规则得到某个结果”。

### 与相似类型的区别

Strategy 强调替换方案；Rule 强调业务约束。

### 常见误区

简单 `if` 不一定都需要抽成独立 Rule。

---

# 三、数据访问与外部集成类

## - [ ] Repository（仓储）

### 一句话定义

为领域层提供类似“对象集合”的持久化访问抽象。

### 核心职责

屏蔽底层数据库实现，让上层围绕领域对象进行读取和保存。

### 示例

```java
public interface UserRepository {
    User findById(Long id);
    void save(User user);
}
```

### 如何识别

接口通常围绕领域对象，而不是直接围绕 SQL。

### 与相似类型的区别

Repository 偏领域抽象；DAO / Mapper 更贴近数据库访问。

### 常见误区

在简单 CRUD 项目中，Repository 和 DAO 的边界可能并不严格。

---

## - [ ] DAO / Mapper（数据访问类）

### 一句话定义

直接负责数据库 CRUD、SQL 或 ORM 数据访问。

### 核心职责

把 SQL、ORM、表结构访问封装在数据访问层。

### 示例

```java
@Mapper
public interface UserMapper {
    UserDO selectById(Long id);
}
```

### 如何识别

经常出现 `select`、`insert`、`update`、`delete` 等数据库语义。

### 与相似类型的区别

DAO 更靠近数据库；Repository 更靠近领域模型。

### 常见误区

不要把复杂业务规则写在 Mapper 中。

---

## - [ ] Client（外部客户端）

### 一句话定义

封装对外部 HTTP、RPC、SDK 或其他远程服务的调用。

### 核心职责

隔离外部协议、认证、序列化、超时、错误处理。

### 典型场景

- Feign Client
- RestClient 封装
- 第三方支付 SDK

### 示例

```java
@FeignClient(name = "user-service")
public interface UserClient {
    @GetMapping("/users/{id}")
    UserResponse getUser(@PathVariable Long id);
}
```

### 如何识别

这个类的主要对象不是本地数据库，而是“另一个系统”。

### 与相似类型的区别

Client 强调调用外部服务；Repository 强调持久化；Gateway 可以进一步屏蔽多种外部实现。

### 常见误区

不要让业务层直接散落大量 HTTP 拼装逻辑。

---

## - [ ] Adapter（适配器）

### 一句话定义

把一个已有接口转换成系统期望的另一种接口。

### 核心职责

解决接口不兼容问题。

### 示例

```java
public class StripePaymentAdapter implements PaymentGateway {
    private final StripeClient client;

    public StripePaymentAdapter(StripeClient client) {
        this.client = client;
    }
}
```

### 如何识别

“外部接口长这样，但系统内部希望用另一套接口”。

### 与相似类型的区别

Adapter 重点是接口转换；Decorator 重点是增强行为。

### 常见误区

不要把所有第三方封装都随便叫 Adapter，应看是否真的发生了接口适配。

---

## - [ ] Gateway（网关）

### 一句话定义

为上层提供访问外部系统、支付渠道、消息系统等基础设施的统一业务接口。

### 核心职责

隐藏外部系统差异和协议细节。

### 示例

```java
public interface PaymentGateway {
    PaymentResult pay(PaymentRequest request);
}
```

### 如何识别

上层只知道“我要支付”，而不知道底层是 Stripe、支付宝还是其他渠道。

### 与相似类型的区别

Gateway 是更业务化的边界抽象；Client 通常更贴近具体远程服务。

### 常见误区

Gateway 不应把具体厂商 SDK 类型泄露给业务层。

---

## - [ ] Converter / Mapper（转换器）

### 一句话定义

负责不同对象模型之间的转换。

### 核心职责

集中处理 Entity、DTO、VO、DO、第三方对象之间的映射。

### 示例

```java
public class UserConverter {
    public static UserVO toVO(User user) {
        return new UserVO(user.getId(), user.getName(), null);
    }
}
```

### 如何识别

核心方法往往是 `toDTO`、`toVO`、`fromRequest`、`convert`。

### 与相似类型的区别

Converter 改变对象表示；Adapter 改变接口。

### 常见误区

字段很少时无需过度封装；复杂映射时可考虑 MapStruct。

---

# 四、对象创建与装配类

## - [ ] Factory（工厂）

### 一句话定义

负责集中创建某类对象，并隐藏对象创建细节。

### 核心职责

将复杂构造逻辑、实现选择逻辑从调用方移走。

### 示例

```java
public class PaymentFactory {
    public static PaymentStrategy create(String type) {
        return switch (type) {
            case "ALI" -> new AlipayStrategy();
            default -> throw new IllegalArgumentException();
        };
    }
}
```

### 如何识别

调用方想要的是“一个对象”，但不想关心它具体怎么创建。

### 与相似类型的区别

Factory 决定创建什么；Builder 决定如何逐步组装一个复杂对象。

### 常见误区

简单 `new` 足够时，没有必要额外引入工厂。

---

## - [ ] Builder（构建器）

### 一句话定义

通过分步骤设置参数来创建复杂对象。

### 核心职责

解决构造参数多、可选参数多、构造过程复杂的问题。

### 示例

```java
User user = User.builder()
        .name("Tom")
        .age(20)
        .build();
```

### 如何识别

对象创建过程比一个简单构造器更复杂。

### 与相似类型的区别

Builder 强调“组装过程”；Factory 强调“创建策略”。

### 常见误区

字段只有两个时通常不需要 Builder。

---

## - [ ] Configuration（配置类）

### 一句话定义

负责声明和装配应用组件、Bean 或框架配置。

### 核心职责

告诉框架“哪些对象应该被创建，以及如何连接起来”。

### 示例

```java
@Configuration
public class HttpClientConfig {
    @Bean
    public RestClient restClient() {
        return RestClient.builder().build();
    }
}
```

### 如何识别

它主要不是业务对象，而是用来“组装系统”。

### 与相似类型的区别

Configuration 是装配定义；Configuration Properties 是配置数据。

### 常见误区

不要把业务逻辑写进配置类。

---

# 五、复用与扩展类

## - [ ] Abstract Base Class（抽象基类）

### 一句话定义

通过继承提供公共状态、默认行为或扩展点的抽象父类。

### 核心职责

复用多个子类之间真正稳定的公共实现。

### 示例

```java
public abstract class AbstractPaymentHandler {
    public abstract void pay();
}
```

### 如何识别

多个子类存在明显“is-a”关系，并共享稳定行为。

### 与相似类型的区别

接口主要定义能力；抽象基类还可以提供状态和部分实现。

### 常见误区

不要仅仅为了复用几行代码就制造不自然的继承体系。

---

## - [ ] Template（模板类）

### 一句话定义

在父类中定义固定流程，把部分步骤交给子类实现。

### 核心职责

固定算法骨架，同时允许部分步骤扩展。

### 示例

```java
public abstract class AbstractImportService {
    public final void execute() {
        read();
        validate();
        save();
    }

    protected abstract void read();
    protected abstract void validate();
    protected abstract void save();
}
```

### 如何识别

整体流程固定，但其中某些步骤因场景不同而变化。

### 与相似类型的区别

Template 常依赖继承；Strategy 常依赖组合。

### 常见误区

现代 Java 中很多场景可优先考虑组合而不是深层继承。

---

## - [ ] Decorator（装饰器）

### 一句话定义

在不修改原对象接口的前提下，为对象动态增加额外行为。

### 核心职责

增强日志、缓存、监控、权限等横切能力。

### 示例

```java
public class LoggingPaymentService implements PaymentService {
    private final PaymentService delegate;

    public void pay() {
        System.out.println("before");
        delegate.pay();
        System.out.println("after");
    }
}
```

### 如何识别

包装对象实现相同接口，并在调用前后增加行为。

### 与相似类型的区别

Decorator 增强行为；Adapter 改变接口；Proxy 控制访问。

### 常见误区

Decorator、Proxy、Wrapper 结构上相似，应该根据“目的”区分。

---

## - [ ] Wrapper（包装器）

### 一句话定义

用另一个对象包住原对象，提供更方便、更安全或更统一的使用方式。

### 核心职责

封装底层复杂性或补充额外上下文。

### 示例

```java
public class SafeHttpResponse {
    private final HttpResponse response;
}
```

### 如何识别

它持有另一个对象，并围绕这个对象提供新的使用入口。

### 与相似类型的区别

Wrapper 是广义概念；Decorator、Adapter、Proxy 都可以看成更具体的包装形式。

### 常见误区

不要只看“里面持有另一个对象”就下结论，要看包装目的。

---

## - [ ] Proxy（代理类）

### 一句话定义

用代理对象代表目标对象，并控制或增强对目标对象的访问。

### 核心职责

实现 AOP、权限检查、事务、延迟加载、远程调用等能力。

### 示例

```text
调用方
  ↓
代理对象
  ↓
目标对象
```

Spring AOP 中常见：

```java
@Transactional
public void createOrder() {
}
```

外部调用通常先经过 Spring 代理对象。

### 如何识别

调用方以为自己在调用目标对象，但实际上先经过另一个对象。

### 与相似类型的区别

- Proxy：控制访问。
- Decorator：增强功能。
- Adapter：转换接口。

### 常见误区

Spring Bean 被 AOP 增强后，容器中通常注入的是代理引用，而不是直接暴露原始目标对象。

---

# 六、辅助与特殊类

## - [ ] Utility Class（工具类）

### 一句话定义

不代表具体业务对象，主要提供一组无状态、通用的辅助方法。

### 核心职责

提供可复用的小型操作能力。

### 核心特征

- 常见大量 `static` 方法。
- 通常禁止实例化。
- 一般不保存实例状态。

### 示例

```java
public final class StringUtils {
    private StringUtils() {
    }

    public static boolean isBlank(String value) {
        return value == null || value.isBlank();
    }
}
```

### 如何识别

这个类本身不表示“一个东西”，只是提供方法。

### 与相似类型的区别

`Instant` 不是 Utility Class，因为一个 `Instant` 对象本身就表示一个具体时间点。

### 常见误区

不要把大量业务逻辑塞进 `XXXUtils`，否则会失去清晰的职责边界。

---

## - [ ] Constants Class（常量类）

### 一句话定义

集中保存一组共享常量。

### 核心职责

避免魔法值散落在代码中。

### 示例

```java
public final class OrderConstants {
    private OrderConstants() {
    }

    public static final String STATUS_PAID = "PAID";
}
```

### 如何识别

基本只有 `public static final` 字段。

### 与相似类型的区别

如果常量本身是一组有限业务状态，优先考虑 `enum`。

### 常见误区

不要创建一个超级 `Constants` 类容纳整个项目所有常量。

---

## - [ ] Exception（异常类）

### 一句话定义

用于表达程序中的异常状态和错误语义。

### 核心职责

把错误类型显式建模，并沿调用栈传播。

### 示例

```java
public class BusinessException extends RuntimeException {
    public BusinessException(String message) {
        super(message);
    }
}
```

### 如何识别

继承 `Throwable`、`Exception` 或 `RuntimeException`。

### 与相似类型的区别

异常类不是业务返回 DTO，它走的是异常传播机制。

### 常见误区

不要用异常代替正常业务分支控制。

---

## - [ ] Enum（枚举类型）

### 一句话定义

表示一组有限、固定的离散值。

### 核心职责

用类型安全方式表示状态、类型、模式或有限选项。

### 示例

```java
public enum OrderStatus {
    CREATED,
    PAID,
    CANCELLED
}
```

### 如何识别

业务值集合是有限且相对稳定的。

### 与相似类型的区别

相比字符串常量，enum 有更好的类型安全和行为封装能力。

### 常见误区

不要用 enum 表达经常动态新增、需要数据库配置的业务数据。

---

## - [ ] Immutable Class（不可变类）

### 一句话定义

对象创建完成后，其可观察状态不会再发生变化。

### 核心职责

提供更安全、更容易推理、更适合并发共享的数据模型。

### 核心特征

- 字段通常 `final`。
- 不暴露修改内部状态的方法。
- 修改操作通常返回新对象。

### JDK 示例

- `String`
- `Instant`
- `LocalDate`
- `BigInteger`

### 示例

```java
Instant now = Instant.now();
Instant later = now.plusSeconds(10);
```

`plusSeconds` 不会修改 `now`，而是返回新的 `Instant`。

### 如何识别

创建后没有 setter，也没有会修改自身状态的方法。

### 与相似类型的区别

Immutable 是一种“对象状态设计属性”，不是业务层角色。一个 Value Object 经常同时也是 Immutable Class。

### 常见误区

`final` 引用不代表引用对象内部一定不可变，例如 `final List<String>` 仍可能被修改。

---

## - [ ] Record（记录类）

### 一句话定义

Java 提供的一种专门用于表达简洁数据载体和值对象的特殊类语法。

### 核心职责

减少 DTO、Value Object 等数据类中的样板代码。

### 核心特征

- 组件字段隐式为 `private final`。
- 自动生成构造器。
- 自动生成 accessor。
- 自动生成 `equals`、`hashCode`、`toString`。
- 隐式 `final`。
- 不能继承普通父类。

### 示例

```java
public record UserDTO(Long id, String name) {
}
```

### 如何识别

声明关键字直接使用 `record`。

### 与相似类型的区别

`record` 是 Java 语法层面的特殊类形态；DTO、VO、Value Object 是设计角色。一个 record 可以承担 DTO，也可以承担 Value Object。

### 常见误区

不要把 `record` 当作一种业务架构层级。它只是类的语言级表达方式。

---

# 七、快速判断一个 Java 类属于什么角色

看到一个陌生类时，可以按下面顺序判断：

- [ ] 它主要是在表示“一个业务对象”吗？
  - 有身份和生命周期 → Entity
  - 只看值 → Value Object
- [ ] 它主要是在搬运数据吗？
  - 通用跨层 → DTO
  - 接口输入 → Request / Command
  - 接口输出 → Response
  - 页面展示 → VO
- [ ] 它主要是在执行行为吗？
  - 完整业务能力 → Service
  - 可切换算法 → Strategy
  - 处理某一类输入 → Handler
  - 做校验 → Validator
  - 响应事件 → Listener
- [ ] 它主要是在访问基础设施吗？
  - 数据库 → DAO / Mapper / Repository
  - 外部服务 → Client / Gateway
  - 接口转换 → Adapter
  - 对象转换 → Converter
- [ ] 它主要是在创建对象吗？
  - 选择如何创建 → Factory
  - 分步骤组装 → Builder
- [ ] 它主要是在提供辅助能力吗？
  - 静态无状态方法 → Utility Class
  - 常量集合 → Constants Class
  - 错误语义 → Exception
- [ ] 它主要是在扩展或包裹其他对象吗？
  - 控制访问 → Proxy
  - 增强行为 → Decorator
  - 转换接口 → Adapter
  - 普通封装 → Wrapper

---

# 八、重要认知

## - [ ] 一个类可以同时具备多个属性

例如：

```java
public record Money(BigDecimal amount, String currency) {
}
```

它可以同时是：

- Record
- Value Object
- Immutable Object

这些标签并不冲突，因为它们描述的是不同维度。

---

## - [ ] “语法形态”和“设计角色”要区分

例如：

```text
class / record / enum / interface
```

属于 Java 语言层面的类型声明方式。

而：

```text
Entity / DTO / Service / Factory / Strategy
```

属于系统设计中的职责角色。

---

## - [ ] 不要只看类名判断职责

例如：

```text
UserService
UserManager
UserHelper
UserProcessor
```

真正判断角色的标准应该是：

> 它的数据、方法、依赖关系和生命周期到底在承担什么职责。

---

# 九、给其他 AI 使用的统一 Markdown 生成提示词

下面这段提示词可以直接复制给其他 AI，用于继续生成同风格的 Java、Spring、数据库、中间件等学习笔记。

```text
你现在要为我生成一份“长期维护型技术学习 Markdown 笔记”。

请严格遵守以下格式规范：

【一、整体结构】
1. 文档必须使用 Markdown。
2. 一级标题 `#` 表示大章节。
3. 二级标题 `##` 表示具体知识点。
4. 每一个具体知识点标题前必须带任务复选框，格式统一为：

   ## - [ ] 知识点名称

5. 文档开头必须先提供一个“学习进度”总清单，所有知识点都在总清单中出现一次。
6. 正文按照知识体系分类，不要简单堆砌知识点。
7. 使用 `---` 分隔大的知识卡片或章节。

【二、每个知识点固定模板】
每个知识点优先按照下面的固定结构生成：

- [ ] 知识点名称

### 一句话定义
用 1～2 句话说明这个概念本质是什么。

### 核心职责
说明它主要解决什么问题、承担什么职责。

### 核心特征
使用 Markdown 无序列表列出最重要特征。

### 典型场景
列出企业开发中常见使用场景。

### 示例
提供简短、正确、可直接理解的 Java / SQL / 配置示例。

### 如何识别
给出一个可以帮助我快速判断该概念的判断问题，例如：
“看到一个类时，问自己：它主要是在表示数据，还是执行行为？”

### 与相似类型的区别
重点对比容易混淆的概念，不要泛泛而谈。

### 常见误区
列出初学者最容易产生的错误理解。

如果某个栏目对当前知识点确实不适用，可以省略，但不要随意改变栏目名称。

【三、表达风格】
1. 使用中文。
2. 面向 Java / Spring 后端开发学习者。
3. 解释必须准确、直接、工程化。
4. 不要使用过度学术化的语言。
5. 不要为了完整而写大量低价值废话。
6. 优先使用“定义 → 职责 → 特征 → 场景 → 示例 → 判断 → 对比 → 误区”的学习路径。
7. 一个概念第一次出现英文全称时，格式为：
   中文名（English Name）
8. Java 类名、方法名、关键字、注解使用反引号，例如：
   `Instant`、`record`、`@Service`
9. 代码必须使用 fenced code block，并注明语言：
   ```java
   ```
10. 对非常重要的结论可以使用 Markdown 引用：
   > 核心结论

【四、知识组织规则】
1. 区分“语言语法形态”和“设计职责角色”。
2. 如果一个概念可以同时属于多个维度，要明确说明这些标签并不冲突。
3. 不要强行把所有概念归到唯一类别。
4. 对容易混淆的内容必须给出对比，例如：
   - Entity vs Value Object
   - DTO vs VO
   - Repository vs DAO
   - Adapter vs Decorator vs Proxy
5. 优先给出 JDK、Spring Boot、MyBatis、常见企业项目中的真实例子。
6. 示例代码控制在能说明问题的最小范围，不要生成大段无关代码。

【五、Checklist 规范】
1. 所有需要学习、掌握、复习的知识点必须以 `- [ ]` 开头。
2. 已学习的内容由我自己改成 `- [x]`，AI 不要主动标记完成。
3. 子任务也可以使用：
   - [ ] 理解定义
   - [ ] 能识别使用场景
   - [ ] 能写简单示例
   - [ ] 能和相似概念区分
4. 不要使用 emoji 作为进度标记。

【六、目标】
最终文档应该同时具备：
- Checklist：能记录学习进度
- Knowledge Map：能看到完整知识结构
- Knowledge Card：每个知识点可以独立复习
- Reference Manual：以后忘记时可以快速查阅

我后续给你一个技术主题时，请直接按照这套规范生成完整 Markdown，不要重新设计格式。
```

---

# 十、推荐使用方式

- [ ] 第一次学习时，只看“一句话定义 + 核心职责 + 示例”。
- [ ] 第二次复习时，重点看“如何识别 + 与相似类型的区别”。
- [ ] 能独立解释并写出例子后，再把对应项从 `- [ ]` 改为 `- [x]`。
- [ ] 对经常混淆的知识点，在 Obsidian 中建立双向链接。
- [ ] 后续新增知识点时继续沿用同一模板，不要频繁修改笔记结构。
