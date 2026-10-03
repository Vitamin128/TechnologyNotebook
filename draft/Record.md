# 学习进度

**声明与基本语义**

- [ ] `record` 的定位与基本声明
- [ ] 记录组件、访问器与自动生成成员

**构造与数据约束**

- [ ] 规范构造器与紧凑构造器
- [ ] 重载构造器与静态工厂
- [ ] 浅不可变与防御性复制

**对象行为与语言能力**

- [ ] `equals()`、`hashCode()` 与 `toString()`
- [ ] 成员方法、接口与继承限制
- [ ] 泛型、局部 `record` 与 Stream 配合

**Java / Spring 后端实践**

- [ ] 使用 `record` 定义 DTO 与 JSON 数据
- [ ] 使用 Bean Validation 校验 `record`
- [ ] `record`、普通类与 JPA 实体的选型

**进阶语法**

- [ ] Java 21：记录模式与解构

本文主体示例使用 Java 17+；最后的记录模式示例需要 Java 21。

Java 14、15 中的 `record` 属于预览特性，Java 16 起正式提供。Java 17 使用 `record` 不需要启用预览功能。

示例是独立片段，重复出现的类型声明不要同时放进同一个包。顶级 `public record` 按普通公共类的规则放在同名 `.java` 文件中。

---

# 一、声明与基本语义

## `record` 的定位与基本声明

- [ ] `record` 的定位与基本声明

### 一句话定义

记录类（Record Class）是 Java 用于表达一组固定数据的特殊类，通过 `record` 声明。

它让“这个对象由哪些数据组成”直接体现在类型声明中。

### 核心职责

简化数据载体的定义，减少构造器、访问器和对象比较方法的样板代码。

### 核心特征

- 是 Java 语言提供的类，不需要 Lombok。
- 仍然是引用类型，使用 `new` 创建对象。
- 默认按组件数据表达相等关系。
- 类隐式为 `final`，组件字段为 `private final`。

### 典型场景

- 接口请求与响应数据。
- 查询结果投影。
- 坐标、区间、组合键等值数据。
- 方法内部的临时计算结果。

### 示例

```java
public record UserSummary(long id, String name) {}
```

创建与使用：

```java
UserSummary user = new UserSummary(1L, "张三");

System.out.println(user.id());   // 1
System.out.println(user.name()); // 张三
```

### 如何识别

“这个类型主要是在表示一组数据，还是维护不断变化的状态和复杂生命周期？”

### 与相似类型的区别

- 普通 `class`：可以自由设计字段、继承关系和对象行为。
- `record`：围绕声明中的组件组织状态与默认对象行为。
- `enum`：表达一组预先定义的常量实例。

### 常见误区

- **`record` 是数据库中的一行记录。** 名称相似，但它是 Java 类型，与数据库没有自动映射关系。
- **`record` 不是真正的类。** 它仍有构造器、方法、类型信息，也能实现接口。
- **使用 `record` 就不再分配对象。** 它仍是普通引用对象，不等于无分配的值类型。

---

## 记录组件、访问器与自动生成成员

- [ ] 记录组件、访问器与自动生成成员

### 一句话定义

记录组件（Record Component）是 `record` 名称后括号中声明的数据项。

编译器根据组件生成字段、访问器、规范构造器及默认对象方法。

### 核心职责

让类型的数据结构与构造、读取方式保持一致。

### 核心特征

对于：

```java
public record UserSummary(long id, String name) {}
```

默认具有：

| 成员 | 对应行为 |
|---|---|
| `private final long id` | 保存 ID |
| `private final String name` | 保存姓名 |
| `public long id()` | 读取 ID |
| `public String name()` | 读取姓名 |
| `UserSummary(long id, String name)` | 按组件顺序构造 |
| `equals()`、`hashCode()` | 基于组件比较与计算哈希 |
| `toString()` | 展示类型、组件名及组件值 |

不会自动生成 setter、builder 或 `withXxx()` 方法。

### 典型场景

使用响应对象返回数据，在 Stream 中通过方法引用提取组件。

### 示例

```java
List<UserSummary> users = List.of(
    new UserSummary(1L, "张三"),
    new UserSummary(2L, "李四")
);

List<String> names = users.stream()
    .map(UserSummary::name)
    .toList();
```

