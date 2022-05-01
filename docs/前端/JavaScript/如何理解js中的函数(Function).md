# 如何理解js中的函数(Function)

# Function类型

首先得知道，每个函数都是Function类型的实例，**所以函数本身是对象**。

示例1：

```js
function sum (num1, num2){
    return sum1 + sum2;  
}
```

示例2：

```javascript
var sum = function(num1, num2){
    return num1 + num2;
}
```

本质上这两种定义函数的方式没什么差别。因为函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。

**定义一个函数可以理解为：函数名是变量，函数是对象，函数名是指向函数的指针，与其他变量没什么不同。**

## 没有重载(深入理解)

看个例子：

```js
function add(num1){
    return num1+100
}

function add(num2){
    return num2+200
}
var result = add(100);    //300
```

这个例子中声明了两个同名函数，结果则是后面的函数覆盖了前面的函数。**可以理解为定义了一个变量add 指向一个function, 然后又改变了这个变量add指向了另一个function，所以只有最后的function生效。**

# 函数属性和方法

每个函数都包含两个属性：length和prototype。其中，length属性表示函数希望接收的命名参数的个数。

```js
function sayName(){
    alert(name);
}
function sum(num1, num2){
    return num1 + num2;
}
function sayHi(){
    alert('hi');
}
console.log(sayName.length);    //1
console.log(sum.length);    //2
console.log(sayHi.length);    //0
```

每个函数都包含两个非继承而来的方法：`call()` 和 `apply()`

- `apply()` 方法接收两个参数，一个时在其中运行函数的作用域，另一个是参数数组。
- `call()` 方法接收的第一个参数也是在其中运行函数的作用于，其他的参数必须逐个列举出来。
- ES5还定义了一个方法：`bind()`，这个方法会创建一个函数的实例，其this值会被绑定到传给bind()函数的值。

详细了解这三个函数，请去查看js中函数的this指向相关内容。

## 函数内部属性

在函数内部，有两个特别的对象：arguments和this。 arguments的主要用途是保存函数参数，但这个对象还有一个名叫callee的属性，该属性是一个指针，指向拥有这个arguments对象的函数。