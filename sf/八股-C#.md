C# 是微软推出的面向对象编程语言，广泛应用于 Windows 开发、.NET 平台、游戏开发（Unity）等领域。面试中常围绕其核心特性、面向对象概念、.NET 框架、内存管理、异步编程等展开。以下是最常见的面试题及解析：

### 一、基础概念与语法

#### 1. C# 与 .NET 的关系是什么？

- **解释**：
    - **.NET** 是一个跨平台的开发框架，包含运行时（CLR）、类库（FCL）、编译器等，支持多种语言（C#、[VB.NET](https://vb.net/)、F# 等）。
    - **C#** 是专门为 .NET 框架设计的编程语言，依赖 .NET 的 CLR（公共语言运行时）执行，编译后生成中间语言（IL），运行时由 CLR 即时编译（JIT）为机器码。
- **核心关系**：C# 是 .NET 生态的主流语言，.NET 为 C# 提供运行环境和基础库。

#### 2. 值类型与引用类型的区别？各自包含哪些常见类型？

| 类型         | 存储位置    | 赋值 / 传递方式      | 常见类型                                                     |
| ------------ | ----------- | -------------------- | ------------------------------------------------------------ |
| **值类型**   | 栈（Stack） | 复制值本身           | 基本类型（int、float、bool、char）、结构体（struct）、枚举（enum）、Nullable 类型 |
| **引用类型** | 堆（Heap）  | 复制引用（内存地址） | 类（class）、接口（interface）、数组、字符串（string，特殊引用类型，不可变）、委托（delegate） |

- **关键区别**：

    - 值类型存储数据本身，赋值时创建副本，修改副本不影响原数据；
    - 引用类型存储指向堆中数据的引用，赋值时传递引用，修改副本会影响原数据。

- **示例**：

    ```csharp
    int a = 10; int b = a; b = 20; // a 仍为 10（值类型）
    string s1 = "hello"; string s2 = s1; s2 = "world"; // s1 仍为 "hello"（string 不可变，本质是创建新对象）
    List<int> list1 = new List<int>(); List<int> list2 = list1; list2.Add(1); // list1 也包含 1（引用类型）
    ```

#### 3. `string` 与 `StringBuilder` 的区别？何时使用 `StringBuilder`？

- **`string`**：

    - 不可变（Immutable）：每次修改（如拼接、替换）都会创建新的字符串对象，原对象不变，频繁操作会产生大量临时对象，浪费内存和性能。

- **`StringBuilder`**：

    - 可变（Mutable）：内部维护一个字符数组，修改时直接操作数组，不会创建新对象，适合频繁拼接、修改字符串的场景（如循环拼接）。

- **使用场景**：

    - 少量字符串操作（如固定拼接）用 `string`；
    - 大量循环拼接（如日志生成、动态 SQL 构建）用 `StringBuilder`。

- **示例**：

    ```csharp
    // 低效：每次 + 都创建新对象
    string s = "";
    for (int i = 0; i < 1000; i++) s += i;
    
    // 高效：仅操作内部数组
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < 1000; i++) sb.Append(i);
    string result = sb.ToString();
    ```

### 二、面向对象与特性

#### 4. C# 中的封装、继承、多态如何实现？

- **封装**：通过访问修饰符（`public`、`private`、`protected`、`internal`）隐藏类的内部实现，仅暴露必要接口。例：`private int _age;` 隐藏字段，通过 `public int Age { get { return _age; } }` 暴露访问。
- **继承**：用 `:` 关键字实现类的继承，子类复用父类的属性和方法，且可重写（`override`）父类方法。注意：C# 是单继承（一个类只能继承一个父类），但可实现多个接口。例：`public class Dog : Animal { ... }`
- **多态**：同一操作作用于不同对象产生不同结果，通过 “重写（`override`）” 和 “接口实现” 实现。例：父类 `Animal` 有 `Say()` 方法，子类 `Dog` 重写为 “汪汪”，`Cat` 重写为 “喵喵”，调用时根据对象实际类型执行对应逻辑。

#### 5. `abstract` 与 `interface` 的区别？

| 特性        | `abstract`（抽象类）                                | `interface`（接口）                                  |
| ----------- | --------------------------------------------------- | ---------------------------------------------------- |
| 方法实现    | 可包含抽象方法（无实现）和具体方法（有实现）        | 所有方法默认是抽象的（C# 8.0+ 可加默认实现）         |
| 字段 / 属性 | 可包含字段、属性（有状态）                          | 不能包含字段，属性默认是抽象的（无状态）             |
| 继承限制    | 类只能单继承抽象类                                  | 类可实现多个接口                                     |
| 访问修饰符  | 方法可设 `public`、`protected` 等                   | 方法默认 `public`，不能加其他修饰符                  |
| 设计目的    | 表示 “is-a” 关系（如 `Dog : Animal`），用于代码复用 | 表示 “can-do” 关系（如 `Dog : ISwim`），用于规范行为 |