需要导入 `java.util.List`。

### 如何识别

“读取这个属性的方法是 `name()`，还是 Java Bean 风格的 `getName()`？”

### 与相似类型的区别

- `record` 默认访问器：`name()`。
- 常见 Java Bean getter：`getName()`。
- `record` 字段默认私有，对其他普通类通常通过访问器读取。

### 常见误区

- **布尔组件自动生成 `isActive()`。** `record User(boolean active)` 默认生成 `active()`。
- **自动提供无参构造器。** 非空组件列表不会因此获得无参构造器。
- **任何只识别 getter/setter 的工具都能直接使用。** 需要确认工具是否支持 record 访问器和构造方式。

---

# 二、构造与数据约束

## 规范构造器与紧凑构造器

- [ ] 规范构造器与紧凑构造器

### 一句话定义

规范构造器（Canonical Constructor）的参数对应全部记录组件。

紧凑构造器（Compact Constructor）是省略参数列表的规范构造器写法，适合校验和规范化数据。

### 核心职责

让对象在创建完成时就满足必要的数据约束。

### 核心特征

- 不显式声明时，编译器生成规范构造器。
- 完整形式需要给组件字段赋值。
- 紧凑形式中，先处理参数，正常结束后编译器自动赋值。
- 紧凑构造器中不能直接给组件字段赋值，例如 `this.name = name`。
- 显式规范构造器的访问权限不能比 record 类型更严格。

### 典型场景

检查必填数据、限制数值范围、去掉名称首尾空白。

### 示例

```java
import java.util.Objects;

public record UserName(String value) {

    public UserName {
        value = Objects.requireNonNull(value, "名称不能为空").strip();

        if (value.isEmpty()) {
            throw new IllegalArgumentException("名称不能为空白");
        }
    }
}
```

使用：

```java
UserName name = new UserName("  张三  ");

System.out.println(name.value()); // 张三
```

完整规范构造器形式：

```java
public record Quantity(int value) {

    public Quantity(int value) {
        if (value < 0) {
            throw new IllegalArgumentException("数量不能为负数");
        }

        this.value = value;
    }
}
```

### 如何识别

“这个约束是否应该对所有创建该对象的调用方都成立？”

### 与相似类型的区别

- 完整规范构造器：显式参数列表，显式字段赋值。
- 紧凑构造器：隐式参数列表，自动字段赋值。
- 普通实例方法：对象已经创建后才执行，不能代替创建时约束。

### 常见误区

- **紧凑构造器是无参构造器。** 它的参数来自组件，只是省略了书写。
- **修改参数不会影响最终字段。** 自动赋值使用处理后的参数。
- **紧凑构造器里调用访问器就能得到最终值。** 字段尚未完成自动赋值，应直接使用参数。
- **构造器适合查数据库验证用户名唯一。** 这类依赖外部状态的业务检查通常放在服务层。

---

## 重载构造器与静态工厂

- [ ] 重载构造器与静态工厂

### 一句话定义

`record` 可以定义额外构造器和静态工厂，为调用方提供默认值或更清楚的创建入口。

### 核心职责

简化常见创建方式，同时让数据仍经过规范构造器处理。

### 核心特征

- 非规范构造器必须通过 `this(...)` 委托其他构造器。
- 构造器链最终到达规范构造器。
- 静态工厂是普通静态方法，可以使用表达业务含义的名称。
- 默认值和校验规则应集中维护。

### 典型场景

分页参数使用默认页码与页大小。

### 示例

```java
public record PageQuery(int page, int size) {

    public PageQuery {
        if (page < 1 || size < 1 || size > 100) {
            throw new IllegalArgumentException("分页参数不合法");
        }
    }

    public PageQuery() {
        this(1, 20);
    }

    public PageQuery(int page) {
        this(page, 20);
    }

    public static PageQuery firstPage(int size) {
        return new PageQuery(1, size);
    }
}
```

```java
PageQuery a = new PageQuery();          // 第 1 页，每页 20 条
PageQuery b = new PageQuery(3);         // 第 3 页，每页 20 条
PageQuery c = PageQuery.firstPage(50);  // 第 1 页，每页 50 条
```

### 如何识别

“是否存在反复使用的默认组合，或需要一个比 `new` 更明确的创建名称？”

