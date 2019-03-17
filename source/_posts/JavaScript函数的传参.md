---
title: JavaScript函数的传参
tag: 深入学习JS
date: 2019-01-16
---
JavaScript 中的函数传参是按值传递还是按引用传递呢？
> ECMAScript 中所有函数的参数都是按值传递的。
<p align="right"><font color=gray size=2>摘自 JavaScript高级程序设计</font></p>

<!-- more -->

下面我们看一段代码
```javascript
function changeStuff(a, b, c) {
  a = a * 10;
  b.item = "changed";
  c = {item: "changed"};
}

var num = 10;
var obj1 = {item: "unchanged"};
var obj2 = {item: "unchanged"};

changeStuff(num, obj1, obj2);

console.log('num: 'num);
console.log('obj1: 'obj1.item);
console.log('obj2: 'obj2.item);
```
打印出来的值是：
```javascript
num: 10
obj1: {item: "changed"}
obj2: {item: "unchanged"}
```
说好的按值传递为什么 obj1 的值变了。
<div align=center>
<img width = "150" height = "150" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/9150e4e5ly1fl77kzjvr5g208c08caa4.gif"/>
</div>

在继续了解 JavaScript 的函数是怎么传参之前，先简单了解一下 JavaScript 中是数据类型。
### 数据类型
JS中有7种数据类型，6种基本数据类型（Undefined, Null, Boolean, Number, String, Symbol）和一种复杂数据类型(Object)。

> 除 Object 以外的所有类型都是不可变的（值本身无法被改变）。


#### 基本数据类型
基本数据类型就是数字、字符串、布尔值、还有两个比较特殊的 null 和 undefined，以及 ES6 新增的 symbol 。
1. 基本数据类型的值是按值访问的；
2. 基本类型的值是不可变的；
3. 基本数据类型比较是按值进行比较。

#### 引用数据类型
除了上面的六种数据类型之外，其他的都是引用类型，统称为 Object 类型，主要细分为Object、Array、Date、RegExpd等。

1. 基本数据类型的值是按引用访问的；
2. 引用数据类型是可变的；
3. 引用数据类型是按引用访问的。

下面我们来看一段代码：
```javascript
var obj1 = {name: "hello world"};
var obj2 = {name: "hello world"};
var obj3 = obj1;
console.log(obj1 === obj2); // 1
console.log(obj1 === obj3); // 2
obj3.name = "hello javascript";
console.log(obj1.name); // 3
```
<div align=center>
<img height = "250" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/WX20190127-221856%402x.png"/>
</div>

obj1 和 obj2 指向的是不同内存地址，obj1 和 obj3 指向的是同一个内存地址。

所以第一个打印的结果应该是 false ，obj1 和 obj2 的引用地址不同，第二个打印结果为 true，obj1 和 obj3 的引用地址相同。 第三个打印结果为 'hello javascript'， 修改 obj3 其实就是修改了其指向的对象的值，所以 obj1 也发生了变化。

### 函数的传参
下面我们再来看看执行了 changeStuff 之后发生了什么吧。

<div align=center>
<img height = "350" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/1548554750178.jpg"/>
</div>

changeStuff 执行之后就是对 changeStuff 中的三个形参进行了一次赋值操作，相当于：
```javascript
a = num;
b = obj1;
c = obj2;
```
<div align=center>
<img height = "350" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/WX20190127-200153%402x.png"/>
</div>
num 是基础类型，所以 a 指向的是一个新的内存地址，而 obj1 和 obj2 是引用类型，赋值给 a, b 的值是当前的引用地址。所以 a, b 指向的是 obj1 和 obj2 指向的内存地址。
```
a = a * 10;
b.item = "changed";
c = {item: "changed"};
```
这段代码执行完之后，会重新给 a 指向一个新的内存地址，==b.item = 'changed'== 实际上就是修改 b 所指向对象的 item 的值， ==c = {item: "changed"}== 把 c 指向一个新的内存地址，切断了 c 与 obj2 之间的联系。
<div align=center>
<img height = "350" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/WX20190127-200614%402x.png"/>
</div>
所以打印的值是
```javascript
num: 10
obj1: {item: "changed"}
obj2: {item: "unchanged"}
```
#### 传入参数声明的位置
在 JS 中我们都知道使用 var 和 function 声明一个变量时会存在变量提升，但是当同时使用 var、function 声明一个变量 'a' 以及传入一个同名变量 'a' 时，最后 JS引擎 会把哪个值赋给变量 'a' 呢 ？

```javascript
(function (a) {
    var a = 10;
    console.log(1, a);
})(20);
(function () {
    var a = 10;
    console.log(2, a);
    function a() {};
})();
(function (a) {
    console.log(3, a);
    function a() {};
})(20);
```
你可以复制这段代码到浏览器的控制台中看一下执行结果，下面是执行结果：
```javascript
1 10
2 10
3 ƒ a() {}
```
由此三段代码可以看出来：

传入的参数是相当于声明在函数的最顶部，还在提升的函数之前。（后声明的变量的值覆盖了前面的值）
### 总结
ECMAScript 中所有函数的参数都是按值传递的，对于引用类型的值传递的是当前对象的引用，即一个堆内存的地址。