- **使用场景**：
    - 抽象类：当多个类共享核心属性和方法时（如 `Animal` 作为 `Dog`、`Cat` 的父类）；
    - 接口：当需要规范不同类的行为但不共享实现时（如 `ISwim` 接口，`Dog` 和 `Fish` 都可实现）。

#### 6. `virtual`、`override`、`new` 关键字的区别？

- **`virtual`**：修饰父类方法，表示该方法可被子类重写。

- **`override`**：子类中用于重写父类的 `virtual` 方法，调用时会执行子类实现（多态）。

- **`new`**：子类中用于隐藏父类方法（非重写），调用时根据变量声明类型执行对应版本（不体现多态）。

- **示例**：

    ```csharp
    public class Parent {
        public virtual void Print() => Console.WriteLine("Parent");
    }
    public class Child1 : Parent {
        public override void Print() => Console.WriteLine("Child1 Override"); // 重写
    }
    public class Child2 : Parent {
        public new void Print() => Console.WriteLine("Child2 New"); // 隐藏
    }
    
    // 调用
    Parent p1 = new Child1(); p1.Print(); // 输出 "Child1 Override"（多态）
    Parent p2 = new Child2(); p2.Print(); // 输出 "Parent"（按声明类型执行）
    Child2 c2 = new Child2(); c2.Print(); // 输出 "Child2 New"（按实际类型执行）
    ```

```c#
public class Parent
{
    public void Print() // 父类方法（无 virtual）
    {
        Console.WriteLine("Parent's Print");
    }
}

public class Child : Parent
{
    public new void Print() // 用 new 隐藏父类方法
    {
        Console.WriteLine("Child's Print (new)");
    }
}

// 调用代码
Parent parent = new Parent();
parent.Print(); // 输出：Parent's Print

Child child = new Child();
child.Print(); // 输出：Child's Print (new) （调用子类自己的方法）

Parent parentAsChild = new Child(); // 父类变量指向子类对象
parentAsChild.Print(); // 输出：Parent's Print （注意：这里调用的是父类方法！）
```

new不是overwrite父类方法，看最后一个例子，运行基类指针的函数的时候调用的是父类的函数，如果是overwrite会运行子类的重写的方法

### 三、.NET 框架与内存管理

#### 7. 什么是 CLR？其主要功能是什么？

- **CLR（Common Language Runtime，公共语言运行时）** 是 .NET 框架的核心，负责管理 C# 等 .NET 语言编译后的代码执行。
- **主要功能**：
    - **内存管理**：通过垃圾回收（GC）自动释放不再使用的内存，避免内存泄漏；
    - **代码编译**：将中间语言（IL）即时编译（JIT）为机器码；
    - **类型安全检查**：确保代码只能访问被授权的内存区域；
    - **异常处理**：提供统一的异常处理机制；
    - **线程管理**：简化多线程编程。

#### 8. 什么是垃圾回收（GC）？GC 如何工作？

- **垃圾回收（GC）**：CLR 提供的自动内存管理机制，用于回收不再被引用的对象所占用的堆内存，无需手动释放（区别于 C++ 的 `delete`）。
- **工作原理**：
    1. **标记**：GC 暂停所有线程，遍历所有对象，标记仍被引用的 “存活对象”；
    2. **清除**：回收未被标记（无引用）的对象内存；
    3. **压缩**：将存活对象移动到堆的连续区域，减少内存碎片（仅在 “第 2 代” 回收时执行）。
- **代（Generation）**：GC 将对象分为 0/1/2 代，新对象为 0 代，回收频率最高；存活久的对象晋升为 1 代、2 代，回收频率低，优化性能。

.NET 的垃圾回收（GC）是由 CLR（公共语言运行时）自动管理内存的机制，核心目标是**自动识别并释放不再被使用的对象所占用的堆内存**，无需开发者手动调用 `free` 或 `delete`。但这并不意味着开发者可以完全 “不考虑内存释放”—— 理解 GC 的工作原理，能帮助我们写出更高效、更少内存问题的代码。

GC 只回收**不可达对象**（即没有任何变量引用的对象），打开的文件，网络句柄，还需要手动

#### 9. `IDisposable` 接口的作用是什么？如何正确实现？

- **作用**：用于释放非托管资源（如文件句柄、数据库连接、网络套接字等，这些资源 GC 无法自动回收）。