### 与相似类型的区别

- 重载构造器：通过参数列表区分创建方式。
- 静态工厂：通过方法名表达含义。
- Builder：适合较多可选参数，但 record 不会自动生成 Builder。

### 常见误区

- **额外构造器可以绕开规范构造器直接赋值。** record 的构造器委托规则不允许这样设计。
- **JSON 反序列化一定使用自己添加的无参构造器。** record 通常采用组件构造方式，取决于框架支持与配置。

---

## 浅不可变与防御性复制

- [ ] 浅不可变与防御性复制

### 一句话定义

`record` 默认提供浅不可变：组件字段不能重新赋值，但字段引用的对象仍可能变化。

防御性复制（Defensive Copy）用于避免外部通过共享引用修改内部数据。

### 核心职责

保持数据对象的内容稳定，减少共享可变状态带来的问题。

### 核心特征

- `final` 限制的是字段重新赋值，不会自动冻结对象。
- `List`、数组、可变日期或自定义对象仍需单独处理。
- `List.copyOf()` 可以得到不受原列表后续结构修改影响的不可修改列表。
- 列表复制不等于复制列表中的每个元素。

### 典型场景

接口返回标签列表、缓存数据快照、事件载荷。

### 示例

```java
import java.util.ArrayList;
import java.util.List;

public record UserTags(List<String> values) {

    public UserTags {
        values = List.copyOf(values);
    }
}
```

使用：

```java
List<String> source = new ArrayList<>(List.of("Java"));

UserTags tags = new UserTags(source);

source.add("SQL");

System.out.println(tags.values()); // [Java]

// tags.values().add("Spring");
// 抛出 UnsupportedOperationException
```

这个例子的元素是不可变的 `String`。若元素本身是可变对象，仍需考虑元素状态。

### 如何识别

“调用方修改传入对象，或通过访问器修改返回对象，会不会改变这个 record 的内容？”

### 与相似类型的区别

- `List.copyOf(source)`：防止原列表后续结构变化影响结果。
- `Collections.unmodifiableList(source)`：只提供不可修改视图，原列表变化仍可能被看到。
- 深复制：还需要处理内部可变元素。

### 常见误区

- **所有 record 都天然线程安全。** 可变组件仍可能引入竞争。
- **`List.copyOf()` 支持 `null` 列表和 `null` 元素。** 两者都不允许。
- **数组组件会自动复制。** 不会；传入和返回数组的共享引用都需要评估。
- **字段是 `final`，就可以安全作为哈希键。** 还要确保参与相等与哈希计算的组件不会变化。

---

# 三、对象行为与语言能力

## `equals()`、`hashCode()` 与 `toString()`

- [ ] `equals()`、`hashCode()` 与 `toString()`

### 一句话定义

record 默认根据所有组件生成相等比较、哈希计算和文本展示方法。

默认相等关系要求是同一种 record 类型，且对应组件相等。

### 核心职责

让数据对象可以按内容比较，支持去重和键值查找。

### 核心特征

- `equals()` 默认涉及所有组件，不只比较 ID。
- 引用组件按自身相等规则比较。
- 相等对象具有相同哈希值，但哈希值相同不代表相等。
- `toString()` 默认包含组件名称和值，不应将其格式当成稳定协议。

### 典型场景

使用组合键、Stream 去重、测试中比较结果对象。

### 示例

```java
record UserSummary(long id, String name) {}

UserSummary a = new UserSummary(1L, "张三");
UserSummary b = new UserSummary(1L, "张三");
UserSummary c = new UserSummary(1L, "李四");

System.out.println(a.equals(b)); // true
System.out.println(a.equals(c)); // false
System.out.println(a == b);      // false

Set<UserSummary> values = new HashSet<>();
values.add(a);
values.add(b);

System.out.println(values.size()); // 1
```

需要导入 `java.util.Set` 和 `java.util.HashSet`。

### 如何识别

“两个对象是否应根据全部数据相等，还是只要业务 ID 相同就视为同一对象？”

### 与相似类型的区别

- `==`：比较对象引用。
- record 默认 `equals()`：比较类型与组件。
- 实体身份比较：可能只关注 ID，与 record 默认规则不同。

### 常见误区

