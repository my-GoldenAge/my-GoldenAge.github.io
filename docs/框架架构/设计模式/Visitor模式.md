# Visitor模式

**访问者模式**是一种行为设计模式， 它能将算法与其所作用的对象隔离开来。

![访问者设计模式](image/visitor.png)

## 问题

假如你的团队开发了一款能够使用巨型图像中地理信息的应用程序。 图像中的每个节点既能代表复杂实体 （例如一座城市）， 也能代表更精细的对象 （例如工业区和旅游景点等）。 如果节点代表的真实对象之间存在公路， 那么这些节点就会相互连接。 在程序内部， 每个节点的类型都由其所属的类来表示， 每个特定的节点则是一个对象。

![将图像导出为 XML](image/problem1.png "将图像导出为 XML。")

一段时间后， 你接到了实现将图像导出到 XML 文件中的任务。 这些工作最初看上去非常简单。 你计划为每个节点类添加导出函数， 然后递归执行图像中每个节点的导出函数。 解决方案简单且优雅： 使用多态机制可以让导出方法的调用代码不会和具体的节点类相耦合。

但你不太走运， 系统架构师拒绝批准对已有节点类进行修改。 他认为这些代码已经是产品了， 不想冒险对其进行修改， 因为修改可能会引入潜在的缺陷。

![所有节点的类中都必须添加导出至 XML 文件的方法](image/problem2-zh-165389120790412.png "所有节点的类中都必须添加导出至 XML 文件的方法， 但如果在修改代码的过程中引入了任何缺陷， 那么整个程序都会面临风险。")

此外， 他还质疑在节点类中包含导出 XML 文件的代码是否有意义。 这些类的主要工作是处理地理数据。 导出 XML 文件的代码放在这里并不合适。

还有另一个原因， 那就是在此项任务完成后， 营销部门很有可能会要求程序提供导出其他类型文件的功能， 或者提出其他奇怪的要求。 这样你很可能会被迫再次修改这些重要但脆弱的类。

## 解决方案

访问者模式建议将新行为放入一个名为*访问者*的独立类中， 而不是试图将其整合到已有类中。 现在， 需要执行操作的原始对象将作为参数被传递给访问者中的方法， 让方法能访问对象所包含的一切必要数据。

如果现在该操作能在不同类的对象上执行会怎么样呢？ 比如在我们的示例中， 各节点类导出 XML 文件的实际实现很可能会稍有不同。 因此， 访问者类可以定义一组 （而不是一个） 方法， 且每个方法可接收不同类型的参数， 如下所示：

```伪代码
class ExportVisitor implements Visitor is
    method doForCity(City c) { ... }
    method doForIndustry(Industry f) { ... }
    method doForSightSeeing(SightSeeing ss) { ... }
    // ...
```

但我们究竟应该如何调用这些方法 （尤其是在处理整个图像方面） 呢？ 这些方法的签名各不相同， 因此我们不能使用多态机制。 为了可以挑选出能够处理特定对象的访问者方法， 我们需要对它的类进行检查。 这是不是听上去像个噩梦呢？

```伪代码
foreach (Node node in graph)
    if (node instanceof City)
        exportVisitor.doForCity((City) node)
    if (node instanceof Industry)
        exportVisitor.doForIndustry((Industry) node)
    // ...
}
```

你可能会问， 我们为什么不使用方法重载呢？ 就是使用相同的方法名称， 但它们的参数不同。 不幸的是， 即使我们的编程语言 （例如 Java 和 C#） 支持重载也不行。 由于我们无法提前知晓节点对象所属的类， 所以重载机制无法执行正确的方法。 方法会将 `节点`基类作为输入参数的默认类型。

