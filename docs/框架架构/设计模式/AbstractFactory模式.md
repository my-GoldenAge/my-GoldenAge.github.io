# Abstract Factory 模式

Abstract的意思是“抽象的”，Factory的意思是“工厂”。将它们组合起来我们就可以知道Abstract Factory表示“抽象工厂”的意思。

通常，我们不会将“抽象的”这个词与“工厂”这个词联系到一起。所谓工厂，是将零件组装成产品的地方，这是一项具体的工作。那么“抽象工厂”到底是什么意思呢？

我们大可不必对这个词表示吃惊。因为在Abstract Factory模式中，不仅有“抽象工厂”，还有“抽象零件”和“抽象产品”。**抽象工厂的工作是将“抽象零件”组装为“抽象产品”。**

读到这里，大家可能会想“哎呀哎呀，你到底想说什么啊？”那么请大家先回忆一下面向对象编程中的“抽象”这个词的具体含义。它指的是“不考虑具体怎样实现，而是仅关注接口(API)”的状态。例如，抽象方法（Abstract Method)并不定义方法的具体实现，而是仅仅只确定了方法的名字和签名（参数的类型和个数)。

关于“忘记方法的具体实现（假装忘记），使用抽象方法进行编程”的设计思想，我们在Template Method模式和Builder模式中已经稍微提及了一些。

在Abstract Factory模式中将会出现抽象工厂，它会将抽象零件组装为抽象产品。**也就是说，我们并不关心零件的具体实现，而是只关心接口(API)。我们仅使用该接口(API)将零件组装成为产品。**

在Tempate Method模式和Builder模式中，子类这一层负责方法的具体实现。在AbstractFactory模式中也是一样的。在子类这一层中有具体的工厂，它负责将具体的零件组装成为具体的产品。

# 示例程序

示例程序的功能是将带有层次关系的链接的集合制作成HTML文件。最后制作完成的HTML文件如图所示。

![image-20220510214114752](image/image-20220510214114752.png ":size=70%")

在示例程序中，类被划分为以下3个包。

- **factory包：包含抽象工厂、零件、产品的包**
- **无名包：包含Main类的包**
- **listfactory包：包含具体工厂、零件、产品的包（这里使用`<u>`标签输出为L文件）**

> 类的一览表

![image-20220510214410168](image/image-20220510214410168.png ":size=85%")

> 实例程序类图

![image-20220510214534243](image/image-20220510214534243.png ":size=75%")

> 文件夹中源文件的结构

 ![image-20220510214700102](image/image-20220510214700102.png ":size=30%")

## 抽象的零件：ltem类

Item类是工ink类和Tray类的父类(Item有“项目”的意思)。这样，Link类和Tray类就具有可替换性了。

caption字段表示项目的“标题”。

makeHTML方法是抽象方法，需要子类来实现这个方法。该方法会返回HTML文件的内容（需要子类去实现)。

```java
package factory;

public abstract class Item {
    protected String caption;
    public Item(String caption){
        this.captioncaption;
    }
    public abstract string makeHTML();
}
```

## 抽象的零件：Link类

Link类是抽象地表示HTML的超链接的类。

url字段中保存的是超链接所指向的地址。乍一看，在工ink类中好像一个抽象方法都没有，但实际上并非如此。由于Link类中没有实现父类(Item类)的抽象方法(makeHTML)，因此它也是抽象类。

```java
package factory;

public abstract class Link extends Item {
    protected String url;
    public Link(String caption,String url){
        super(caption);
        this.url url;
    }
}
```

## 抽象的零件：Tray类

Tray类表示的是一个含有多个工ink类和Tray类的容器(Tray有托盘的意思。请想象成在托盘上放置着一个一个项目)。

Tray类使用add方法将Link类和Tray类集合在一起。为了表示集合的对象是“Link类和Tray类”，我们设置add方法的参数为Link类和Tray类的父类Item类。

虽然Tray类也继承了Item类的抽象方法makeHTML,但它并没有实现该方法。因此，Tray类也是抽象类。

```java
package factory;

import java.util.ArrayList;

public abstract class Tray extends Item {
    protected ArrayList tray new ArrayList();
    public Tray(String caption){
        super(caption);
    }
    public void add(Itemitem){
        tray.add(item);
    }
}
```

## 抽象的产品：Page类

Page类是抽象地表示HTML页面的类。如果将工ink和Tray比喻成抽象的“零件”，那么Page类就是抽象的“产品”。title和author分别是表示页面标题和页面作者的字段。作者名字通过参数传递给Page类的构造函数。