- **实现方式**：

    - 实现 `IDisposable` 接口的 `Dispose()` 方法，在其中释放非托管资源，并调用 `GC.SuppressFinalize(this)` 告知 GC 无需执行终结器；
    - 可选：定义终结器（`~类名()`），作为 `Dispose()` 未被调用时的 “保底” 释放机制。

- **使用场景**：配合 `using` 语句自动调用 `Dispose()`，确保资源释放。

- **示例**：

    ```csharp
    public class FileHandler : IDisposable {
        private FileStream _stream;
        private bool _disposed = false;
    
        public FileHandler(string path) => _stream = new FileStream(path, FileMode.Open);
    
        public void Dispose() {
            Dispose(true);
            GC.SuppressFinalize(this); // 避免终结器执行
        }
    
        protected virtual void Dispose(bool disposing) {
            if (_disposed) return;
            if (disposing) {
                // 释放托管资源（如 _stream）
                _stream?.Dispose();
            }
            // 释放非托管资源（如原生句柄）
            _disposed = true;
        }
    
        ~FileHandler() => Dispose(false); // 终结器：保底释放
    }
    
    // 使用 using 自动释放
    using (var handler = new FileHandler("file.txt")) {
        // 操作文件
    } // 自动调用 Dispose()
    ```

### 四、高级特性

#### 10. 什么是委托（Delegate）和事件（Event）？事件与委托的关系？

- **委托（Delegate）**：一种类型安全的函数指针，用于封装方法，支持将方法作为参数传递或赋值给变量（类似 C++ 的函数指针，但更安全）。例：`public delegate void MyDelegate(string message); ` 
- **事件（Event）**：基于委托的 “发布 - 订阅” 机制，是对委托的封装，限制了外部对委托的直接修改（仅允许 `+=`/`-=` 订阅 / 取消订阅），更安全。例：`public event MyDelegate MyEvent;`
- **关系**：事件是委托的 “安全包装”，委托是事件的底层实现。事件确保只能在类内部触发（`MyEvent?.Invoke(...)`），外部只能订阅 / 取消，避免误操作。
- delegate类似函数声明，函数指针，event是对这个delegate实现，调用的时候直接调event的对象名，外部订阅的时候用“+=”

##### 步骤 1：定义委托（约定方法格式）

委托相当于一个 “方法模板”，规定了可以被封装的方法的**参数和返回值类型**。

```csharp
// 定义委托：无返回值，接收一个字符串参数（闹钟消息）
public delegate void AlarmHandler(string message);
```

##### 步骤 2：定义事件（基于委托的发布者）

事件由 “发布者” 定义，用于通知 “订阅者” 某个事情发生（如闹钟响铃）。

```csharp
// 发布者：闹钟类
public class AlarmClock
{
    // 定义事件：基于上面的 AlarmHandler 委托
    public event AlarmHandler Ring; // 事件名通常用动词的现在分词（如 Ring、Clicked）

    // 闹钟触发响铃的方法（内部逻辑）
    public void Start(int seconds)
    {
        Console.WriteLine($"闹钟将在 {seconds} 秒后响铃...");
        System.Threading.Thread.Sleep(seconds * 1000); // 模拟等待

        // 触发事件：通知所有订阅者（若有订阅者）
        if (Ring != null) // 检查是否有订阅者
        {
            Ring("叮铃铃！起床了！"); // 传递消息给订阅者
        }
    }
}
```

##### 步骤 3：定义订阅者（响应事件的方法）

订阅者提供符合委托格式的方法，通过 “订阅” 事件来接收通知。

```csharp
// 订阅者1：小明（被闹钟叫醒后吃早餐）
public class XiaoMing
{
    // 响应闹钟的方法（必须符合 AlarmHandler 委托的格式）
    public void WakeUp(string message)
    {
        Console.WriteLine($"小明听到：{message}，然后去吃早餐。");
    }
}

// 订阅者2：小红（被闹钟叫醒后看书）
public class XiaoHong
{
    // 响应闹钟的方法（同样符合委托格式）
    public void WakeUp(string message)
    {
        Console.WriteLine($"小红听到：{message}，然后去看书。");
    }
}
```

##### 步骤 4：关联发布者和订阅者（订阅事件）

通过 `+=` 订阅事件，`-=` 取消订阅。

```csharp
class Program
{
    static void Main(string[] args)
    {
        // 创建发布者（闹钟）
        AlarmClock alarm = new AlarmClock();

        // 创建订阅者
        XiaoMing ming = new XiaoMing();
        XiaoHong hong = new XiaoHong();

        // 订阅事件：将订阅者的方法绑定到事件
        alarm.Ring += ming.WakeUp; // 小明订阅
        alarm.Ring += hong.WakeUp; // 小红订阅

        // 启动闹钟（触发事件）
        alarm.Start(3);

        // （可选）取消订阅
        alarm.Ring -= ming.WakeUp;
        Console.WriteLine("\n小明取消了订阅，下次闹钟响铃他不会被通知。");
    }
}
```

