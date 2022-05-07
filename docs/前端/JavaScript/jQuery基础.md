# jQuery

# jQuery简介

## jQuery 库 - 特性

jQuery 是一个 JavaScript 函数库。

jQuery 库包含以下特性：

- HTML 元素选取
- HTML 元素操作
- CSS 操作
- HTML 事件函数
- JavaScript 特效和动画
- HTML DOM 遍历和修改
- AJAX
- Utilities

## 向您的页面添加 jQuery 库

### 直接下载

jQuery 库位于一个 JavaScript 文件中，其中包含了所有的 jQuery 函数。

可以通过下面的标记把 jQuery 添加到网页中：（[先下载jQuery](https://www.jq22.com/jquery-info122)，共有两个版本的 jQuery 可供下载：一份是精简过的，另一份是未压缩的（供调试或阅读）。）

```html
<head>
    <script type="text/javascript" src="jquery.js"></script>
</head>
```

请注意，`<script>` 标签应该位于页面的 `<head>` 部分。

### 使用 Google 的 CDN

```html
<head>
    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs
                                        /jquery/1.4.0/jquery.min.js"></script>
</head>
```

### 使用 Microsoft 的 CDN

```html
<head>
    <script type="text/javascript" src="http://ajax.microsoft.com/ajax/jquery
                                        /jquery-1.4.min.js"></script>
</head>
```

许多用户在访问其他站点时，已经从谷歌或微软加载过 jQuery。所有结果是，当他们访问您的站点时，会从缓存中加载 jQuery，这样可以减少加载时间。同时，大多数 CDN 都可以确保当用户向其请求文件时，会从离用户最近的服务器上返回响应，这样也可以提高加载速度。

# jQuery 语法

**通过 jQuery，您可以选取（查询，query） HTML 元素，并对它们执行“操作”（actions）。**

## jQuery 语法

jQuery 语法是为 HTML 元素的选取编制的，可以对元素执行某些操作。

基础语法是：`$(selector).action()`

- 美元符号定义 jQuery
- 选择符（selector）“查询”和“查找” HTML 元素（其中**包含**CSS的选择器语法）
- jQuery 的 action() 执行对元素的操作

**示例：**

`$(this).hide()` - 隐藏当前元素

`$("p").hide()` - 隐藏所有段落

`$(".test").hide()` - 隐藏所有 class="test" 的所有元素

`$("#test").hide()` - 隐藏所有 id="test" 的元素

## 文档就绪函数

您也许已经注意到在我们的实例中的所有 jQuery 函数位于一个 document ready 函数中：

```js
$(document).ready(function(){

--- jQuery functions go here ----

});
```

这是为了防止文档在完全加载（就绪）之前运行 jQuery 代码。

如果在文档没有完全加载之前就运行函数，操作可能失败（所以建议就统一放在最后，`</body>`前后，反正jQuery代码也是js）。

# jQuery 选择器

**选择器允许您对元素组或单个元素进行操作。**

## jQuery 选择器

在前面的章节中，我们展示了一些有关如何选取 HTML 元素的实例。

关键点是学习 jQuery 选择器是如何准确地选取您希望应用效果的元素。

jQuery 元素选择器和属性选择器允许您通过标签名、属性名或内容对 HTML 元素进行选择。

选择器允许您对 HTML 元素组或单个元素进行操作。

在 HTML DOM 术语中：

选择器允许您对 DOM 元素组或单个 DOM 节点进行操作。

## jQuery 元素选择器

jQuery 使用 CSS 选择器来选取 HTML 元素。

`$("p")` 选取 `<p>` 元素。

`$("p.intro")` 选取所有 class="intro" 的 `<p>` 元素。

`$("p#demo")` 选取所有 id="demo" 的 `<p>` 元素。

## jQuery 属性选择器

jQuery 使用 XPath 表达式来选择带有给定属性的元素。

`$("[href]")` 选取所有带有 href 属性的元素。

`$("[href='#']")` 选取所有带有 href 值等于 "#" 的元素。

`$("[href!='#']")` 选取所有带有 href 值不等于 "#" 的元素。

`$("[href$='.jpg']")` 选取所有 href 值以 ".jpg" 结尾的元素。

## jQuery CSS 选择器

jQuery CSS 选择器可用于改变 HTML 元素的 CSS 属性。

下面的例子把所有 p 元素的背景颜色更改为红色：

**比如：**

```js
$("p").css("background-color","red");
```

# jQuery 事件

**jQuery 是为事件处理特别设计的。**

## jQuery 事件函数

jQuery 事件处理方法是 jQuery 中的核心函数。

事件处理程序指的是当 HTML 中发生某些事件时所调用的方法。术语由事件“触发”（或“激发”）经常会被使用。

**实例：**

```html
<html>
    <head>
        <script type="text/javascript" src="jquery.js"></script>
    </head>

    <body>
        <h2>This is a heading</h2>
        <p>This is a paragraph.</p>
        <p>This is another paragraph.</p>
        <button>Click me</button>
    </body>
    <script type="text/javascript">
        $(document).ready(function(){
            $("button").click(function(){
                $("p").hide();
            });
        });
    </script>
</html>
```

在上面的例子中，当按钮的点击事件被触发时会调用一个函数：

```js
$("button").click(function() {..some code... } )
```

该方法隐藏所有 `<p>` 元素：

```js
$("p").hide();
```

## 单独文件中的函数

如果您的网站包含许多页面，并且您希望您的 jQuery 函数易于维护，那么请把您的 jQuery 函数放到独立的 .js 文件中。

当我们在教程中演示 jQuery 时，会将函数直接添加到 `<head>` 部分中。不过，把它们放到一个单独的文件中会更好，就像这样（通过 src 属性来引用文件）：

**实例：**

```html
<head>
    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript" src="my_jquery_functions.js"></script>
</head>
```

## jQuery 名称冲突

jQuery 使用 $ 符号作为 jQuery 的简介方式。

某些其他 JavaScript 库中的函数（比如 Prototype）同样使用 $ 符号。

jQuery 使用名为 noConflict() 的方法来解决该问题。

`var jq=jQuery.noConflict()`，帮助您使用自己的名称（比如 jq）来代替 $ 符号。

## 结论

由于 jQuery 是为处理 HTML 事件而特别设计的，那么当您遵循以下原则时，您的代码会更恰当且更易维护：

- 把所有 jQuery 代码置于事件处理函数中
- 把所有事件处理函数置于文档就绪事件处理器中
- 把 jQuery 代码置于单独的 .js 文件中
- 如果存在名称冲突，则重命名 jQuery 库

## jQuery 事件

下面是 jQuery 中事件方法的一些例子：

| Event 函数                        | 绑定函数至                                     |
| :-------------------------------- | :--------------------------------------------- |
| `$(document).ready(function)`     | 将函数绑定到文档的就绪事件（当文档完成加载时） |
| `$(selector).click(function)`     | 触发或将函数绑定到被选元素的点击事件           |
| `$(selector).dblclick(function)`  | 触发或将函数绑定到被选元素的双击事件           |
| `$(selector).focus(function)`     | 触发或将函数绑定到被选元素的获得焦点事件       |
| `$(selector).mouseover(function)` | 触发或将函数绑定到被选元素的鼠标悬停事件       |

如需完整的参考手册，请访问我们的 [jQuery 事件参考手册](https://www.nowcoder.com/tutorial/62/c427388dfaa546c4a5142a1ee8222ada)。

