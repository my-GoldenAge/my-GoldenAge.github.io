# jQuery操作HTML

# jQuery - 获得内容和属性

**jQuery 拥有可操作 HTML 元素和属性的强大方法。**

## jQuery DOM 操作

jQuery 中非常重要的部分，就是操作 DOM 的能力。

jQuery 提供一系列与 DOM 相关的方法，这使访问和操作元素和属性变得很容易。

DOM 定义访问 HTML 和 XML 文档的标准：

“W3C 文档对象模型独立于平台和语言的界面，允许程序和脚本动态访问和更新文档的内容、结构以及样式。”

## 获得内容 - text()、html() 以及 val()

三个简单实用的用于 DOM 操作的 jQuery 方法：

- text() - 设置或返回所选元素的文本内容
- html() - 设置或返回所选元素的内容（包括 HTML 标记）
- val() - 设置或返回表单字段的值

下面的例子演示如何通过 jQuery text() 和 html() 方法来获得内容：

**实例**

```js
$("#btn1").click(function(){
    alert("Text: " + $("#test").text());
});
$("#btn2").click(function(){
    alert("HTML: " + $("#test").html());
});
```

下面的例子演示如何通过 jQuery val() 方法获得输入字段的值：

**实例**

```js
$("#btn1").click(function(){
    alert("Value: " + $("#test").val());
});
```

## 获取属性 - attr()

jQuery attr() 方法用于获取属性值。

下面的例子演示如何获得链接中 href 属性的值：

### 实例

```js
$("button").click(function(){
    alert($("#w3s").attr("href"));
});
```

# jQuery - 设置内容和属性

## 设置内容 - text()、html() 以及 val()

我们将使用前一章中的三个相同的方法来设置内容：

- text() - 设置或返回所选元素的文本内容
- html() - 设置或返回所选元素的内容（包括 HTML 标记）
- val() - 设置或返回表单字段的值

下面的例子演示如何通过 text()、html() 以及 val() 方法来设置内容：

**实例**

```js
$("#btn1").click(function(){
    $("#test1").text("Hello world!");
});
$("#btn2").click(function(){
    $("#test2").html("<b>Hello world!</b>");
});
$("#btn3").click(function(){
    $("#test3").val("Dolly Duck");
});
```

## text()、html() 以及 val() 的回调函数

上面的三个 jQuery 方法：text()、html() 以及 val()，同样拥有回调函数。回调函数由两个参数：被选元素列表中当前元素的下标，以及原始（旧的）值。然后以函数新值返回您希望使用的字符串。

下面的例子演示带有回调函数的 text() 和 html()：

**实例**

```js
$("#btn1").click(function(){
    $("#test1").text(function(i,origText){
        return "Old text: " + origText + " New text: Hello world!
        (index: " + i + ")";
    });
});

$("#btn2").click(function(){
    $("#test2").html(function(i,origText){
        return "Old html: " + origText + " New html: Hello <b>world!</b>
        (index: " + i + ")";
    });
});
```

## 设置属性 - attr()

jQuery attr() 方法也用于设置/改变属性值。

下面的例子演示如何改变（设置）链接中 href 属性的值：

**实例**

```js
$("button").click(function(){
    $("#w3s").attr("href","http://www.nowcoder.com/jquery");
});
```

attr() 方法也允许您同时设置多个属性。

下面的例子演示如何同时设置 href 和 title 属性：

**实例**

```js
$("button").click(function(){
    $("#w3s").attr({
        "href" : "http://www.nowcoder.com/jquery",
        "title" : "nowcoder jQuery Tutorial"
    });
});
```

## attr() 的回调函数

jQuery 方法 attr()，也提供回调函数。回调函数由两个参数：被选元素列表中当前元素的下标，以及原始（旧的）值。然后以函数新值返回您希望使用的字符串。

下面的例子演示带有回调函数的 attr() 方法：

**实例**

```js
$("button").click(function(){
    $("#w3s").attr("href", function(i,origValue){
        return origValue + "/jquery";
    });
});
```

# jQuery - 添加元素

**通过 jQuery，可以很容易地添加新元素/内容。**

## 添加新的 HTML 内容

我们将学习用于添加新内容的四个 jQuery 方法：

- append() - 在被选元素的结尾插入内容
- prepend() - 在被选元素的开头插入内容
- after() - 在被选元素之后插入内容
- before() - 在被选元素之前插入内容

