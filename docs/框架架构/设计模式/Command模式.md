# Command模式

**命令模式**（Command Pattern）是一种数据驱动的设计模式，它属于行为型模式。请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。

# 介绍

**意图：**将一个请求封装成一个对象，从而使您可以用不同的请求对客户进行参数化。

**主要解决：**在软件系统中，行为请求者与行为实现者通常是一种紧耦合的关系，但某些场合，比如需要对行为进行记录、撤销或重做、事务等处理时，这种无法抵御变化的紧耦合的设计就不太合适。

**何时使用：**在某些场合，比如要对行为进行"记录、撤销/重做、事务"等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，如何将"行为请求者"与"行为实现者"解耦？将一组行为抽象为对象，可以实现二者之间的松耦合。

**如何解决：**通过调用者调用接受者执行命令，顺序：调用者→命令→接受者。

**关键代码：**定义三个角色：1、received 真正的命令执行对象 2、Command 3、invoker 使用命令对象的入口

**应用实例：**struts 1 中的 action 核心控制器 ActionServlet 只有一个，相当于 Invoker，而模型层的类会随着不同的应用有不同的模型类，相当于具体的 Command。

**优点：** 1、降低了系统耦合度。 2、新的命令可以很容易添加到系统中去。

**缺点：**使用命令模式可能会导致某些系统有过多的具体命令类。

**使用场景：**认为是命令的地方都可以使用命令模式，比如： 1、GUI 中每一个按钮都是一条命令。 2、模拟 CMD。

# 在 Java 中使用该模式

> 复杂度：⭐
>
> 流行度：⭐⭐⭐

我们首先创建作为命令的接口 *Order*，然后创建作为请求的 *Stock* 类。实体命令类 *BuyStock* 和 *SellStock*，实现了 *Order* 接口，将执行实际的命令处理。创建作为调用对象的类 *Broker*，它接受订单并能下订单。

*Broker* 对象使用命令模式，基于命令的类型确定哪个对象执行哪个命令。*CommandPatternDemo* 类使用 *Broker* 类来演示命令模式。

![img](image/20220427-command-1-command-1.svg)

## Order.java

创建一个命令接口。

```java
package com.llh.command;

/**
 * ClassName: Order
 * Author: Maybe
 * Date: 2022/5/28  16:51
 */
public interface Order {
    void execute();
}
```

## Stock.java

创建一个请求类。

```java
package com.llh.command;

/**
 * ClassName: Stock
 * Author: Maybe
 * Date: 2022/5/28  16:52
 */
public class Stock {
    private String name = "ABC";
    private int quantity = 10;

    public void buy() {
        System.out.println("Stock [ Name: " + name + ", Quantity: " + quantity + " ] bought");
    }

    public void sell() {
        System.out.println("Stock [ Name: " + name + ", Quantity: " + quantity + " ] sold");
    }
}
```

下面创建实现了 *Order* 接口的实体类。

## BuyStock.java

```java
package com.llh.command;

/**
 * ClassName: BuyStock
 * Author: Maybe
 * Date: 2022/5/28  16:52
 */
public class BuyStock implements Order {
    private Stock abcStock;

    public BuyStock(Stock abcStock) {
        this.abcStock = abcStock;
    }

    @Override
    public void execute() {
        abcStock.buy();
    }
}
```

## SellStock.java

```java
package com.llh.command;

/**
 * ClassName: SellStock
 * Author: Maybe
 * Date: 2022/5/28  16:53
 */
public class SellStock implements Order {
    private Stock abcStock;

    public SellStock(Stock abcStock) {
        this.abcStock = abcStock;
    }

    @Override
    public void execute() {
        abcStock.sell();
    }
}
```

## Broker.java

创建命令调用类。

```java
package com.llh.command;

import java.util.ArrayList;
import java.util.List;

/**
 * ClassName: Broker
 * Author: Maybe
 * Date: 2022/5/28  17:04
 */
public class Broker {
    private List<Order> orderList = new ArrayList<Order>();

    public void takeOrder(Order order) {
        orderList.add(order);
    }

    public void placeOrders() {
        for (Order order : orderList) {
            order.execute();
        }
        orderList.clear();
    }
}
```

