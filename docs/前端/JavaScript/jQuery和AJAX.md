# jQuery和AJAX

# jQuery - AJAX 简介

**AJAX 是与服务器交换数据的艺术，它在不重载全部页面的情况下，实现了对部分网页的更新。**

## 什么是 AJAX？

AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。

简短地说，在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。

使用 AJAX 的应用程序案例：谷歌地图、腾讯微博、优酷视频、人人网等等。

您可以在我们的 AJAX 教程中学到更多有关 AJAX 的知识。

## 关于 jQuery 与 AJAX

jQuery 提供多个与 AJAX 有关的方法。

通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON - 同时您能够把这些外部数据直接载入网页的被选元素中。

编写常规的 AJAX 代码并不容易，因为不同的浏览器对 AJAX 的实现并不相同。这意味着您必须编写额外的代码对浏览器进行测试。不过，jQuery 团队为我们解决了这个难题，我们只需要一行简单的代码，就可以实现 AJAX 功能。

# jQuery - AJAX load() 方法

## jQuery load() 方法

**jQuery load() 方法是简单但强大的 AJAX 方法。**

load() 方法从服务器加载数据，并把返回的数据放入被选元素中。

**语法**：

```js
$(selector).load(URL,data,callback);
```

必需的 *URL* 参数规定您希望加载的 URL。

可选的 *data* 参数规定与请求一同发送的查询字符串键/值对集合。

可选的 *callback* 参数是 load() 方法完成后所执行的函数名称。

这是示例文件（"demo_test.txt"）的内容：

```html
<h2>jQuery and AJAX is FUN!!!</h2>
<p id="p1">This is some text in a paragraph.</p>
```

下面的例子会把文件 "demo_test.txt" 的内容加载到指定的 `<div>` 元素中：

**示例**

```js
$("#div1").load("demo_test.txt");
```

也可以把 jQuery 选择器添加到 URL 参数。

下面的例子把 "demo_test.txt" 文件中 id="p1" 的元素的内容，加载到指定的 `<div>` 元素中：

**实例**

```js
$("#div1").load("demo_test.txt #p1");
```

可选的 callback 参数规定当 load() 方法完成后所要允许的回调函数。回调函数可以设置不同的参数：

- *responseTxt* - 包含调用成功时的结果内容
- *statusTXT* - 包含调用的状态
- *xhr* - 包含 XMLHttpRequest 对象

下面的例子会在 load() 方法完成后显示一个提示框。如果 load() 方法已成功，则显示“外部内容加载成功！”，而如果失败，则显示错误消息：

**实例**

```js
$("button").click(function(){
    $("#div1").load("demo_test.txt",function(responseTxt,statusTxt,xhr){
        if(statusTxt=="success")
            alert("外部内容加载成功！");
        if(statusTxt=="error")
            alert("Error: "+xhr.status+": "+xhr.statusText);
    });
});
```

# jQuery - AJAX get() 和 post() 方法

**jQuery get() 和 post() 方法用于通过 HTTP GET 或 POST 请求从服务器请求数据。**

## HTTP 请求：GET vs. POST

两种在客户端和服务器端进行请求-响应的常用方法是：GET 和 POST。

- *GET* - 从指定的资源请求数据
- *POST* - 向指定的资源提交要处理的数据

GET 基本上用于从服务器获得（取回）数据。注释：GET 方法可能返回缓存数据。

POST 也可用于从服务器获取数据。不过，POST 方法不会缓存数据，并且常用于连同请求一起发送数据。

## jQuery $.get() 方法

$.get() 方法通过 HTTP GET 请求从服务器上请求数据。

**语法**：

```
$.get(URL,callback);
```

必需的 *URL* 参数规定您希望请求的 URL。

可选的 *callback* 参数是请求成功后所执行的函数名。

下面的例子使用 $.get() 方法从服务器上的一个文件中取回数据：

**实例**

```js
$("button").click(function(){
    $.get("demo_test.asp",function(data,status){
        alert("Data: " + data + "\nStatus: " + status);
    });
});
```

$.get() 的第一个参数是我们希望请求的 URL（"demo_test.asp"）。

第二个参数是回调函数。第一个回调参数存有被请求页面的内容，第二个回调参数存有请求的状态。

```asp
<%
	response.write("This is some text from an external ASP file.")
%>
```

## jQuery $.post() 方法

$.post() 方法通过 HTTP POST 请求从服务器上请求数据。

**语法**：

```js
$.post(URL,data,callback);
```

必需的 *URL* 参数规定您希望请求的 URL。

可选的 *data* 参数规定连同请求发送的数据。

可选的 *callback* 参数是请求成功后所执行的函数名。

下面的例子使用 $.post() 连同请求一起发送数据：

**实例**

```js
$("button").click(function(){
    $.post("demo_test_post.asp",
           {
        name:"Donald Duck",
        city:"Duckburg"
    },
           function(data,status){
        alert("Data: " + data + "\nStatus: " + status);
    });
});
```

$.post() 的第一个参数是我们希望请求的 URL ("demo_test_post.asp")。

然后我们连同请求（name 和 city）一起发送数据。

"demo_test_post.asp" 中的 ASP 脚本读取这些参数，对它们进行处理，然后返回结果。

