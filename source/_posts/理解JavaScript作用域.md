---
title: 理解JavaScript作用域
tag: 深入学习JS
date: 2018-12-20
---
#### 什么是作用域
JavaScript 定义了一套如何存储以及查找变量的规则，这套规则就是**作用域**。

作用域共有两种主要的工作模型。第一种是最为普遍的，被大多数编程语言所采用的词法作用域，另外一种叫作动态作用域。JavaScript 采用的就是词法作用域。

作用域主要有全局作用域、函数作用域以及 ES6 新增的块级作用域。
<!-- more -->
#### 全局作用域
拥有全局作用域的是全局变量。

在不用的环境中，全局变量是不同的:
- 在浏览器环境中全局变量是 window
- 在 Node.js 中全局变量是 golbal

在浏览器 JS 中我们可以通过 window.spoce 的方式访问全局作用域下的定义的变量。
#### 函数作用域
> 函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复用(事实上在嵌套的作用域中也可以使用)

在 JavaScript 中每个函数都会创建一个函数作用域。在函数之外一般是访问不到函数内定义的变量，只能在函数内部进行访问。
```javascript
function fn() {
    var spoce = 'fn'
    console.log(spoce); // 1
}
console.log(spoce); // 2
```
在上面的代码中<code>1</code> 会打印出 *fn*,
但是执行到<code>2</code>时会报错 <em style="color: red">Uncaught ReferenceError: spoce is not defined</em>。
##### 立即执行函数


#### 块作用域
在 ES6 之前，JavaScript 不存在块级作用域。
```javascript
if(true) {
    var spoce = 'define spoce'
}
console.log(spoce) // 会发生什么？？？
```
如果是块级作用域在外面是不应该访问到 spoce 这个变量，代码就会报错 <em style="color: red">Uncaught ReferenceError: spoce is not defined</em>。而实际上这段代码会打印出 *define spoce*，为什么会这样呢？

用 var 声明的变量存在**变量提升**的问题。<code>var spoce = 'define spoce'</code>，分为两个部分：<code>var spoce</code> 和 <code>spoce = 'define spoce'</code>，在解析 JS 代码时会把<code>var spoce</code>提升到函数作用域的顶部，在 if 条件语句中只是对其进行了赋值操作。所以上面的代码是在函数作用域中声明了<code>spoce</code>，所以打印的是 *define spoce*。

```javascript
if(true) {
    let spoce = 'define spoce'
}
console.log(spoce) // 又会发生什么？？？
```
难道这里不是打印 *define spoce* 吗?

其实代码执行到这里就会报错 <em style="color: red">Uncaught ReferenceError: spoce is not defined</em> 。
<div align=center>
<img width = "150" height = "150" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/7e9adf9d26b1a18ffaf7f80129344279.gif"/>
</div>

let 关键字会将声明的变量隐式的绑定在所在的块作用域（通常是 {...} 内部），所以在 if {...} 这个块之外是访问不到块作用域的，所以执行到 console.log(spoce) 的时候 <code>spoce</code> 是没有声明的。

使用 let 声明变量的时候还要注意的是 let 声明的变量不会变量提升，使用变量必须在声明之后才能使用，否则也是会抛出 <em style="color: red">Uncaught ReferenceError: spoce is not defined</em>。

在 ES6 中还有一个 const 关键字与 let 类似，只是 const 声明变量不能进行修改。

#### 作用域链
```javascript
var a = 10;
function fn() {
    var b = 5;
    function fn1() {
        var c = 1;
        console.log('result: ', a + b + c); // result: 16
    }
    fn1();
}
fn();
```
<div align=center>
<img height = "400" src="https://raw.githubusercontent.com/volcanoliuc/blog/master/images/spoce.png"/>
</div>
变量<code>a</code><code>b</code><code>c</code> 都会查找当前作用域是否存在变量<code>a</code> <code>b</code> 或 <code>c</code>，如果不存在则继续查找父级作用域直至查询到全局作用域为止。这样一套变量的查找规则其实就是作用域链的作用，规定了一个变量能访问的作用域。至于为什么会这样以后有时间再细细研究（内部属性 [[Scope]] 的作用）。

```javascript
var a = 10;
function fn() {
    var a = 5;
    var b = 5;
    function fn1() {
        var c = 1;
        console.log('result: ', a + b + c); // result: 11
    }
    fn1();
}
fn();
```
作用域查找会在找到第一个匹配标签的时候就结束，因此内外作用域有同名的变量的时候，会产生遮蔽效应，直接采用了内层的同名变量a。

除了 let、const 之外，with 和 try/catch 也会产生块级作用域，这是这两种方法并不推荐使用，如果感兴趣可自行 google。

#### 总结
在 JavaScript 中函数作用域是我们最常使用的作用域单元，ES6 流行之后，块级作用域我们使用的也越来越多，let、const 声明的变量会添加到块级作用域中。