- **ID 相同的 record 一定相等。** 其他组件不同，默认就不相等。
- **数组组件默认按内容比较。** 数组自身使用引用相等，因此内容相同的不同数组也可能导致 record 不相等。
- **自动 `toString()` 可以随便写入日志。** 密码、令牌等敏感组件可能被直接输出。
- **只改 `equals()` 不改 `hashCode()` 没关系。** 自定义时必须维护两者契约，并保留一致的数据语义。

---

## 成员方法、接口与继承限制

- [ ] 成员方法、接口与继承限制

### 一句话定义

record 可以包含方法、静态成员并实现接口，但实例状态由记录组件决定，且不能参与普通类继承扩展。

### 核心职责

在保持数据模型清楚的同时，提供与数据直接相关的行为。

### 核心特征

- 可以声明实例方法、静态方法和静态字段。
- 可以实现一个或多个接口。
- 不能添加额外实例字段或实例初始化块。
- 隐式继承 `java.lang.Record`，不能另写 `extends` 指定父类。
- 自身隐式为 `final`，不能被其他类继承。

### 典型场景

数据对象实现统一接口，提供简单的计算或格式化方法。

### 示例

```java
interface Named {
    String name();
}

public record Customer(String name) implements Named {

    public String displayName() {
        return "客户：" + name;
    }

    public static Customer anonymous() {
        return new Customer("匿名");
    }
}
```

自动生成的 `public String name()` 已满足接口要求。

### 如何识别

“这个方法是在解释或计算现有数据，还是需要新增独立状态和生命周期？”

### 与相似类型的区别

- 普通类：可以增加私有实例状态、继承父类。
- record：全部实例字段来自组件，但仍能通过接口表达行为契约。

### 常见误区

- **record 不能写任何业务方法。** 可以写，关键是职责是否适合数据模型。
- **组件之外再加一个实例缓存字段就行。** 额外实例字段不允许。
- **可以通过继承 record 增加一个字段。** 不能继承；可采用组合或另定义类型。

---

## 泛型、局部 `record` 与 Stream 配合

- [ ] 泛型、局部 `record` 与 Stream 配合

### 一句话定义

record 支持泛型，也可以声明在方法内部，为中间结果提供有名称、有类型的数据结构。

### 核心职责

替代缺少语义的数组、临时 `Map` 或多层二元组。

### 核心特征

- 泛型 record 可以包装不同数据类型。
- 局部 record 适合只在当前方法中使用的临时结果。
- 局部 record 隐式为静态类型，不捕获方法局部变量。
- 默认相等规则使它适合表达稳定的组合键。

### 典型场景

统一分页结果、Stream 中间计算、按多个字段分组。

### 示例

泛型结果：

```java
import java.util.List;

public record PageResult<T>(List<T> items, long total) {

    public PageResult {
        items = List.copyOf(items);

        if (total < 0) {
            throw new IllegalArgumentException("总数不能为负数");
        }
    }
}
```

局部组合键：

```java
record Employee(String department, boolean active) {}

public Map<?, Long> countEmployees(List<Employee> employees) {
    record GroupKey(String department, boolean active) {}

    return employees.stream()
        .collect(Collectors.groupingBy(
            employee -> new GroupKey(
                employee.department(),
                employee.active()
            ),
            Collectors.counting()
        ));
}
```

需要导入 `java.util.List`、`java.util.Map` 和 `java.util.stream.Collectors`。

这里返回 `Map<?, Long>` 是因为组合键只在方法内部定义。如果调用方需要访问键的具体组件，应将 `GroupKey` 提升为对外可见类型，并返回 `Map<GroupKey, Long>`。

### 如何识别

“这组临时数据是否需要一个明确类型？它只在方法内部使用，还是需要暴露给调用方？”

### 与相似类型的区别

- `Object[]`：依赖下标和强制转换。
- `Map<String, Object>`：字段含义靠字符串约定。
- record：字段名称与类型由编译器检查。

### 常见误区

- **局部 record 可以像 Lambda 一样捕获外部变量。** 需要将相关值作为组件传入。
- **任意 record 都适合作为分组键。** 键中参与相等与哈希计算的数据应保持稳定。
- **内部临时类型适合直接充当公共 API。** 公共 API 应提供调用方能使用的明确类型。

