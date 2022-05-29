# Mediator模式

**中介者模式**是一种行为设计模式， 能让你减少对象之间混乱无序的依赖关系。 该模式会限制对象之间的直接交互， 迫使它们通过一个中介者对象进行合作。

 ![中介者设计模式](image/mediator.png)

## 问题

假如你有一个创建和修改客户资料的对话框， 它由各种控件组成， 例如文本框 （Text­Field）、 复选框 （Checkbox） 和按钮 （Button） 等。

![用户界面中各元素间的混乱关系](image/problem1-zh-165382848764027.png "用户界面中各元素间的关系会随程序发展而变得混乱。")

某些表单元素可能会直接进行互动。 例如， 选中 “我有一只狗” 复选框后可能会显示一个隐藏文本框用于输入狗狗的名字。 另一个例子是提交按钮必须在保存数据前校验所有输入内容。

![UI 中各元素相互依赖](image/problem2.png "元素间存在许多关联。 因此， 对某些元素进行修改可能会影响其他元素。")

如果直接在表单元素代码中实现业务逻辑， 你将很难在程序其他表单中复用这些元素类。 例如， 由于复选框类与狗狗的文本框相耦合， 所以将无法在其他表单中使用它。 你要么使用渲染资料表单时用到的所有类， 要么一个都不用。

## 解决方案

中介者模式建议你停止组件之间的直接交流并使其相互独立。 这些组件必须调用特殊的中介者对象， 通过中介者对象重定向调用行为， 以间接的方式进行合作。 最终， 组件仅依赖于一个中介者类， 无需与多个其他组件相耦合。

在资料编辑表单的例子中， 对话框 （Dialog） 类本身将作为中介者， 其很可能已知自己所有的子元素， 因此你甚至无需在该类中引入新的依赖关系。

![UI 元素必须通过中介者进行沟通。](image/solution1-zh-165382862807330.png "UI 元素必须通过中介者对象进行间接沟通。")

绝大部分重要的修改都在实际表单元素中进行。 让我们想想提交按钮。 之前， 当用户点击按钮后， 它必须对所有表单元素数值进行校验。 而现在它的唯一工作是将点击事件通知给对话框。 收到通知后， 对话框可以自行校验数值或将任务委派给各元素。 这样一来， 按钮不再与多个表单元素相关联， 而仅依赖于对话框类。

你还可以为所有类型的对话框抽取通用接口， 进一步削弱其依赖性。 接口中将声明一个所有表单元素都能使用的通知方法， 可用于将元素中发生的事件通知给对话框。 这样一来， 所有实现了该接口的对话框都能使用这个提交按钮了。

采用这种方式， 中介者模式让你能在单个中介者对象中封装多个对象间的复杂关系网。 类所拥有的依赖关系越少， 就越易于修改、 扩展或复用。

## 真实世界类比

![空中交通管制塔台](image/live-example.png "飞行器驾驶员之间不会通过相互沟通来决定下一架降落的飞机。 所有沟通都通过控制塔台进行。")

飞行器驾驶员们在靠近或离开空中管制区域时不会直接相互交流。 但他们会与飞机跑道附近， 塔台中的空管员通话。 如果没有空管员， 驾驶员就需要留意机场附近的所有飞机， 并与数十位飞行员组成的委员会讨论降落顺序。 那恐怕会让飞机坠毁的统计数据一飞冲天吧。

塔台无需管制飞行全程， 只需在航站区加强管控即可， 因为该区域的决策参与者数量对于飞行员来说实在太多了。

## 中介者模式结构

![中介者设计模式的结构](image/structure-165382869588933.png)

1. **组件** （Component） 是各种包含业务逻辑的类。 每个组件都有一个指向中介者的引用， 该引用被声明为中介者接口类型。 组件不知道中介者实际所属的类， 因此你可通过将其连接到不同的中介者以使其能在其他程序中复用。
2. **中介者** （Mediator） 接口声明了与组件交流的方法， 但通常仅包括一个通知方法。 组件可将任意上下文 （包括自己的对象） 作为该方法的参数， 只有这样接收组件和发送者类之间才不会耦合。

3. **具体中介者** （Concrete Mediator） 封装了多种组件间的关系。 具体中介者通常会保存所有组件的引用并对其进行管理， 甚至有时会对其生命周期进行管理。

