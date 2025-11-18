# 面向对象设计（OOD）核心知识手册
[TOC]

# SOLID 设计原则
SOLID 是面向对象设计中5个核心原则的缩写，遵循这些原则能显著提升代码的可读性、可维护性和扩展性，是构建高质量软件系统的基础。

## 单一责任原则（Single Responsibility Principle）

### 核心定义
一个类应该只有一个引起它变化的原因，即一个类只负责一项核心职责。

### 通俗理解
就像一家公司里的岗位分工：财务只负责记账报税，程序员只负责写代码，若让财务同时兼顾编程工作，不仅效率低下，还容易出现错误。在代码中，若一个类同时处理用户数据存储、界面展示、逻辑计算等多个功能，当其中任一功能需要修改时，都可能影响其他功能，增加维护风险。

### 实践案例
- **反例**：一个 `UserManager` 类同时包含用户信息存储（操作数据库）、密码加密、用户登录验证、发送欢迎邮件等功能。当邮件发送规则改变时，修改该类可能会影响用户登录逻辑。
- **正例**：将功能拆分为 `UserRepository`（数据存储）、`PasswordEncoder`（密码加密）、`LoginValidator`（登录验证）、`EmailService`（邮件发送）四个类，每个类专注于单一职责。

### 核心价值
- 降低代码复杂度，提高可读性。
- 减少功能修改时的连锁反应，提升可维护性。
- 便于单元测试，每个类的测试场景更单一。

## 开放封闭原则（Open Close Principle）
### 核心定义
软件实体（类、模块、函数等）应该对扩展开放，对修改关闭。即当需求变化时，应通过新增代码实现功能扩展，而非修改原有稳定代码。

### 通俗理解
好比手机的配件生态：手机本身的硬件设计（原有代码）固定，但支持通过安装新APP（扩展代码）实现新功能，或连接蓝牙耳机、移动电源（新增组件）扩展使用场景，无需拆开手机修改内部结构。

### 实践案例
- **反例**：一个计算商品折扣的 `DiscountCalculator` 类，最初支持“满减折扣”，当需要新增“会员折扣”时，直接修改 `calculate` 方法，在其中添加会员折扣逻辑，导致原有代码被修改，可能引入bug。
- **正例**：定义 `Discount` 接口，实现 `FullReductionDiscount`（满减）、`MemberDiscount`（会员）等实现类，`DiscountCalculator` 依赖 `Discount` 接口进行计算。新增折扣类型时，只需新增接口实现类，无需修改计算器原有代码。

### 核心价值
- 保护原有稳定代码，降低修改风险。
- 提高代码扩展性，适应需求快速变化。
- 符合“增量开发”理念，便于团队协作。

## 里氏替换原则（Liskov Substitution Principle）
### 核心定义
如果对每一个类型为 `S` 的对象 `o1`，都存在类型为 `T` 的对象 `o2`，使得以 `T` 定义的所有程序 `P` 在所有用 `o1` 替换 `o2` 后，程序 `P` 的行为没有变化，那么类型 `S` 是类型 `T` 的子类型。简单来说，子类对象可以无缝替换父类对象，且不影响程序的正确性。

### 通俗理解
就像现实中的“子类”物品：苹果是水果的子类，若程序原本需要“水果”（父类）来制作果汁，用“苹果”（子类）替换后，依然能成功制作果汁，且不会出现逻辑错误。但如果子类违背父类的行为约定（如用“辣椒”替换“水果”），则会导致程序异常。

### 实践案例
- **反例**：父类 `Bird` 有 `fly` 方法，子类 `Ostrich`（鸵鸟）继承 `Bird` 后，重写 `fly` 方法时抛出“无法飞行”异常。当程序用 `Ostrich` 替换 `Bird` 调用 `fly` 方法时，会导致程序崩溃，违背里氏替换原则。
- **正例**：拆分父类为 `Bird`（基础鸟类）和 `FlyingBird`（会飞的鸟类），`Ostrich` 继承 `Bird`，`Sparrow`（麻雀）继承 `FlyingBird`。此时用 `Sparrow` 替换 `FlyingBird` 调用 `fly` 方法，行为保持一致。

### 核心价值
- 保证继承体系的合理性，避免子类破坏父类的行为约定。
- 提高代码的复用性和可扩展性，支持多态的安全使用。
- 降低程序的维护成本，减少因子类替换导致的bug。

## 接口分离原则（Interface Segregation Principle）
### 核心定义
客户端不应该依赖它不需要的接口，即一个大的接口应拆分为多个小的、专用的接口，让客户端只依赖自己需要的接口。