---

# 四、Java / Spring 后端实践

## 使用 `record` 定义 DTO 与 JSON 数据

- [ ] 使用 `record` 定义 DTO 与 JSON 数据

### 一句话定义

record 可以表示数据传输对象（Data Transfer Object），简称 DTO，用于接口数据和应用层之间的数据传递。

JSON 序列化与反序列化由框架完成，不是 record 自带的语言功能。

### 核心职责

明确接口字段，减少 DTO 中的样板代码。

### 核心特征

- 适合字段确定、创建后通常不再修改的数据。
- 支持 record 的 JSON 框架可以根据组件和规范构造器处理数据。
- Jackson 2.12 引入 record 支持；具体项目仍需核对版本和配置。
- 以下示例使用 Jackson 2.x 的 `com.fasterxml.jackson` 包名。

### 典型场景

用户摘要响应、创建请求、服务之间的结果对象。

### 示例

```java
public record UserResponse(long id, String name) {}
```

下面的片段放在声明了 `throws Exception` 的演示方法中：

```java
import com.fasterxml.jackson.databind.ObjectMapper;

ObjectMapper mapper = new ObjectMapper();

UserResponse response = new UserResponse(1L, "张三");

String json = mapper.writeValueAsString(response);

UserResponse restored = mapper.readValue(
    "{\"id\":1,\"name\":\"张三\"}",
    UserResponse.class
);
```

在默认命名配置下，JSON 对应 `id` 与 `name` 两个属性。

### 如何识别

“这个对象是否在表达接口数据，而不需要依赖 setter 逐步组装？”

### 与相似类型的区别

- DTO：传递数据。
- JPA 实体：参与持久化状态管理。
- record：一种实现类型，既可以用于 DTO，也可以用于其他数据模型。

### 常见误区

- **record 本身会生成 JSON。** 需要 JSON 库。
- **没有无参构造器就不能反序列化。** 支持 record 的库可以使用规范构造器。
- **Java Bean getter 与 record 访问器完全相同。** 旧工具可能不识别 `name()`。
- **字段随意增加不会影响调用方。** 增加组件会改变构造器签名，也可能改变 JSON 与相等语义。

---

## 使用 Bean Validation 校验 `record`

- [ ] 使用 Bean Validation 校验 `record`

### 一句话定义

Bean 校验（Bean Validation）通过约束注解检查对象数据，record 组件也可以声明这些约束。

校验是否执行，取决于是否调用校验器或进入框架提供的校验流程。

### 核心职责

在接口边界检查必填、格式和范围，并提供一致的错误处理。

### 核心特征

- 可在组件上使用 `@NotBlank`、`@NotNull`、`@Min` 等约束。
- Spring MVC 常用 `@Valid @RequestBody` 触发请求对象校验。
- 注解传播到哪些成员，取决于注解的 `@Target`。
- 普通 `new` 不会仅因存在校验注解就自动执行 Bean Validation。

### 典型场景

检查创建用户请求中的邮箱和年龄。

### 示例

以下使用 Spring 6+ 的 `jakarta.validation` 命名空间，并假设项目已有 Bean Validation 实现。

```java
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;

public record CreateUserRequest(
    @NotBlank @Email String email,
    @NotNull @Min(18) Integer age
) {}
```

控制器示例只展示绑定与校验：

```java
import jakarta.validation.Valid;
import org.springframework.web.bind.annotation.*;

@RestController
public class UserController {

    @PostMapping("/users/validate")
    public String validate(
        @Valid @RequestBody CreateUserRequest request
    ) {
        return "校验通过";
    }
}
```

### 如何识别

“这个约束需要保证对象永远有效，还是主要用于接口输入校验？实际触发校验的位置在哪里？”

### 与相似类型的区别

- 紧凑构造器校验：每次构造都会执行，可以规范化参数。
- Bean Validation：在校验流程中检查注解，支持分组与统一错误信息。
- 服务层检查：处理唯一性、权限等依赖业务或外部状态的约束。

### 常见误区

- **加 `@NotNull` 后，`new` 时传入 `null` 就必然报错。** 没有主动校验或构造器检查时不会。
- **`@Email` 同时保证非空。** 必填应额外声明相应约束。
- **基本类型 `int` 适合表达必填年龄。** 使用 `Integer` 更容易区分缺失值与数值零。
- **所有构造器异常都会变成统一的字段校验错误。** 构造失败与 Bean Validation 失败属于不同阶段。