第三个参数是回调函数。第一个回调参数存有被请求页面的内容，而第二个参数存有请求的状态。

```asp
<%
	dim fname,city
    fname=Request.Form("name")
    city=Request.Form("city")
    Response.Write("Dear " & fname & ". ")
    Response.Write("Hope you live well in " & city & ".")
%>
```

# 使用实例

## GET

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        <title>GET、POST</title>
        <script src="js/jquery-3.3.1.min.js"></script>
    </head>
    <body>
        <input type="text" name="page" id="page" />
        <input type="button" value="Submit" onclick="submit()" />
    </body>
    <script>
        function submit() {
            $.ajax({
                type: "GET",
                url: "localhost:8080/travle/news/list",
                data: { page: $("#page").val(), limit: 5 },
                dataType: "JSON",
                success: function(result) {}
            });
        }
    </script>
</html>
```

## POST

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        <title>GET、POST</title>
        <script src="js/jquery-3.3.1.min.js"></script>
    </head>
    <body>
        <input type="text" name="username" id="username" />
        <input type="text" name="password" id="password" />
        <input type="button" value="Submit" onclick="submit()" />
    </body>
    <script>
        function submit() {
            $.ajax({
                type: "POST",
                url: "localhost:8080/travle/admin/login",
                data: {
                    username: $("#username").val(),
                    password: $("#password").val()
                },
                dataType: "JSON",
                success: function(result) {}
            });
        }
    </script>
</html>
```

## 参数说明

| 序号 | 参数              | 解释                                                         |
| ---- | ----------------- | ------------------------------------------------------------ |
| 1    | **url**           | String 类型参数，发送请求的地址                              |
| 2    | **type**          | String 类型参数，设置请求方式（ GET、POST、PUT、DELETE ）    |
| 3    | **timeout**       | Number类型参数，设置请求超时时间（毫秒）                     |
| 4    | **async**         | Boolean 类型参数，默认设置为 true，当为 true 表示发送异步请求，当为 false 表示发送同步请求 |
| 5    | **cache**         | Boolean 类型参数，默认设置为 true 默认为 true（当 dataType 为 script 时，默认为 false ），设置为false将不会从浏览器缓存中加载请求信息 |
| 6    | **data**          | Object 或 String 类型的参数，发送请求的参数，如果是 GET 方式则以 ? & 形式拼接到 url 中，如果是 POST 方式则将数据放在 FormData 中 |
| 7    | **dataType**      | String 类型的参数，指定服务器返回的数据类型<br />xml：返回 XML 文档<br />html：返回纯文本 HTML 信息 <br />script：返回纯文本 JavaScript 代码 不会自动缓存结果 <br />json：返回 JSON 数据 <br />jsonp：JSONP 格式 <br />text：返回纯文本字符串 |
| 8    | **beforeSend**    | Function 类型参数，发送请求前可以修改 XMLHttpRequest 对象的函数，例如添加自定义 HTTP 头 |
| 9    | **complete**      | Function 类型参数，请求完成后调用的回调函数（请求成功或失败时均调用） |
| 10   | **success**       | Function 类型参数，请求成功的回调函数                        |
| 11   | **error**         | Function 类型参数，请求失败的回调函数                        |
| 12   | **contentType**   | String 类型参数，当发送信息至服务器时，内容编码类型默认为"application/x-www-form-urlencoded" |
| 13   | **dataFilter**    | Function 类型参数，给Ajax返回的原始数据进行预处理的函数。提供data和type两个参数。data是Ajax返回的原始数据，type是调用jQuery.ajax时提供的dataType参数。函数返回的值将由jQuery进一步处理<br/>function(data, type){<br/>&nbsp;&nbsp;&nbsp;&nbsp;//返回处理后的数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;return data;<br/>} |
| 14   | **global**        | Boolean 类型参数，默认为true。表示是否触发全局ajax事件。设置为false将不会触发全局ajax事件，ajaxStart或ajaxStop可用于控制各种ajax事件 |
| 15   | **ifModified**    | Boolean 类型参数，默认为false。仅在服务器数据改变时获取新数据。服务器数据改变判断的依据是Last-Modified头信息。默认值是false，即忽略头信息 |
| 16   | **jsonp**         | String 类型参数，在一个jsonp请求中重写回调函数的名字。该值用来替代在"callback=?"这种GET或POST请求中URL参数里的"callback"部分，例如{jsonp:'onJsonPLoad'}会导致将"onJsonPLoad=?"传给服务器 |
| 17   | **username**      | String 类型参数，用于响应HTTP访问认证请求的用户名            |
| 18   | **password**      | String 类型参数，用于响应HTTP访问认证请求的密码              |
| 19   | **processData**   | Boolean 类型参数，默认为true。默认情况下，发送的数据将被转换为对象（从技术角度来讲并非字符串）以配合默认内容类型"application/x-www-form-urlencoded"。如果要发送DOM树信息或者其他不希望转换的信息，请设置为false |
| 20   | **scriptCharset** | String 类型参数，只有当请求时dataType为"jsonp"或者"script"，并且type是GET时才会用于强制修改字符集(charset)。通常在本地和远程的内容编码不同时使用 |