## CommandPatternDemo.java

使用 Broker 类来接受并执行命令。

```java
package com.llh.command;

/**
 * ClassName: CommandPatternDemo
 * Author: Maybe
 * Date: 2022/5/28  17:05
 */
public class CommandPatternDemo {
    public static void main(String[] args) {
        Stock abcStock = new Stock();

        BuyStock buyStockOrder = new BuyStock(abcStock);
        SellStock sellStockOrder = new SellStock(abcStock);

        Broker broker = new Broker();
        broker.takeOrder(buyStockOrder);
        broker.takeOrder(sellStockOrder);

        broker.placeOrders();
    }
}
```

运行结果：

```
Stock [ Name: ABC, Quantity: 10 ] bought
Stock [ Name: ABC, Quantity: 10 ] sold
```

# 登场的角色

 ![命令设计模式的结构](image/structure.png ":size=60%")

- **发送者** （Sender）——亦称 “触发者 （Invoker）”——类负责对请求进行初始化， 其中必须包含一个成员变量来存储对于命令对象的引用。 发送者触发命令， 而不向接收者直接发送请求。 注意， 发送者并不负责创建命令对象： 它通常会通过构造函数从客户端处获得预先生成的命令。（**Broker.java**）

- **命令** （Command） 接口通常仅声明一个执行命令的方法。（**Order.java**）

- **具体命令** （Concrete Commands） 会实现各种类型的请求。 具体命令自身并不完成工作， 而是会将调用委派给一个业务逻辑对象。 但为了简化代码， 这些类可以进行合并。（**BuyStock.java**和**SellStock.java**）

  接收对象执行方法所需的参数可以声明为具体命令的成员变量。 你可以将命令对象设为不可变， 仅允许通过构造函数对这些成员变量进行初始化。

- **接收者** （Receiver） 类包含部分业务逻辑。 几乎任何对象都可以作为接收者。 绝大部分命令只处理如何将请求传递到接收者的细节， 接收者自己会完成实际的工作。

- **客户端** （Client） 会创建并配置具体命令对象。 客户端必须将包括接收者实体在内的所有请求参数传递给命令的构造函数。 此后， 生成的命令就可以与一个或多个发送者相关联了。

# 命令模式适合应用场景

 **如果你需要通过操作来参数化对象， 可使用命令模式。**

 命令模式可将特定的方法调用转化为独立对象。 这一改变也带来了许多有趣的应用： 你可以将命令作为方法的参数进行传递、 将命令保存在其他对象中， 或者在运行时切换已连接的命令等。

举个例子： 你正在开发一个 GUI 组件 （例如上下文菜单）， 你希望用户能够配置菜单项， 并在点击菜单项时触发操作。

---

**如果你想要将操作放入队列中、 操作的执行或者远程执行操作， 可使用命令模式。**

同其他对象一样， 命令也可以实现序列化 （序列化的意思是转化为字符串）， 从而能方便地写入文件或数据库中。 一段时间后， 该字符串可被恢复成为最初的命令对象。 因此， 你可以延迟或计划命令的执行。 但其功能远不止如此！ 使用同样的方式， 你还可以将命令放入队列、 记录命令或者通过网络发送命令。

---

**如果你想要实现操作回滚功能， 可使用命令模式。**

尽管有很多方法可以实现撤销和恢复功能， 但命令模式可能是其中最常用的一种。

为了能够回滚操作， 你需要实现已执行操作的历史记录功能。 命令历史记录是一种包含所有已执行命令对象及其相关程序状态备份的栈结构。