## jQuery append() 方法

jQuery append() 方法在被选元素的结尾插入内容。

**实例**

```js
$("p").append("Some appended text.");
```

## jQuery prepend() 方法

jQuery prepend() 方法在被选元素的开头插入内容。

**实例**

```js
$("p").prepend("Some prepended text.");
```

## 通过 append() 和 prepend() 方法添加若干新元素

在上面的例子中，我们只在被选元素的开头/结尾插入文本/HTML。

不过，append() 和 prepend() 方法能够通过参数接收无限数量的新元素。可以通过 jQuery 来生成文本/HTML（就像上面的例子那样），或者通过 JavaScript 代码和 DOM 元素。

在下面的例子中，我们创建若干个新元素。这些元素可以通过 text/HTML、jQuery 或者 JavaScript/DOM 来创建。然后我们通过 append() 方法把这些新元素追加到文本中（对 prepend() 同样有效）：

**实例**

```js
function appendText(){
    var txt1 = "<p>Text.</p>";               // 以 HTML 创建新元素
    var txt2 = $("<p></p>").text("Text.");   // 以 jQuery 创建新元素
    var txt3 = document.createElement("p");  // 以 DOM 创建新元素
    txt3.innerHTML = "Text.";
    $("p").append(txt1,txt2,txt3);         // 追加新元素
}
```

## jQuery after() 和 before() 方法

jQuery after() 方法在被选元素之后插入内容。

jQuery before() 方法在被选元素之前插入内容。

**实例**

```js
$("img").after("Some text after");

$("img").before("Some text before");
```

## 通过 after() 和 before() 方法添加若干新元素

after() 和 before() 方法能够通过参数接收无限数量的新元素。可以通过 text/HTML、jQuery 或者 JavaScript/DOM 来创建新元素。

在下面的例子中，我们创建若干新元素。这些元素可以通过 text/HTML、jQuery 或者 JavaScript/DOM 来创建。然后我们通过 after() 方法把这些新元素插到文本中（对 before() 同样有效）：

**实例**

```js
function afterText(){
    var txt1 = "<b>I </b>";                    // 以 HTML 创建新元素
    var txt2 = $("<i></i>").text("love ");     // 通过 jQuery 创建新元素
    var txt3 = document.createElement("big");  // 通过 DOM 创建新元素
    txt3.innerHTML = "jQuery!";
    $("img").after(txt1,txt2,txt3);          // 在 img 之后插入新元素
}
```

# jQuery - 删除元素

**通过 jQuery，可以很容易地删除已有的 HTML 元素。**

## 删除元素/内容

如需删除元素和内容，一般可使用以下两个 jQuery 方法：

- remove() - 删除被选元素（及其子元素）
- empty() - 从被选元素中删除子元素

## jQuery remove() 方法

jQuery remove() 方法删除被选元素及其子元素。

**实例**

```js
$("#div1").remove();
```

## jQuery empty() 方法

jQuery empty() 方法删除被选元素的子元素。

**实例**

```js
$("#div1").empty();
```

## 过滤被删除的元素

jQuery remove() 方法也可接受一个参数，允许您对被删元素进行过滤。

该参数可以是任何 jQuery 选择器的语法。

下面的例子删除 class="italic" 的所有 `<p>` 元素：

**实例**

```js
$("p").remove(".italic");
```

# jQuery - 获取并设置 CSS 类

**通过 jQuery，可以很容易地对 CSS 元素进行操作。**

## jQuery 操作 CSS

jQuery 拥有若干进行 CSS 操作的方法。我们将学习下面这些：

- addClass() - 向被选元素添加一个或多个类
- removeClass() - 从被选元素删除一个或多个类
- toggleClass() - 对被选元素进行添加/删除类的切换操作
- css() - 设置或返回样式属性

## 实例样式表

下面的样式表将用于本页的所有例子：

```css
.important{
    font-weight:bold;
    font-size:xx-large;
}

.blue{
    color:blue;
}
```

## jQuery addClass() 方法

下面的例子展示如何向不同的元素添加 class 属性。当然，在添加类时，您也可以选取多个元素：

**实例**

```js
$("button").click(function(){
    $("h1,h2,p").addClass("blue");
    $("div").addClass("important");
});
```

您也可以在 addClass() 方法中规定多个类：

**实例**

