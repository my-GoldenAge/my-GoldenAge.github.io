# Builder 模式

大都市中林立着许多高楼大厦，这些高楼大厦都是具有建筑结构的大型建筑。通常，建造和构建这种具有建筑结构的大型物体在英文中称为Build。

在建造大楼时，需要先打牢地基，搭建框架，然后自下而上地一层一层盖起来。通常，在建造这种具有复杂结构的物体时，很难一气呵成。我们需要首先建造组成这个物体的各个部分，然后分阶段将它们组装起来。

在本章中，我们将要学习用于组装具有复杂结构的实例的**Builder模式**。

# 示例程序

作为示例程序，我们来看一段使用Builder模式编写“文档”的程序。这里编写出的文档具有以下结构。

- **含有一个标题**
- **含有几个字符串**
- **含有条目项目**

Builder类中定义了决定文档结构的方法，然后Director类使用该方法编写一个具体的文档。

Builder是抽象类，它并没有进行任何实际的处理，仅仅声明了抽象方法。Builder类的子类决定了用来编写文档的具体处理。

在示例程序中，我们定义了以下Builder类的子类。

- **TextBuilder类：使用纯文本（普通字符串）编写文档**
- **HTMLBuilder类：使用HTML编写文档**

Director使用TextBuilder类时可以编写纯文本文档；使用HTMLBuilder类时可以编写HTML文档。

> 类的一览

![image-20220514151410150](image/image-20220514151410150.png ":size=80%")

> 示例程序类图

![image-20220516141737342](image/image-20220516141737342.png ":size=75%")

## Builder类

Builder类是一个声明了编写文档的方法的抽象类。makeTitle、makeString、makeTimes方法分别是编写标题、字符串、条目的方法。close方法是完成文档编写的方法。

```java
public abstract class Builder {
    public abstract void makeTitle(String title);
    public abstract void makestring(String str);
    public abstract void makeItems(String[] items);
    public abstract void close();
}
```

## Director类

Director类使用Builder类中声明的方法来编写文档。

Director类的构造函数的参数是Builder类型的。但是实际上我们并不会将Builder类的实例作为参数传递给Director类。这是因为Builder类是抽象类，是无法生成其实例的。实际上传递给Director类的是Builder类的子类（即后面会讲到的TextBuilder类和
HTMLBuilder类等)的实例。而正是这些Builder类的子类决定了编写出的文档的形式。

construct方法是编写文档的方法。调用这个方法后就会编写文档。construct方法中所使用的方法都是在Builder类中声明的方法(construct的意思是“构建”)。

![image-20220516142508485](image/image-20220516142508485.png ":size=80%")

## TextBuilder类

TextBuilder类是Builder类的子类，它的功能是使用纯文本编写文档，并以String返回结果。

![image-20220516142637624](image/image-20220516142637624.png ":size=80%")

## HTMLBuilder类

HTMLBuilder类也是Builder类的子类，它的功能是使用HTML编写文档，其返回结果是HTML文件的名字。

![image-20220516142817251](image/image-20220516142817251.png ":size=80%")

## Main类

Main类是Builder模式的测试程序。我们可以使用如下的命令来编写相应格式的文档：

- **java Main plain：编写纯文本文档**
- **java Main html：编写HTML格式的文档**

当我们在命令行中指定参数为plain的时候，会将TextBuilder类的实例作为参数传递至Director类的构造函数中；而若是在命令行中指定参数为html的时候，则会将HTMLBuilder类的实例作为参数传递至Director类的构造函数中。

由于TextBuilder和HTMLBuilder都是Builder的子类，因此Director仅仅使用Builder的方法即可编写文档。也就是说，Director并不关心实际编写文档的到底是TextBuilder还是HTMLBuilder。

正因为如此，我们必须在Builder中声明足够多的方法，以实现编写文档的功能，但并不包括TextBuilder和HTMLBuilder中特有的方法。

```java
public class Main {
    public static void main(String[]args){
        if(args.length != 1){
            usage();
            System.exit(0);
        }
        if (args[0].equals("plain")){
            TextBuilder textbuilder new TextBuilder();
            Directordirector new Director(textbuilder);
            director.construct();
            String result textbuilder.getResult();
            System.out.println(result);
        }else if (args[0].equals("html")){
            HTMLBuilder htmlbuilder new HTMLBuilder();
            Director director new Director(htmlbuilder);
            director.construct();
            String filename htmlbuilder.getResult();
            System.out,println(filename+"文件编写完成。");
        }else{
            usage();
            System.exit(0);
        }
    }
    
    public static void usage(){
        System.out.println("Usage:java Main plain	编写纯文本文档");
        System.out.println("Usage:java Main html	编写HTML文档");
    }
}
```

> 运行结果（纯文本文档）

 ![image-20220516143454888](image/image-20220516143454888.png ":size=40%")

> 运行结果（HTML文档）

 ![image-20220516143613349](image/image-20220516143613349.png ":size=50%")

![image-20220516144146947](image/image-20220516144146947.png ":size=70%")

# Builder模式中的登场角色

- **Builder(建造者)**

  Builder角色负责定义用于生成实例的接口(API)。Builder角色中准备了用于生成实例的方法。在示例程序中，由Builder类扮演此角色。

- **ConcreteBuilder(具体的建造者)**

  ConcreteBuilder角色是负责实现Builder角色的接口的类(API)。这里定义了在生成实例时实际被调用的方法。此外，在ConcreteBuilder角色中还定义了获取最终生成结果的方法。在示例程序中，由TextBuilder类和HTMLBuilder类扮演此角色。

- **Director(监工)**

  Director角色负责使用Builder角色的接口(API)来生成实例。它并不依赖于ConcreteBuilder角色。为了确保不论ConcreteBuilder角色是如何被定义的，Director角色都能正常工作，它**只调用在Builder角色中被定义的方法**。在示例程序中，由Director类扮演此角色。

- **Client(使用者)**

  该角色使用了Builder模式（在GoF的书中，Builder模式并不包含Client角色)。在示例程序中，由Main类扮演此角色。

> Builder模式类图

![image-20220516221601165](image/image-20220516221601165.png ":size=70%") 

> Builder模式的时序图

![image-20220516223533451](image/image-20220516223533451.png ":size=70%")