### 通俗理解
如同餐厅的菜单设计：若将所有菜品（包括主食、菜品、甜品、饮料）都放在一个菜单上，顾客点餐时需要在大量无关菜品中寻找目标；而拆分为主食菜单、菜品菜单、甜品菜单后，顾客可根据需求选择对应菜单，提高点餐效率。在代码中，过大的接口会迫使客户端实现不需要的方法，增加冗余代码。

### 实践案例
- **反例**：定义一个 `UserOperation` 接口，包含 `login`、`register`、`deleteUser`、`exportData` 等方法。普通用户客户端只需 `login` 和 `register` 方法，却被迫依赖整个接口，且可能因误实现 `deleteUser` 等危险方法导致安全问题。
- **正例**：将接口拆分为 `UserAuthInterface`（`login`、`register`）、`UserAdminInterface`（`deleteUser`）、`DataExportInterface`（`exportData`），不同客户端根据权限依赖对应接口。

### 核心价值
- 减少接口冗余，降低客户端与接口的耦合度。
- 提高接口的灵活性和可维护性，修改一个接口不会影响其他接口的客户端。
- 避免客户端实现不需要的方法，提升代码的安全性和简洁性。

## 依赖反转原则（Dependency Inversion Principle）
### 核心定义
高层模块不应该依赖低层模块，两者都应该依赖抽象；抽象不应该依赖细节，细节应该依赖抽象。简单来说，应面向接口编程，而非面向具体实现编程。

### 通俗理解
好比建筑施工中的“图纸依赖”：施工队（高层模块）不需要依赖具体的砖块、水泥供应商（低层模块），而是依赖建筑图纸（抽象接口）；供应商只需按照图纸标准提供材料（细节依赖抽象）。这样更换供应商时，无需修改施工队的施工逻辑。

### 实践案例
- **反例**：高层模块 `OrderService` 直接依赖低层模块 `MySQLRepository` 进行订单存储。当需要将数据库改为 PostgreSQL 时，必须修改 `OrderService` 的代码，耦合度极高。
- **正例**：定义 `OrderRepository` 抽象接口，`MySQLRepository` 和 `PostgreSQLRepository` 实现该接口。`OrderService` 依赖 `OrderRepository` 接口，通过依赖注入的方式使用具体实现。更换数据库时，只需新增对应接口实现类，无需修改高层模块代码。

### 核心价值
- 降低高层模块与低层模块的耦合度，提高系统的灵活性。
- 便于替换具体实现，适应不同的业务场景（如数据库切换、第三方服务替换）。
- 促进代码的模块化设计，提升系统的可扩展性和可维护性。

## 迪米特法则（Law of Demeter）
### 核心定义
一个对象应该对其他对象保持最少的了解，也称为“最少知识原则”。即对象之间的交互应仅限于直接关联的对象，避免通过中间对象访问多层嵌套的对象。

### 通俗理解
就像日常生活中的“办事流程”：你想给朋友寄礼物，只需将礼物交给快递公司（直接关联对象），无需了解快递公司的分拣中心、运输车队、派送员等内部细节。在代码中，若一个对象通过多层调用访问其他对象的方法，会导致对象间耦合度升高，且当中间对象结构变化时，会影响所有依赖它的对象。

### 实践案例
- **反例**：
  ```java
  // 汽车类
  class Car {
      private FuelTank fuelTank = new FuelTank();
      public FuelTank getFuelTank() {
          return fuelTank;
      }
  }
  // 油箱类
  class FuelTank {
      public void addOil() {
          System.out.println("添加燃油");
      }
  }
  // 客户端代码：通过Car获取FuelTank，再调用addOil方法（违背迪米特法则）
  public class Client {
      public static void main(String[] args) {
          Car car = new Car();
          car.getFuelTank().addOil(); // 客户端了解Car和FuelTank的内部关联
      }
  }
  ```
- **正例**：
  ```java
  // 汽车类：封装加油逻辑，对外提供addOil方法
  class Car {
      private FuelTank fuelTank = new FuelTank();
      public void addOil() {
          fuelTank.addOil(); // 内部调用，客户端无需感知FuelTank的存在
      }
  }
  // 油箱类
  class FuelTank {
      public void addOil() {
          System.out.println("添加燃油");
      }
  }
  // 客户端代码：直接调用Car的addOil方法（符合迪米特法则）
  public class Client {
      public static void main(String[] args) {
          Car car = new Car();
          car.addOil();
      }
  }
  ```

