# Java中正则表达式的使用

# 1、正则表达式介绍

1、英文句点`.`符号：匹配单个任意字符。

- 表达式`t.o` 可以匹配：`tno`，`t#o`，`teo`等等。不可以匹配：`tnno`，`to`，`Tno`，`t正o`等。

2、中括号`[]`：只有方括号里面指定的字符才参与匹配，也只能匹配单个字符。

- 表达式：`t[abcd]n` 只可以匹配：`tan`，`tbn`，`tcn`，`tdn`。不可以匹配：`thn`，`tabn`，`tn`等。

3、`|` 符号。相当与“或”，可以匹配指定的字符，但是也只能选择其中一项进行匹配。

- 表达式：`t(a|b|c|dd)n` 只可以匹配：`tan`，`tbn`，`tcn`，`tddn`。不可以匹配`taan`，`tn`，`tabcn`等。

4、表示匹配次数的符号：

|  符号   |    次数     |
| :-----: | :---------: |
|   `*`   | 0次或者多次 |
|   `+`   | 1次或者多次 |
|   `?`   | 0次或者1次  |
|  `{n}`  |   恰好n次   |
| `{n,m}` | 从n次到m次  |

- 表达式：`[0—9]{ 3 } \— [0-9]{ 2 } \— [0-9]{ 3 }` 的匹配格式为：`999—99—999`
  因为`—`符号在正则表达式中有特殊的含义，它表示一个范围，所以在前面加转义字符`\`。

5、`^`符号：表示否，如果用在方括号内，`^`表示不想匹配的字符。

- 表达式：`[^x]` 第一个字符不能是x

6、`\S`符号：非空字符
7、`\s`符号：空字符，只可以匹配一个空格、制表符、回车符、换页符，不可以匹配自己输入的多个空格。
8、`\r`符号：空格符，与`\n`、`\tab`相同

> 有关`\`：

在其他语言中，`\\` 表示：**我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义。**

在 Java 中，`\\` 表示：**我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义。**

所以，在其他的语言中（如 Perl），一个反斜杠 `\` 就足以具有转义的作用，而在 Java 中正则表达式中则需要有两个反斜杠才能被解析为其他语言中的转义作用。也可以简单的理解在 Java 的正则表达式中，两个反斜杠 `\\` 代表其他语言中的一个 `\`，这也就是为什么表示一位数字的正则表达式是 `\\d`，而表示一个普通的反斜杠是 `\\`。

```java
System.out.print("\\");    // 输出为 \
System.out.print("\\\\");  // 输出为 \\
```

> **快捷符号**

|  字符  | 说明                                                         |
| :----: | :----------------------------------------------------------- |
|  `\b`  | 匹配一个字边界，即字与空格间的位置。例如，"er\b"匹配"never"中的"er"，但不匹配"verb"中的"er"。 |
|  `\B`  | 非字边界匹配。"er\B"匹配"verb"中的"er"，但不匹配"never"中的"er"。 |
| `\cx`  | 匹配 *x* 指示的控制字符。例如，\cM 匹配 Control-M 或回车符。*x* 的值必须在 A-Z 或 a-z 之间。如果不是这样，则假定 c 就是"c"字符本身。 |
|  `\d`  | 数字字符匹配。等效于 [0-9]。                                 |
|  `\D`  | 非数字字符匹配。等效于 `[^0-9]`。                            |
|  `\f`  | 换页符匹配。等效于 \x0c 和 \cL。                             |
|  `\n`  | 换行符匹配。等效于 \x0a 和 \cJ。                             |
|  `\r`  | 匹配一个回车符。等效于 \x0d 和 \cM。                         |
|  `\s`  | 匹配任何空白字符，包括空格、制表符、换页符等。与 [ \f\n\r\t\v] 等效。 |
|  `\S`  | 匹配任何非空白字符。与 `[^ \f\n\r\t\v]` 等效。               |
|  `\t`  | 制表符匹配。与 \x09 和 \cI 等效。                            |
|  `\v`  | 垂直制表符匹配。与 \x0b 和 \cK 等效。                        |
|  `\w`  | 匹配任何字类字符，包括下划线。与"[A-Za-z0-9_]"等效。         |
|  `\W`  | 与任何非单词字符匹配。与"`[^A-Za-z0-9_]`"等效。              |
| `\xn`  | 匹配 n，此处的 n 是一个十六进制转义码。十六进制转义码必须正好是两位数长。例如，"\x41"匹配"A"。"\x041"与"\x04"&"1"等效。允许在正则表达式中使用 ASCII 代码。 |
| `\num` | 匹配 num，此处的 num 是一个正整数。到捕获匹配的反向引用。例如，"(.)\1"匹配两个连续的相同字符。 |
|  `\n`  | 标识一个八进制转义码或反向引用。如果 \n 前面至少有 n 个捕获子表达式，那么 n 是反向引用。否则，如果 n 是八进制数 (0-7)，那么 n 是八进制转义码。 |
| `\nm`  | 标识一个八进制转义码或反向引用。如果 \nm 前面至少有 nm 个捕获子表达式，那么 nm 是反向引用。如果 \nm 前面至少有 n 个捕获，则 n 是反向引用，后面跟有字符 m。如果两种前面的情况都不存在，则 \nm 匹配八进制值 nm，其中 n 和 m 是八进制数字 (0-7)。 |
| `\nml` | 当 n 是八进制数 (0-3)，*m* 和 l 是八进制数 (0-7) 时，匹配八进制转义码 nml。 |
| `\un`  | 匹配 n，其中 *n* 是以四位十六进制数表示的 Unicode 字符。例如，\u00A9 匹配版权符号 (©)。 |

[常用正则表达式大全](https://blog.csdn.net/ZYC88888/article/details/98479629?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165113508016782425117495%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165113508016782425117495&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-98479629.142^v9^pc_search_result_cache,157^v4^control&utm_term=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1018.2226.3001.4187)

# 2、Java中的正则表达式

## String常用的正则表达式方法

1、判断功能

`public boolean matches(String regex)`：判断调用该方法的字符串是否与正则表达式匹配，一旦不匹配就返回false，否则返回true

案例：判断录入的手机号是否为13或者18开头

```java
package Lemon;