可以使用add方法向页面中增加Item(即Link或Tray。增加的Item将会在页面中显示出来。

output方法首先根据页面标题确定文件名，接着调用makeHTML方法将自身保存的HTML内容写人到文件中。

其中，我们可以去掉如下语句(1)中的this，将其写为如下语句(2)那样：

```java
writer.write (this.makeHTML()) //(1)
writer.write (makeHTML()); //(2)
```

为了强调调用的是Page类自己的makeHTML方法，我们显式地加上了this。这里调用的makeHTML方法是一个抽象方法。output方法是一个简单的Template Method模式的方法。

```java
package factory;

import java.io.*
    import java.util.ArrayList;

public abstract class Page {
    protected String title;
    protected String author;
    protected ArrayList content new ArrayList ();
    public Page(String title,String author){
        this.title title;
        this.authorauthor;
    }
    public void add(Itemitem){
        content.add(item);
    }
    public void output(){
        try{
            String filename title ".html";
            Writer writer new FileWriter(filename);
            writer.write(this.makeHTML());
            writer.close();
            System.out,println(filename+"编写完成。");
        }catch (IOException e){
            e.printstackTrace();
        }
    }
    public abstract string makeHTML();
}
```

## 抽象的工厂：Factory类

前面我们学习了抽象零件和抽象产品的代码，现在终于可以来看看抽象工厂了。getFactory方法可以根据指定的类名生成具体工厂的实例。例如，可以像下面这样，将参数classname指定为具体工厂的类名所对应的字符串。

getFactory方法通过调用Class类的forName方法来动态地读取类信息，接着使用newInstance方法生成该类的实例，并将其作为返回值返回给调用者。

class类属于java.lang包，是用来表示类的类。class类包含于Java的标准类库中。forName是java.lang.Class的类方法（静态方法），newInstance则是java.lang.class的实例方法。

请注意，虽然getFactory方法生成的是具体工厂的实例，但是返回值的类型是抽象工厂类型。

createLink、createTray、createPage等方法是用于在抽象工厂中生成零件和产品的方法。这些方法都是抽象方法，具体的实现被交给了Factory类的子类。不过，这里确定了方法的名字和签名。

```java
package factory;

public abstract class Factory {
    public static Factory getFactory(String classname){
        Factory factory = null;
        try{
            factory =(Factory)class.forName (classname).newInstance();
        }catch (ClassNotFoundException e){
            System.err.println("没有找到"+classname+"类。");
        }catch (Exception e){
            e.printstackTrace();
        }
        return factory;
    }
    public abstract Link createLink(String caption,String url);
    public abstract Tray createTray(String caption);
    public abstract Page createpage(String title,String author);
}
```

## 使用工厂将零件组装称为产品：Main类

在理解了抽象的零件、产品、工厂的代码后，我们来看看Main类的代码。Main类使用抽象工厂生产零件并将零件组装成产品。Main类中只引人了factory包，从这一点可以看出，该类并没有使用任何具体零件、产品和工厂。

具体工厂的类名是通过命令行来指定的。例如，如果要使用listfactory包中的ListFactory类，可以在命令行中输入以下命令。

> java Main listfactory.ListFactory

Main类会使用getFactory方法生成该参数(arg[0])对应的工厂，并将其保存在factory变量中。

之后，Main类会使用factory生成Link和Tray，然后将Link和Tray都放入Tray中，最后生成Page并将生成结果输出至文件。

```java
import factory.*;

public class Main {
    public static void main(String[]args){
        if (args.length !1){
            System.out.println("Usage:java Main class.name.of.ConcreteFactory");
            System.out.println("Example 1:java Main listfactory.ListFactory");
            System.out.println("Example 2:java Main tablefactory.TableFactory");
            System.exit(0);
        }
        Factory factory Factory.getFactory(args[0]);
        Link people=factory.createLink("人民日报"，"http://www,people.com.cn/");
        Link gmw=factory.createLink("光明日报"，"http://www.gmw.cn/");
        Link us yahoo factory.createLink ("Yahoo!""http://www.yahoo.com/");
        Link jp_yahoo factory.createLink ("Yahoo!Japan","http://www.yahoo.co.jp/");
        Link excite factory.createLink("Excite","http://www.excite.com/");
        Link google factory.createLink ("Google","http://www.google.com/");
        Tray traynews=factory.createTray("日报");
        traynews.add(people);
        traynews.add(gmw);
        Tray trayyahoo factory.createTray("Yahoo!");
        trayyahoo.add(us yahoo);
        trayyahoo.add(jp_yahoo);

        Tray traysearch=factory.createTray("检索引擎");
        traysearch.add(trayyahoo);
        traysearch.add(excite);
        traysearch.add(google);
        Page page=factory.createPage("LinkPage","杨文轩");
        page.add(traynews);
        page.add(traysearch);
        page.output();
    }
}
```

## 具体的工厂：ListFactory类

之前我们学习了抽象类的代码，现在让我们将视角切换到具体类。首先，我们来看看listfactory包中的工厂—ListFactory类。

ListFactory类实现了Factory类的createLink方法、createTray方法以及createPage方法。当然，各个方法内部只是分别简单地new出了ListLink类的实例、ListTray类的实例以及ListPage类的实例（根据实际需求，这里可能需要用Prototype模式来进行clone)。