---

## `record`、普通类与 JPA 实体的选型

- [ ] `record`、普通类与 JPA 实体的选型

### 一句话定义

是否采用 record，应看对象的数据语义、可变性和框架要求，而不是只看它能省多少代码。

### 核心职责

在简洁的数据建模与必要的状态管理之间做合适选择。

### 核心特征

| 判断点 | 倾向使用 record | 倾向使用普通类 |
|---|---|---|
| 主要职责 | 表达一组确定的数据 | 管理状态或复杂行为 |
| 修改方式 | 创建新的结果对象 | 在原对象上改变状态 |
| 相等关系 | 通常由全部组件决定 | 可能按 ID 或其他规则定义 |
| 继承需求 | 实现接口即可 | 需要类继承 |
| 框架创建方式 | 支持组件构造 | 要求特定实例化或代理机制 |

标准 JPA 实体不能声明为 record。Jakarta Persistence 3.2 明确允许 record 用于嵌入类型等场景，但这不代表允许 record 作为实体。

### 典型场景

- `UserResponse`：适合 record。
- 查询汇总结果：适合 record。
- 需要 JPA 管理的 `UserEntity`：使用满足实体要求的普通类。

### 示例

用创建新对象表达修改：

```java
public record UserProfile(long id, String name) {

    public UserProfile withName(String newName) {
        return new UserProfile(id, newName);
    }
}
```

```java
UserProfile original = new UserProfile(1L, "张三");
UserProfile renamed = original.withName("李四");

System.out.println(original.name()); // 张三
System.out.println(renamed.name());  // 李四
```

`withName()` 是手动编写的方法，不是编译器自动生成的。

### 如何识别

“修改时是否应该产生一个新值？框架是否要求这个类型承担可变实体或代理对象的角色？”

### 与相似类型的区别

- record 数据值：关注当前数据内容。
- JPA 实体：关注身份、持久化状态及生命周期。
- 普通不可变类：也能表达值，但可以更自由地隐藏内部表示。

### 常见误区

- **所有只有字段的类都应该改成 record。** 框架要求和相等语义同样重要。
- **给 record 加 `@Entity` 就能成为标准 JPA 实体。** 不符合实体类型要求。
- **record 不能用于任何持久化相关场景。** 查询投影、DTO 和受支持的嵌入类型是不同场景。
- **换成 record 不影响原有调用代码。** getter 名称、构造方式、继承关系及相等规则都可能变化。

---

# 五、进阶语法

## Java 21：记录模式与解构

- [ ] Java 21：记录模式与解构

### 一句话定义

记录模式（Record Pattern）在类型匹配成功时，将 record 的组件直接提取到局部变量。

下面的解构语法使用 Java 21，不属于本文 Java 17 基础示例。

### 核心职责

减少类型判断后的访问器调用，使结构化数据匹配更直接。

### 核心特征

- 可以配合 `instanceof` 使用。
- 按组件顺序进行匹配和提取。
- 可以使用 `var` 推断组件变量类型。
- 支持嵌套记录模式。
- `null` 不匹配 record 模式。

### 典型场景

处理多种事件或结果类型，并读取匹配对象的数据。

### 示例

```java
record Point(int x, int y) {}

Object value = new Point(3, 4);

// Java 21
if (value instanceof Point(int x, int y)) {
    System.out.println(x + y); // 7
}
```

Java 17 可以使用类型模式，再调用访问器：

```java
if (value instanceof Point point) {
    System.out.println(point.x() + point.y()); // 7
}
```

### 如何识别

“我是否在类型判断成功后，立刻提取这个 record 的多个组件？”

### 与相似类型的区别

- 类型模式：得到整个对象变量，例如 `Point point`。
- 记录模式：直接得到组件变量，例如 `Point(int x, int y)`。

### 常见误区

- **Java 17 支持 record，所以也支持上述解构写法。** record 声明与记录模式是不同的语言功能。
- **解构会修改或拆毁原对象。** 它只匹配并读取组件。
- **组件可以随意换顺序。** 匹配结构对应 record 的组件声明顺序。