这种方法有两个缺点。 首先， 程序状态的保存功能并不容易实现， 因为部分状态可能是私有的。 你可以使用[备忘录](https://refactoringguru.cn/design-patterns/memento)模式来在一定程度上解决这个问题。

其次， 备份状态可能会占用大量内存。 因此， 有时你需要借助另一种实现方式： 命令无需恢复原始状态， 而是执行反向操作。 反向操作也有代价： 它可能会很难甚至是无法实现。

# 实现方式

1. 声明仅有一个执行方法的命令接口。
2. 抽取请求并使之成为实现命令接口的具体命令类。 每个类都必须有一组成员变量来保存请求参数和对于实际接收者对象的引用。 所有这些变量的数值都必须通过命令构造函数进行初始化。
3. 找到担任*发送者*职责的类。 在这些类中添加保存命令的成员变量。 发送者只能通过命令接口与其命令进行交互。 发送者自身通常并不创建命令对象， 而是通过客户端代码获取。
4. 修改发送者使其执行命令， 而非直接将请求发送给接收者。
5. 客户端必须按照以下顺序来初始化对象：
   - 创建接收者。
   - 创建命令， 如有需要可将其关联至接收者。
   - 创建发送者并将其与特定命令关联。

# 命令模式优缺点

> [!tip|label:优点]
>
> -  *单一职责原则*。 你可以解耦触发和执行操作的类。
> -  *开闭原则*。 你可以在不修改已有客户端代码的情况下在程序中创建新的命令。
> -  你可以实现撤销和恢复功能。
> -  你可以实现操作的延迟执行。
> -  你可以将一组简单命令组合成一个复杂命令。

> [!danger|label:缺点]
>
> - 代码可能会变得更加复杂， 因为你在发送者和接收者之间增加了一个全新的层次。

# 与其他模式的关系

- [责任链模式](https://refactoringguru.cn/design-patterns/chain-of-responsibility)、 [命令模式](https://refactoringguru.cn/design-patterns/command)、 [中介者模式](https://refactoringguru.cn/design-patterns/mediator)和[观察者模式](https://refactoringguru.cn/design-patterns/observer)用于处理请求发送者和接收者之间的不同连接方式：

  - *责任链*按照顺序将请求动态传递给一系列的潜在接收者， 直至其中一名接收者对请求进行处理。
  - *命令*在发送者和请求者之间建立单向连接。
  - *中介者*清除了发送者和请求者之间的直接连接， 强制它们通过一个中介对象进行间接沟通。
  - *观察者*允许接收者动态地订阅或取消接收请求。

- [责任链](https://refactoringguru.cn/design-patterns/chain-of-responsibility)的管理者可使用[命令模式](https://refactoringguru.cn/design-patterns/command)实现。 在这种情况下， 你可以对由请求代表的同一个上下文对象执行许多不同的操作。

  还有另外一种实现方式， 那就是请求自身就是一个*命令*对象。 在这种情况下， 你可以对由一系列不同上下文连接而成的链执行相同的操作。

- 你可以同时使用[命令](https://refactoringguru.cn/design-patterns/command)和[备忘录模式](https://refactoringguru.cn/design-patterns/memento)来实现 “撤销”。 在这种情况下， 命令用于对目标对象执行各种不同的操作， 备忘录用来保存一条命令执行前该对象的状态。

- [命令](https://refactoringguru.cn/design-patterns/command)和[策略模式](https://refactoringguru.cn/design-patterns/strategy)看上去很像， 因为两者都能通过某些行为来参数化对象。 但是， 它们的意图有非常大的不同。

  - 你可以使用*命令*来将任何操作转换为对象。 操作的参数将成为对象的成员变量。 你可以通过转换来延迟操作的执行、 将操作放入队列、 保存历史命令或者向远程服务发送命令等。
  - 另一方面， *策略*通常可用于描述完成某件事的不同方式， 让你能够在同一个上下文类中切换算法。

- [原型模式](https://refactoringguru.cn/design-patterns/prototype)可用于保存[命令](https://refactoringguru.cn/design-patterns/command)的历史记录。

- 你可以将[访问者模式](https://refactoringguru.cn/design-patterns/visitor)视为[命令模式](https://refactoringguru.cn/design-patterns/command)的加强版本， 其对象可对不同类的多种对象执行操作。