```java
package listfactory;

import factory.*;

public class ListFactory extends Factory{
    public Link createLink(String caption,String url){
        return new ListLink(caption,url);
    }

    public Tray createTray(String caption){
        return new ListTray(caption);
    }
    public Page createpage(String title,String author){
        return new ListPage(title,author);
    }
}
```

## 具体的零件：ListLink类

ListLink类是Link类的子类。在工ist工ink类中必须实现的方法是哪个呢？对了，就是在父类中声明的makeHTML抽象方法。ListLink类使用`<li>`标签和`<a>`标签来制作HTML片段。这段HTML片段也可以与工istTary和ListPag的结果合并起来，就如同将螺栓和螺母拧在一起一样。

```java
package listfactory;

import factory.*;

public class ListLink extends Link {
    public ListLink(String caption,String url){
        super(caption,url);
    }
    public String makeHTML(){
        return "<li><a href=\"" + url +"\">" + caption + "</a></li>\n";
    }
}
```

## 具体的零件：ListTray类

ListTray类是Tray类的子类。这里我们重点看一下makeHTML方法是如何实现的。tray字段中保存了所有需要以HTML格式输出的Item,而负责将它们以HTML格式输出的就是makeHTML方法了。那么该方法究竟是如何实现的呢？

makeHTML方法首先使用`<li>`标签输出标题(caption)，接着使用`<u1>`和`<li>`标签输出每个Item。输出的结果先暂时保存在StringBuffer中，最后再通过toString方法将输出结果转换为String类型并返回给调用者。

那么，每个Item又是如何输出为HTML格式的呢？当然就是调用每个Item的makeHTM工方法了。请注意，这里并不关心变量item中保存的实例究竟是ListLink的实例还是ListTray的实例，只是简单地调用了item.makeHTML()语句而已。这里**不能使用switch语句或if语句去判断变量item中保存的实例的类型**，否则就是非面向对象编程了。变量item是Item类型的，而Item类又声明了makeHTML方法，而且ListLink类和ListTray类都是Item类的子类，因此可以放心地调用。之后item会帮我们进行处理。至于item究竟进行了什么样的处理只有item的实例（对象）才知道。这就是面向对象的优点。

这里使用的java.util.Iterator类与我们在Iterator模式一章中所学习的迭代器在功能上是相同的，不过它是Java类库中自带的。为了从java.util.ArrayList类中得到java.util.Iterator，我们调用iterator方法。

```java
package listfactory;

import factory.*;
import java.util.Iterator;

public class ListTray extends Tray {
    public ListTray(String caption){
        super(caption);
    }
    public String makeHTML (){
        StringBuffer buffer new StringBuffer();
        buffer.append ("<li>\n");
        buffer.append (caption +"\n");
        buffer.append ("<ul>\n");
        Iterator it tray.iterator();
        while (it.hasNext ()){
            Item item (Item)it.next();
            buffer.append (item.makeHTML());
        }
        buffer.append("</ul>\n");
        buffer.append ("</1i>\n");
        return buffer.tostring ()
    }
}
```

## 具体的产品：ListPage类

ListPage类是Page类的子类。关于makeHTML方法，大家应该已经明白了吧。ListPage将字段中保存的内容输出为HTML格式。作者名(author)用`<address>`标签输出。

大家知道为什么while语句被夹在`<u1>...</u1>`之间吗？这是因为在while语句中append的item.makeHTML()的输出结果需要被嵌入在`<ul>...</ul>`之间的缘故。请大家再回顾一下ListLink和ListTray的makeHTML()方法，在它们的最外侧都会有`<li>`标签，就像
是“螺栓”和“螺母”的接头一样。

while语句的上一条语句中的content继承自Page类的字段。