import java.util.Scanner;

public class RegexDm {
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入手机好：");
        String s = sc.nextLine();

        String regex = "1[38]\\d{9}";//定义手机好规则
        boolean flag = s.matches(regex);//判断功能
        System.out.println("flag:"+flag);
    }
}
```

2、分割功能

`public String[] split(String regex)`：将调用该方法的字符串按照正则表达式进行分割，返回分割出来的字符串数组

```java
package Lemon;

import java.util.Scanner;

public class RegexDm {
    public static void main(String[] args){
        String age = "18-24";//定义年龄范围
        String regex = "-";
        String[] strArr = age.split(regex);//分割成字符串数组

        int startAge = Integer.parseInt(strArr[0]);
        int endAge = Integer.parseInt(strArr[1]);

        Scanner sc = new Scanner(System.in);
        System.out.println("请输入您的年龄：");
        int a = sc.nextInt();
        if (a >= startAge && a <= endAge){
            System.out.println("你就是我想找的");
        }else{
            System.out.println("滚");
        }
    }
}
```

3、替换功能

`public String replaceAll(String regex,String replacement)`：将调用该方法的字符串用replacement字符按照正则表达式进行替换

```java
package Lemon;

public class RegexDm {
    public static void main(String[] args){
        String s = "12342jasfkgnas234";
        //把字符串里面的数字替换成*
        String regex = "\\d";
        String ss = "*";
        String result = s.replaceAll(regex,ss);
        System.out.println(result);
    }
}
```

## Java中的正则表达式体系

`java.util.regex` 包主要包括以下三个类：

- Pattern 类：

  pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法（compile()），它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。

- Matcher 类：

  Matcher 对象是对输入字符串进行解释和匹配操作的引擎。与Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。

- PatternSyntaxException：

  PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。

以下实例中使用了正则表达式 **.\*runoob.\*** 用于查找字符串中是否包了 **runoob** 子串：

```java
import java.util.regex.*;
 
class RegexExample1{
   public static void main(String[] args){
      String content = "I am noob " +
        "from runoob.com.";
 
      String pattern = ".*runoob.*";
 
      boolean isMatch = Pattern.matches(pattern, content);
      System.out.println("字符串中是否包含了 'runoob' 子字符串? " + isMatch);
   }
}

运行结果：

字符串中是否包含了 'runoob' 子字符串? true
```

## 捕获组

捕获组是把多个字符当一个单独单元进行处理的方法，它通过对括号内的字符分组来创建。

例如，正则表达式 (dog) 创建了单一分组，组里包含"d"，"o"，和"g"。

捕获组是通过从左至右计算其开括号来编号。例如，在表达式（（A）（B（C））），有四个这样的组：

- ((A)(B(C)))
- (A)
- (B(C))
- (C)

可以通过调用 matcher 对象的 groupCount 方法来查看表达式有多少个分组。groupCount 方法返回一个 int 值，表示matcher对象当前有多个捕获组。

还有一个特殊的组（group(0)），它总是代表整个表达式。该组不包括在 groupCount 的返回值中。

可以通过`matcher.find()`返回的布尔值判断，只要上面的一个捕获组匹配到东西就返回true，然后可以根据`matcher.group(int group)`来查看该组所匹配到的内容。

## 实例

```java
package com.llh;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * @ClassName: RegexTest
 * @author: Maybe
 * @date: 2022/4/28  17:16
 */
public class RegexTest {
    public static void main(String[] args) {
        // 按指定模式在字符串查找
        String line = "This order was placed for QT3000! OK?";
        String regex = "(\\D*)(\\d+)(.*)";
        // 创建 pattern 对象
        Pattern pattern = Pattern.compile(regex);
        // 现在创建 matcher 对象
        Matcher matcher = pattern.matcher(line);
        if (matcher.find()) {
            System.out.println("Found value: " + matcher.group(0));
            System.out.println("Found value: " + matcher.group(1));
            System.out.println("Found value: " + matcher.group(2));
            System.out.println("Found value: " + matcher.group(3));
        } else {
            System.out.println("NO MATCH");
        }
    }
}

运行结果：

Found value: This order was placed for QT3000! OK?
Found value: This order was placed for QT
Found value: 3000
Found value: ! OK?    
```

> 参考文献：

[Java正则表达式](https://www.runoob.com/java/java-regular-expressions.html)