```js
$("button").click(function(){
    $("#div1").addClass("important blue");
});
```

## jQuery removeClass() 方法

下面的例子演示如何不同的元素中删除指定的 class 属性：

**实例**

```js
$("button").click(function(){
    $("h1,h2,p").removeClass("blue");
});
```

## jQuery toggleClass() 方法

下面的例子将展示如何使用 jQuery toggleClass() 方法。该方法对被选元素进行添加/删除类的切换操作：

**实例**

```js
$("button").click(function(){
    $("h1,h2,p").toggleClass("blue");
});
```

# jQuery - css() 方法

## jQuery css() 方法

css() 方法设置或返回被选元素的一个或多个样式属性。

## 返回 CSS 属性

如需返回指定的 CSS 属性的值，请使用如下语法：

```js
css("propertyname");
```

下面的例子将返回首个匹配元素的 background-color 值：

**实例**

```js
$("p").css("background-color");
```

## 设置 CSS 属性

如需设置指定的 CSS 属性，请使用如下语法：

```js
css("propertyname","value");
```

下面的例子将为所有匹配元素设置 background-color 值：

**实例**

```js
$("p").css("background-color","yellow");
```

## 设置多个 CSS 属性

如需设置多个 CSS 属性，请使用如下语法：

```js
css({"propertyname":"value","propertyname":"value",...});
```

下面的例子将为所有匹配元素设置 background-color 和 font-size：

**实例**

```js
$("p").css({"background-color":"yellow","font-size":"200%"});
```

# jQuery - 尺寸

**通过 jQuery，很容易处理元素和浏览器窗口的尺寸。**

## jQuery 尺寸 方法

jQuery 提供多个处理尺寸的重要方法：

- width()
- height()
- innerWidth()
- innerHeight()
- outerWidth()
- outerHeight()

## jQuery width() 和 height() 方法

width() 方法设置或返回元素的宽度（不包括内边距、边框或外边距）。

height() 方法设置或返回元素的高度（不包括内边距、边框或外边距）。

下面的例子返回指定的 `<div>` 元素的宽度和高度：

**实例**

```js
$("button").click(function(){
    var txt="";
    txt+="Width: " + $("#div1").width() + "</br>";
    txt+="Height: " + $("#div1").height();
    $("#div1").html(txt);
});
```

## jQuery innerWidth() 和 innerHeight() 方法

innerWidth() 方法返回元素的宽度（包括内边距）。

innerHeight() 方法返回元素的高度（包括内边距）。

下面的例子返回指定的 `<div>` 元素的 inner-width/height：

**实例**

```js
$("button").click(function(){
    var txt="";
    txt+="Inner width: " + $("#div1").innerWidth() + "</br>";
    txt+="Inner height: " + $("#div1").innerHeight();
    $("#div1").html(txt);
});
```

## jQuery outerWidth() 和 outerHeight() 方法

outerWidth() 方法返回元素的宽度（包括内边距和边框）。

outerHeight() 方法返回元素的高度（包括内边距和边框）。

下面的例子返回指定的 `<div>` 元素的 outer-width/height：

**实例**

```js
$("button").click(function(){
    var txt="";
    txt+="Outer width: " + $("#div1").outerWidth() + "</br>";
    txt+="Outer height: " + $("#div1").outerHeight();
    $("#div1").html(txt);
});
```

outerWidth(true) 方法返回元素的宽度（包括内边距、边框和外边距）。

outerHeight(true) 方法返回元素的高度（包括内边距、边框和外边距）。

**实例**

```js
$("button").click(function(){
    var txt="";
    txt+="Outer width (+margin): " + $("#div1").outerWidth(true) + "</br>";
    txt+="Outer height (+margin): " + $("#div1").outerHeight(true);
    $("#div1").html(txt);
});
```

## jQuery - 更多的 width() 和 height()

下面的例子返回文档（HTML 文档）和窗口（浏览器视口）的宽度和高度：

**实例**

```js
$("button").click(function(){
    var txt="";
    txt+="Document width/height: " + $(document).width();
    txt+="x" + $(document).height() + "\n";
    txt+="Window width/height: " + $(window).width();
    txt+="x" + $(window).height();
    alert(txt);
});
```

下面的例子设置指定的 **<div>** 元素的宽度和高度：

**实例**

```js
$("button").click(function(){
    $("#div1").width(500).height(500);
});
```