4. 组件并不知道其他组件的情况。 如果组件内发生了重要事件， 它只能通知中介者。 中介者收到通知后能轻易地确定发送者， 这或许已足以判断接下来需要触发的组件了。

   对于组件来说， 中介者看上去完全就是一个黑箱。 发送者不知道最终会由谁来处理自己的请求， 接收者也不知道最初是谁发出了请求。

## 在 Java 中使用模式

> 复杂度：⭐⭐
>
> 流行度：⭐⭐

**使用示例：** 中介者模式在 Java 代码中最常用于帮助程序 GUI 组件之间的通信。 在 MVC 模式中， 控制器是中介者的同义词。

下面是核心 Java 程序库中该模式的一些示例：

- [`java.util.Timer`](https://docs.oracle.com/javase/8/docs/api/java/util/Timer.html) （所有 `schedule­XXX()`方法）
- [`java.util.concurrent.Executor#execute()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html#execute-java.lang.Runnable-)
- [`java.util.concurrent.ExecutorService`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html) （ `invoke­XXX()`和 `submit­()`方法）
- [`java.util.concurrent.ScheduledExecutorService`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledExecutorService.html) （所有 `schedule­XXX()`方法）
- [`java.lang.reflect.Method#invoke()`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#invoke-java.lang.Object-java.lang.Object...-)

### 示例

我们通过聊天室实例来演示中介者模式。实例中，多个用户可以向聊天室发送消息，聊天室向所有的用户显示消息。我们将创建两个类 *ChatRoom* 和 *User*。*User* 对象使用 *ChatRoom* 方法来分享他们的消息。

*MediatorPatternDemo*，我们的演示类使用 *User* 对象来显示他们之间的通信。

 ![中介者模式的 UML 图](image/mediator_pattern_uml_diagram.jpg ":size=60%")

### ChatRoom.java

创建中介类。

```java
package com.llh.mediator;

import java.util.Date;

/**
 * ClassName: ChatRoom
 * Author: Maybe
 * Date: 2022/5/29  20:57
 */
public class ChatRoom {
    public static void showMessage(User user, String message) {
        System.out.println(new Date().toString()
                + " [" + user.getName() + "] : " + message);
    }
}
```

### User.java

创建 user 类。

```java
package com.llh.mediator;

/**
 * ClassName: User
 * Author: Maybe
 * Date: 2022/5/29  20:57
 */
public class User {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public User(String name) {
        this.name = name;
    }

    public void sendMessage(String message) {
        ChatRoom.showMessage(this, message);
    }
}
```

### MediatorPatternDemo.java

使用 *User* 对象来显示他们之间的通信。

```java
package com.llh.mediator;

/**
 * ClassName: MediatorPatternDemo
 * Author: Maybe
 * Date: 2022/5/29  20:58
 */
public class MediatorPatternDemo {
    public static void main(String[] args) {
        User robert = new User("Robert");
        User john = new User("John");

        robert.sendMessage("Hi! John!");
        john.sendMessage("Hello! Robert!");
    }
}
```

**运行结果：**

```
Sun May 29 20:58:37 CST 2022 [Robert] : Hi! John!
Sun May 29 20:58:37 CST 2022 [John] : Hello! Robert!
```

## 中介者模式适合应用场景

 **当一些对象和其他对象紧密耦合以致难以对其进行修改时， 可使用中介者模式。**

 该模式让你将对象间的所有关系抽取成为一个单独的类， 以使对于特定组件的修改工作独立于其他组件。

---

 **当组件因过于依赖其他组件而无法在不同应用中复用时， 可使用中介者模式。**

 应用中介者模式后， 每个组件不再知晓其他组件的情况。 尽管这些组件无法直接交流， 但它们仍可通过中介者对象进行间接交流。 如果你希望在不同应用中复用一个组件， 则需要为其提供一个新的中介者类。

---

 **如果为了能在不同情景下复用一些基本行为， 导致你需要被迫创建大量组件子类时， 可使用中介者模式。**

 由于所有组件间关系都被包含在中介者中， 因此你无需修改组件就能方便地新建中介者类以定义新的组件合作方式。

## 实现方式

1. 找到一组当前紧密耦合， 且提供其独立性能带来更大好处的类 （例如更易于维护或更方便复用）。

2. 声明中介者接口并描述中介者和各种组件之间所需的交流接口。 在绝大多数情况下， 一个接收组件通知的方法就足够了。

   如果你希望在不同情景下复用组件类， 那么该接口将非常重要。 只要组件使用通用接口与其中介者合作， 你就能将该组件与不同实现中的中介者进行连接。

3. 实现具体中介者类。 该类可从自行保存其下所有组件的引用中受益。

4. 你可以更进一步， 让中介者负责组件对象的创建和销毁。 此后， 中介者可能会与[工厂](https://refactoringguru.cn/design-patterns/abstract-factory)或[外观](https://refactoringguru.cn/design-patterns/facade)类似。

5. 组件必须保存对于中介者对象的引用。 该连接通常在组件的构造函数中建立， 该函数会将中介者对象作为参数传递。

6. 修改组件代码， 使其可调用中介者的通知方法， 而非其他组件的方法。 然后将调用其他组件的代码抽取到中介者类中， 并在中介者接收到该组件通知时执行这些代码。

## 中介者模式优缺点

> [!tip|label:优点]
>
> -  *单一职责原则*。 你可以将多个组件间的交流抽取到同一位置， 使其更易于理解和维护。
> -  *开闭原则*。 你无需修改实际组件就能增加新的中介者。
> -  你可以减轻应用中多个组件间的耦合情况。
> -  你可以更方便地复用各个组件。

> [!danger|label:缺点]
>
> - 一段时间后， 中介者可能会演化成为[上帝对象](https://refactoringguru.cn/antipatterns/god-object)。

## 与其他模式的关系

- [责任链模式](https://refactoringguru.cn/design-patterns/chain-of-responsibility)、 [命令模式](https://refactoringguru.cn/design-patterns/command)、 [中介者模式](https://refactoringguru.cn/design-patterns/mediator)和[观察者模式](https://refactoringguru.cn/design-patterns/observer)用于处理请求发送者和接收者之间的不同连接方式：

  - *责任链*按照顺序将请求动态传递给一系列的潜在接收者， 直至其中一名接收者对请求进行处理。
  - *命令*在发送者和请求者之间建立单向连接。
  - *中介者*清除了发送者和请求者之间的直接连接， 强制它们通过一个中介对象进行间接沟通。
  - *观察者*允许接收者动态地订阅或取消接收请求。

- [外观模式](https://refactoringguru.cn/design-patterns/facade)和[中介者](https://refactoringguru.cn/design-patterns/mediator)的职责类似： 它们都尝试在大量紧密耦合的类中组织起合作。

  - *外观*为子系统中的所有对象定义了一个简单接口， 但是它不提供任何新功能。 子系统本身不会意识到外观的存在。 子系统中的对象可以直接进行交流。
  - *中介者*将系统中组件的沟通行为中心化。 各组件只知道中介者对象， 无法直接相互交流。

- [中介者](https://refactoringguru.cn/design-patterns/mediator)和[观察者](https://refactoringguru.cn/design-patterns/observer)之间的区别往往很难记住。 在大部分情况下， 你可以使用其中一种模式， 而有时可以同时使用。 让我们来看看如何做到这一点。

  *中介者*的主要目标是消除一系列系统组件之间的相互依赖。 这些组件将依赖于同一个中介者对象。 *观察者*的目标是在对象之间建立动态的单向连接， 使得部分对象可作为其他对象的附属发挥作用。

  有一种流行的中介者模式实现方式依赖于*观察者*。 中介者对象担当发布者的角色， 其他组件则作为订阅者， 可以订阅中介者的事件或取消订阅。 当*中介者*以这种方式实现时， 它可能看上去与*观察者*非常相似。

  当你感到疑惑时， 记住可以采用其他方式来实现中介者。 例如， 你可永久性地将所有组件链接到同一个中介者对象。 这种实现方式和*观察者*并不相同， 但这仍是一种中介者模式。

  假设有一个程序， 其所有的组件都变成了发布者， 它们之间可以相互建立动态连接。 这样程序中就没有中心化的中介者对象， 而只有一些分布式的观察者。