但是， 访问者模式可以解决这个问题。 它使用了一种名为[双分派](https://refactoringguru.cn/design-patterns/visitor-double-dispatch)的技巧， 不使用累赘的条件语句也可下执行正确的方法。 与其让客户端来选择调用正确版本的方法， 不如将选择权委派给作为参数传递给访问者的对象。 由于该对象知晓其自身的类， 因此能更自然地在访问者中选出正确的方法。 它们会 “接收” 一个访问者并告诉其应执行的访问者方法。

```伪代码
// 客户端代码
foreach (Node node in graph)
    node.accept(exportVisitor)

// 城市
class City is
    method accept(Visitor v) is
        v.doForCity(this)
    // ...

// 工业区
class Industry is
    method accept(Visitor v) is
        v.doForIndustry(this)
    // ...
```

我承认最终还是修改了节点类， 但毕竟改动很小， 且使得我们能够在后续进一步添加行为时无需再次修改代码。

现在， 如果我们抽取出所有访问者的通用接口， 所有已有的节点都能与我们在程序中引入的任何访问者交互。 如果需要引入与节点相关的某个行为， 你只需要实现一个新的访问者类即可。

## 真实世界类比

![保险代理](image/visitor-comic-1.png "优秀的保险代理人总能为不同类型的团体提供不同的保单。")

假如有这样一位非常希望赢得新客户的资深保险代理人。 他可以拜访街区中的每栋楼， 尝试向每个路人推销保险。 所以， 根据大楼内组织类型的不同， 他可以提供专门的保单：

- 如果建筑是居民楼， 他会推销医疗保险。
- 如果建筑是银行， 他会推销失窃保险。
- 如果建筑是咖啡厅， 他会推销火灾和洪水保险。

## 访问者模式结构

![访问者设计模式的结构](image/structure-zh-165389183021215.png)

1. **访问者** （Visitor） 接口声明了一系列以对象结构的具体元素为参数的访问者方法。 如果编程语言支持重载， 这些方法的名称可以是相同的， 但是其参数一定是不同的。
2. **具体访问者** （Concrete Visitor） 会为不同的具体元素类实现相同行为的几个不同版本。
3. **元素** （Element） 接口声明了一个方法来 “接收” 访问者。 该方法必须有一个参数被声明为访问者接口类型。
4. **具体元素** （Concrete Element） 必须实现接收方法。 该方法的目的是根据当前元素类将其调用重定向到相应访问者的方法。 请注意， 即使元素基类实现了该方法， 所有子类都必须对其进行重写并调用访问者对象中的合适方法。
5. **客户端** （Client） 通常会作为集合或其他复杂对象 （例如一个[组合](https://refactoringguru.cn/design-patterns/composite)树） 的代表。 客户端通常不知晓所有的具体元素类， 因为它们会通过抽象接口与集合中的对象进行交互。

## 在 Java 中使用模式

> 复杂度： ⭐⭐⭐
>
> 流行度： ⭐

**使用示例：** 访问者不是常用的设计模式， 因为它不仅复杂， 应用范围也比较狭窄。

这里是 Java 程序库代码中该模式的一些示例：

- [`javax.lang.model.element.AnnotationValue`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/AnnotationValue.html) 和 [`Annotation­Value­Visitor`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/AnnotationValueVisitor.html)
- [`javax.lang.model.element.Element`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/Element.html) 和 [`Element­Visitor`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/ElementVisitor.html)
- [`javax.lang.model.type.TypeMirror`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/type/TypeMirror.html) 和 [`Type­Visitor`](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/type/TypeVisitor.html)
- [`java.nio.file.FileVisitor`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitor.html) 和 [`Simple­File­Visitor`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/SimpleFileVisitor.html)
- [`javax.faces.component.visit.VisitContext`](https://docs.oracle.com/javaee/7/api/javax/faces/component/visit/VisitContext.html) 和 [`Visit­Callback`](https://docs.oracle.com/javaee/7/api/javax/faces/component/visit/VisitCallback.html)

### 将形状导出为 XML 文件

在本例中， 我们希望将一系列几何形状导出为 XML 文件。 重点在于我们不希望直接修改形状代码， 或者至少能确保最小程度的修改。

最终， 访问者模式建立了一个框架， 允许我们在不修改已有类的情况下向形状层次结构中添加新的行为。

### shapes/Shape.java: 通用形状接口

```java
package com.llh.visitor.shapes;

import com.llh.visitor.visitor.Visitor;

/**
 * ClassName: Shape
 * Author: Maybe
 * Date: 2022/5/30  14:27
 */
public interface Shape {
    void move(int x, int y);

    void draw();

    String accept(Visitor visitor);
}
```

### shapes/Dot.java: 点

```java
package com.llh.visitor.shapes;

import com.llh.visitor.visitor.Visitor;

/**
 * ClassName: Dot
 * Author: Maybe
 * Date: 2022/5/30  14:28
 */
public class Dot implements Shape {
    private int id;
    private int x;
    private int y;

    public Dot() {
    }

    public Dot(int id, int x, int y) {
        this.id = id;
        this.x = x;
        this.y = y;
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitDot(this);
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public int getId() {
        return id;
    }
}
```

### shapes/Circle.java: 圆形

```java
package com.llh.visitor.shapes;

import com.llh.visitor.visitor.Visitor;

/**
 * ClassName: Circle
 * Author: Maybe
 * Date: 2022/5/30  14:28
 */
public class Circle extends Dot {
    private int radius;

    public Circle(int id, int x, int y, int radius) {
        super(id, x, y);
        this.radius = radius;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitCircle(this);
    }

    public int getRadius() {
        return radius;
    }
}
```

### shapes/Rectangle.java: 矩形

```java
package com.llh.visitor.shapes;

import com.llh.visitor.visitor.Visitor;

/**
 * ClassName: Rectangle
 * Author: Maybe
 * Date: 2022/5/30  14:29
 */
public class Rectangle implements Shape {
    private int id;
    private int x;
    private int y;
    private int width;
    private int height;

    public Rectangle(int id, int x, int y, int width, int height) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitRectangle(this);
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    public int getId() {
        return id;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public int getWidth() {
        return width;
    }

    public int getHeight() {
        return height;
    }
}
```

### shapes/CompoundShape.java: 组合形状

```java
package com.llh.visitor.shapes;

import com.llh.visitor.visitor.Visitor;

import java.util.ArrayList;
import java.util.List;

/**
 * ClassName: CompoundShape
 * Author: Maybe
 * Date: 2022/5/30  14:30
 */
public class CompoundShape implements Shape {
    public int id;
    public List<Shape> children = new ArrayList<>();

    public CompoundShape(int id) {
        this.id = id;
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    public int getId() {
        return id;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitCompoundGraphic(this);
    }

    public void add(Shape shape) {
        children.add(shape);
    }
}
```

### visitor/Visitor.java: 通用访问者接口

```java
package com.llh.visitor.visitor;

import com.llh.visitor.shapes.Circle;
import com.llh.visitor.shapes.CompoundShape;
import com.llh.visitor.shapes.Dot;
import com.llh.visitor.shapes.Rectangle;

/**
 * ClassName: Visitor
 * Author: Maybe
 * Date: 2022/5/30  14:30
 */
public interface Visitor {
    String visitDot(Dot dot);

    String visitCircle(Circle circle);

    String visitRectangle(Rectangle rectangle);

    String visitCompoundGraphic(CompoundShape cg);
}
```

### visitor/XMLExportVisitor.java: 具体访问者， 将所有形状导出为 XML 文件

```java
package com.llh.visitor.visitor;

import com.llh.visitor.shapes.*;

/**
 * ClassName: XMLExportVisitor
 * Author: Maybe
 * Date: 2022/5/30  14:31
 */
public class XMLExportVisitor implements Visitor {
    public String export(Shape... args) {
        StringBuilder sb = new StringBuilder();
        sb.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>" + "\n");
        for (Shape shape : args) {
            sb.append(shape.accept(this)).append("\n");
        }
        return sb.toString();
    }

    public String visitDot(Dot d) {
        return "<dot>" + "\n" +
                "    <id>" + d.getId() + "</id>" + "\n" +
                "    <x>" + d.getX() + "</x>" + "\n" +
                "    <y>" + d.getY() + "</y>" + "\n" +
                "</dot>";
    }

    public String visitCircle(Circle c) {
        return "<circle>" + "\n" +
                "    <id>" + c.getId() + "</id>" + "\n" +
                "    <x>" + c.getX() + "</x>" + "\n" +
                "    <y>" + c.getY() + "</y>" + "\n" +
                "    <radius>" + c.getRadius() + "</radius>" + "\n" +
                "</circle>";
    }

    public String visitRectangle(Rectangle r) {
        return "<rectangle>" + "\n" +
                "    <id>" + r.getId() + "</id>" + "\n" +
                "    <x>" + r.getX() + "</x>" + "\n" +
                "    <y>" + r.getY() + "</y>" + "\n" +
                "    <width>" + r.getWidth() + "</width>" + "\n" +
                "    <height>" + r.getHeight() + "</height>" + "\n" +
                "</rectangle>";
    }

    public String visitCompoundGraphic(CompoundShape cg) {
        return "<compound_graphic>" + "\n" +
                "   <id>" + cg.getId() + "</id>" + "\n" +
                _visitCompoundGraphic(cg) +
                "</compound_graphic>";
    }

    private String _visitCompoundGraphic(CompoundShape cg) {
        StringBuilder sb = new StringBuilder();
        for (Shape shape : cg.children) {
            String obj = shape.accept(this);
            // Proper indentation for sub-objects.
            obj = "    " + obj.replace("\n", "\n    ") + "\n";
            sb.append(obj);
        }
        return sb.toString();
    }
}
```

### Demo.java: 客户端代码

```java
package com.llh.visitor;

import com.llh.visitor.shapes.*;
import com.llh.visitor.visitor.XMLExportVisitor;

/**
 * ClassName: Demo
 * Author: Maybe
 * Date: 2022/5/30  14:35
 */
public class Demo {
    public static void main(String[] args) {
        Dot dot = new Dot(1, 10, 55);
        Circle circle = new Circle(2, 23, 15, 10);
        Rectangle rectangle = new Rectangle(3, 10, 17, 20, 30);

        CompoundShape compoundShape = new CompoundShape(4);
        compoundShape.add(dot);
        compoundShape.add(circle);
        compoundShape.add(rectangle);

        CompoundShape c = new CompoundShape(5);
        c.add(dot);
        compoundShape.add(c);

        export(circle, compoundShape);
    }

    private static void export(Shape... shapes) {
        XMLExportVisitor exportVisitor = new XMLExportVisitor();
        System.out.println(exportVisitor.export(shapes));
    }
}
```

**运行结果：**

```xml
<?xml version="1.0" encoding="utf-8"?>
<circle>
    <id>2</id>
    <x>23</x>
    <y>15</y>
    <radius>10</radius>
</circle>
<compound_graphic>
   <id>4</id>
    <dot>
        <id>1</id>
        <x>10</x>
        <y>55</y>
    </dot>
    <circle>
        <id>2</id>
        <x>23</x>
        <y>15</y>
        <radius>10</radius>
    </circle>
    <rectangle>
        <id>3</id>
        <x>10</x>
        <y>17</y>
        <width>20</width>
        <height>30</height>
    </rectangle>
    <compound_graphic>
       <id>5</id>
        <dot>
            <id>1</id>
            <x>10</x>
            <y>55</y>
        </dot>
    </compound_graphic>
</compound_graphic>
```

## 访问者模式适合应用场景

 **如果你需要对一个复杂对象结构 （例如对象树） 中的所有元素执行某些操作， 可使用访问者模式。**

 访问者模式通过在访问者对象中为多个目标类提供相同操作的变体， 让你能在属于不同类的一组对象上执行同一操作。

---

 **可使用访问者模式来清理辅助行为的业务逻辑。**

 该模式会将所有非主要的行为抽取到一组访问者类中， 使得程序的主要类能更专注于主要的工作。

---

 **当某个行为仅在类层次结构中的一些类中有意义， 而在其他类中没有意义时， 可使用该模式。**

 你可将该行为抽取到单独的访问者类中， 只需实现接收相关类的对象作为参数的访问者方法并将其他方法留空即可。

## 实现方式

1. 在访问者接口中声明一组 “访问” 方法， 分别对应程序中的每个具体元素类。

2. 声明元素接口。 如果程序中已有元素类层次接口， 可在层次结构基类中添加抽象的 “接收” 方法。 该方法必须接受访问者对象作为参数。

3. 在所有具体元素类中实现接收方法。 这些方法必须将调用重定向到当前元素对应的访问者对象中的访问者方法上。

4. 元素类只能通过访问者接口与访问者进行交互。 不过访问者必须知晓所有的具体元素类， 因为这些类在访问者方法中都被作为参数类型引用。

5. 为每个无法在元素层次结构中实现的行为创建一个具体访问者类并实现所有的访问者方法。

   你可能会遇到访问者需要访问元素类的部分私有成员变量的情况。 在这种情况下， 你要么将这些变量或方法设为公有， 这将破坏元素的封装； 要么将访问者类嵌入到元素类中。 后一种方式只有在支持嵌套类的编程语言中才可能实现。

6. 客户端必须创建访问者对象并通过 “接收” 方法将其传递给元素。

## 访问者模式优缺点

> [!tip|label:优点]
>
> -  *开闭原则*。 你可以引入在不同类对象上执行的新行为， 且无需对这些类做出修改。
> -  *单一职责原则*。 可将同一行为的不同版本移到同一个类中。
> -  访问者对象可以在与各种对象交互时收集一些有用的信息。 当你想要遍历一些复杂的对象结构 （例如对象树）， 并在结构中的每个对象上应用访问者时， 这些信息可能会有所帮助。

> [!danger|label:缺点]
>
> - 每次在元素层次结构中添加或移除一个类时， 你都要更新所有的访问者。
> - 在访问者同某个元素进行交互时， 它们可能没有访问元素私有成员变量和方法的必要权限。

## 与其他模式的关系

- 你可以将[访问者模式](https://refactoringguru.cn/design-patterns/visitor)视为[命令模式](https://refactoringguru.cn/design-patterns/command)的加强版本， 其对象可对不同类的多种对象执行操作。
- 你可以使用[访问者](https://refactoringguru.cn/design-patterns/visitor)对整个[组合模式](https://refactoringguru.cn/design-patterns/composite)树执行操作。
- 可以同时使用[访问者](https://refactoringguru.cn/design-patterns/visitor)和[迭代器模式](https://refactoringguru.cn/design-patterns/iterator)来遍历复杂数据结构， 并对其中的元素执行所需操作， 即使这些元素所属的类完全不同。