### 核心价值
- 降低对象间的耦合度，减少代码的依赖关系。
- 提高代码的可维护性，当内部对象结构变化时，只需修改封装类，不影响客户端。
- 简化代码逻辑，让对象的职责更清晰。

# UML 符号基础
在面向对象设计中，UML（统一建模语言）是常用的可视化工具，以下是类图中最基础的访问修饰符符号：

| 符号 | 含义       | 说明                                   |
|------|------------|----------------------------------------|
| `+`  | Public（公有） | 类外部可直接访问该成员（属性或方法）   |
| `-`  | Private（私有）| 仅类内部可访问，外部无法直接操作       |
| `#`  | Protected（受保护）| 类内部及子类可访问，外部不可直接访问 |

### 示例
```uml
class Person {
  - name: String  // 私有属性：姓名
  + age: int      // 公有属性：年龄
  # gender: String // 受保护属性：性别
  + eat(): void   // 公有方法：吃饭
  - sleep(): void // 私有方法：睡觉
  # work(): void  // 受保护方法：工作
}
```

# 面向对象设计（OOD）三大核心特性
## 封装
### 核心定义
将对象的属性和方法封装在类内部，通过访问修饰符控制外部对内部成员的访问权限，仅对外提供有限的公共接口。

### 通俗理解
好比一个智能手机：内部的芯片、电池、主板等硬件（属性和私有方法）被封装在机身内部，用户无需了解其工作原理，只需通过屏幕、按键等公共接口（如打电话、发消息）操作手机，同时避免了误操作对内部硬件的损坏。

### 核心价值
- **隐藏实现细节**：外部无需关注对象内部的工作机制，降低使用复杂度。
- **提高安全性**：防止外部随意修改对象的核心属性，保证数据的完整性和一致性。
- **便于维护**：内部实现逻辑修改时，只要公共接口不变，外部代码无需调整。

### 实践案例
```java
class BankAccount {
  private double balance; // 私有属性：账户余额，外部无法直接修改

  // 公有方法：存款（对外提供的合法接口）
  public void deposit(double amount) {
      if (amount > 0) {
          balance += amount;
      } else {
          throw new IllegalArgumentException("存款金额必须为正数");
      }
  }

  // 公有方法：查询余额（对外提供的合法接口）
  public double getBalance() {
      return balance;
  }
}
```

## 继承
### 核心定义
继承是类与类之间的关系，子类通过继承父类，可直接复用父类的属性和方法，同时可新增自己的属性和方法，或重写父类的方法。继承的核心关系是“is a”（是一种）。

### 通俗理解
如同现实中的父子关系：儿子会继承父亲的一些特征（如外貌、性格），同时也会有自己的独特特征。在代码中，`Dog` 类继承 `Animal` 类，`Dog` 是一种 `Animal`，可复用 `Animal` 的 `eat`、`sleep` 方法，同时新增 `bark` 方法。

### 潜在问题
- **强耦合**：父类与子类紧密关联，父类的任何修改（如属性名变更、方法逻辑调整）都可能导致子类出现错误，维护成本高。
- **职责冗余**：子类可能继承父类中不需要的属性和方法，违背单一责任原则。
- **灵活性差**：继承是单根继承（多数面向对象语言），子类无法同时继承多个父类，限制了代码的扩展性。

### 实践建议
- 避免过度使用继承，优先考虑组合（“has a”关系）实现代码复用。
- 父类应设计为抽象类或接口，定义通用规范，避免包含具体的业务逻辑。
- 子类重写父类方法时，应遵循里氏替换原则，确保行为一致性。

### 示例
```java
// 父类：动物
abstract class Animal {
  public void eat() {
      System.out.println("动物进食");
  }

  public abstract void makeSound(); // 抽象方法：发出声音
}

// 子类：狗（继承动物类）
class Dog extends Animal {
  @Override
  public void makeSound() {
      System.out.println("汪汪汪");
  }

  // 子类新增方法
  public void fetch() {
      System.out.println("狗接飞盘");
  }
}
```

## 多态
### 核心定义
多态是指同一操作作用于不同的对象，会产生不同的执行结果。在面向对象中，多态通常通过接口实现、继承并重写方法来实现。

### 通俗理解
好比“打招呼”这个行为：中国人可能说“你好”，美国人可能说“Hello”，不同的人（对象）执行相同的行为（打招呼），呈现出不同的结果。在代码中，调用同一个接口方法，不同的实现类会执行不同的逻辑。

### 实现方式
1. **接口多态**：不同类实现同一个接口，重写接口方法。
2. **继承多态**：子类继承父类，重写父类的方法。
3. **抽象类多态**：子类继承抽象类，实现抽象方法。