##### 运行结果

```plaintext
闹钟将在 3 秒后响铃...
小明听到：叮铃铃！起床了！，然后去吃早餐。
小红听到：叮铃铃！起床了！，然后去看书。

小明取消了订阅，下次闹钟响铃他不会被通知。
```

#### 11. 异步编程中 `async/await` 的作用？与 `Task` 的关系？

- **`async/await`** 是 C# 5.0 引入的异步编程语法糖，简化异步代码的编写，避免回调地狱（Callback Hell）。

- **`Task`/`Task<T>`**：表示异步操作的结果，`async` 方法返回 `Task`（无返回值）或 `Task<T>`（有返回值）。

- **工作原理**：

    - `await` 关键字暂停当前方法执行，将控制权返回给调用者，待异步操作完成后继续执行后续代码；
    - 底层仍基于线程池，但无需手动管理线程，由 CLR 自动调度。

- **示例**：

    ```csharp
    // 异步方法：下载网页内容
    public async Task<string> DownloadAsync(string url) {
        using (var client = new HttpClient()) {
            // await 暂停，等待异步操作完成，不阻塞线程
            return await client.GetStringAsync(url); 
        }
    }
    
    // 调用异步方法
    public async void Main() {
        string content = await DownloadAsync("https://example.com");
        Console.WriteLine(content);
    }
    ```

    

#### 12. 泛型（Generic）的作用是什么？有哪些优势？

- **泛型**：允许在定义类、方法、接口时不指定具体类型，而在使用时指定，实现 “一次定义，多种类型复用”。

- **优势**：

    - **类型安全**：**编译时检查类型，避免装箱 / 拆箱（值类型与 `object` 之间的转换）带来的性能损耗；**
    - **代码复用**：同一逻辑支持不同类型（如 `List<int>`、`List<string>` 共享 `List<T>` 实现）；
    - **减少强制转换**：无需手动将 `object` 转换为具体类型，简化代码。

- **示例**：

    ```csharp
    // 泛型类：支持任意类型的栈
    public class Stack<T> {
        private T[] _items;
        public void Push(T item) { ... }
        public T Pop() { ... }
    }
    
    // 使用：类型安全，无装箱
    var intStack = new Stack<int>();
    intStack.Push(10);
    int value = intStack.Pop();
    ```

    

### 五、实践与场景

#### 13. `using` 语句的作用是什么？适用于哪些场景？

- **作用**：确保实现 `IDisposable` 接口的对象在使用后自动释放资源（调用 `Dispose()` 方法），即使发生异常也能保证释放。

- **适用场景**：操作文件、数据库连接、网络流等非托管资源时，避免资源泄漏。

- **示例**：

    ```csharp
    // 自动释放 FileStream
    using (var stream = new FileStream("file.txt", FileMode.Open)) {
        // 读取文件
    } // 离开作用域时自动调用 stream.Dispose()
    ```

    

#### 14. 什么是装箱（Boxing）和拆箱（Unboxing）？有什么性能影响？

- **装箱**：将值类型（如 `int`）转换为引用类型（`object` 或接口类型），会在堆上创建新对象并复制值，产生额外内存开销。

- **拆箱**：将装箱后的引用类型转换回值类型，需先检查对象类型，再复制值，同样有性能损耗。

- **示例**：

    ```csharp
    int i = 10;
    object o = i; // 装箱：堆上创建 object，复制 i 的值
    int j = (int)o; // 拆箱：检查 o 是 int 装箱的，复制值到 j
    ```

    

- **建议**：避免频繁装箱 / 拆箱（如在泛型出现前，`ArrayList` 存储值类型会频繁装箱，建议用 `List<T>` 替代）。

#### 15. C# 6.0 及以上有哪些重要新特性？

- **C# 6.0**：字符串插值（`$"Hello {name}"`）、空值传播运算符（`?.`）、自动属性初始化器（`public int Age { get; set; } = 18`）等；
- **C# 7.0**：元组（`(int x, int y) = (1, 2)`）、模式匹配（`if (obj is int i)`）、本地函数等；
- **C# 8.0**：可空引用类型（`string?`，编译时检查空引用）、默认接口方法（接口可含实现）等；
- **C# 9.0**：记录类型（`record`， immutable 数据载体）、顶级语句（简化程序入口）等。

### 总结

C# 面试题聚焦 “基础语法”“面向对象特性”“内存管理”“异步编程” 和 “.NET 框架核心”，考察对语言设计思想和实际开发场景的理解。掌握这些知识点，不仅能应对面试，更能写出高效、安全、易维护的 C# 代码。