### 核心价值
- **提高代码灵活性**：客户端可通过统一的接口操作不同的对象，无需关注具体实现。
- **便于扩展**：新增功能时，只需新增接口实现类，无需修改原有客户端代码，符合开放封闭原则。
- **简化代码逻辑**：减少条件判断语句，通过多态自动匹配对应的实现。

### 实践案例
```java
// 接口：形状
interface Shape {
  double calculateArea(); // 计算面积
}

// 实现类：圆形
class Circle implements Shape {
  private double radius;

  public Circle(double radius) {
      this.radius = radius;
  }

  @Override
  public double calculateArea() {
      return Math.PI * radius * radius;
  }
}

// 实现类：矩形
class Rectangle implements Shape {
  private double width;
  private double height;

  public Rectangle(double width, double height) {
      this.width = width;
      this.height = height;
  }

  @Override
  public double calculateArea() {
      return width * height;
  }
}

// 客户端代码：多态调用
public class Client {
  public static void main(String[] args) {
      Shape circle = new Circle(5);
      Shape rectangle = new Rectangle(3, 4);

      System.out.println("圆形面积：" + circle.calculateArea()); // 输出：78.5398...
      System.out.println("矩形面积：" + rectangle.calculateArea()); // 输出：12.0
  }
}
```





# 关联，聚合，组合的关系

a. 关联和聚合从代码上看很像

b. 关联就是“利用工具”的关系，例如，user 利用 order

c. 聚合是“包含”关系，例如，电脑包含鼠标

d. 关联聚合，都是只持有指针，不负责对象生命周期，不负责new对象，对象由外部传入。

e. 组合则自己在构造函数里面会new对象

#### 1. 关联（Association）代码示例：用户与订单
```cpp
class Order { /* 订单类，独立存在 */ };

class User {
private:
    // 命名体现“关联的对象”（订单是用户的业务关联对象，非“部分”）
    vector<Order*> userOrders; 

public:
    // 订单来自外部（可能是用户创建，也可能是系统分配，无“包含”约束）
    void addOrder(Order* order) {
        userOrders.push_back(order);
    }

    // 核心逻辑：用户与订单的“交互”（如下单、支付）
    void payOrder(Order* order) { /* ... */ }
};
```

**代码语义**：`User`和`Order`是业务上的关联（用户操作订单），`userOrders`仅表示 “用户相关的订单”，不隐含 “订单是用户的一部分”。

#### 2. 聚合（Aggregation）代码示例：电脑与鼠标
```cpp
class Mouse { /* 鼠标类，可独立存在 */ };

class Computer {
private:
    // 命名体现“整体的部分”（鼠标是电脑的组成部分）
    Mouse* attachedMouse; 

public:
    // 鼠标来自外部，但语义上“属于”电脑（如“电脑附带的鼠标”）
    Computer(Mouse* mouse) : attachedMouse(mouse) {}

    // 核心逻辑：整体对部分的“使用”（如电脑使用鼠标输入）
    void useMouse() { /* ... */ }
};
```

**代码语义**：`Computer`和`Mouse`是 “整体 - 部分” 关系，`attachedMouse`明确表示 “电脑包含的鼠标”，隐含 “鼠标是电脑的一部分”（尽管可分离）。

#### 3. 组合（Composition）：“整体由部分构成”（部分不可独立）

`Car` ◆———— `Engine`（实心菱形在`Car`端，表示汽车 “由发动机构成”）

```cpp
#include <string>
using namespace std;

// 发动机类（部分）：不可独立存在（无汽车时发动机无意义）
class Engine {
private:
    int horsepower;
public:
    Engine(int hp) : horsepower(hp) {}
};

// 汽车类（整体）：与发动机是组合关系
class Car {
private:
    string brand;
    Engine* engine; // 包含发动机，且负责发动机的生命周期
public:
    // 整体创建时，内部创建部分（发动机不可外部传入）
    Car(string b, int hp) : brand(b) {
        engine = new Engine(hp); // 部分由整体创建
    }
    // 整体销毁时，自动销毁部分（生命周期绑定）
    ~Car() {
        delete engine; // 必须删除发动机，避免内存泄漏
    }
};

// 测试：发动机无法独立创建，随汽车创建/销毁
int main() {
    Car bmw("宝马", 340); // 创建汽车时，内部自动创建发动机
    // Engine* eng = new Engine(200); // 不允许：发动机不可独立存在
    return 0; // 函数结束时，bmw析构，发动机也被销毁
}
```

