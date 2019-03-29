---
title: 理解JavaScript中的闭包
tag: 深入学习JS
date: 2019-02-17
---

#### 什么是闭包
> 闭包是指有权访问另一个函数作用域中的变量的函数。

闭包不是我们需要学习的语法或者模式才能使用的工具，闭包是基于词法作用域书写代码是所产生的自然结果，闭包在我们所写的代码中随处可见。
<!-- more -->
#### 闭包的产生
> 当函数可以记住并访问所在的词法作用域是，就产生了闭包，即使函数是在当前词法作用域之外执行。

根据闭包的定义其实创建闭包最常用的方式就是在一个函数内部创建另外一个函数：

```javascript
function fn() {
    var str = 'fn';
    function innerFn() {
        console.log(str)
    }
    return innerFn;
}

var spoce = fn();
spoce() // 'fn'
```
函数 innerFn 能过访问 fn 的内部作用域，fn 函数将 innerFn 所引用的函数对象作为一个返回值传递出去。
fn 函数执行后，spoce 获取 innerFn 引用的函数对象，执行 spoce 就会打印出 'fn' , 通常一个函数被执行其整个内部作用域都会被销毁，然而 fn 的内部作用域仍然存在, 并且 spoce 可以在 fn 内部作用域之外访问到 fn 函数内部的变量。
其实这一切都是因为 innerFn 函数拥有覆盖 fn 函数内部作用域的闭包，使得该作用域能够一直存货，以供 innerFn 在之后的任何时间进行引用。
innerFn 函数对 fn 函数内部作用域的持续引用就叫做闭包。

#### 闭包的应用
> JavaScript中闭包无处不在。
##### 封装数据
在其他语言中基本都有私有变量的属性，类似 Java 提供了 private 关键字声明私有变量，在 JavaScript 中没有声明私有变量的关键字，我们可以通过闭包来实现数据的封装。

```javascript
const myObject = {
    _name: 'hello world',
    setName: (name) => {this._name = name;},
    getName: () => {return this._name;}
}
myObject.getName(); // hello world
myObject.setName('hello 闭包');
myObject._name; // hello 闭包
myObject._name = 'hello javascript';
myObject.getName(); // hello javascript
```
对于 _name 我们是可以直接访问和修改的，因为 _name 只是 myObject 的一个属性，通过属性名可以直接访问到。

如果我们想让 _name 是一个私有属性或变量，外部不能直接访问或修改这个变量，我们就可以通过闭包来实现：

```javascript
const myObject = (() => {
    var _name = 'hello world'
    return {
        setName: (name) => {_name = name;},
        getName: () => { return _name; }
    }
})()
myObject.getName(); // hello world
myObject.setName('hello 闭包');
myObject._name; // undefined
myObject._name = 'hello javascript';
myObject.getName(); // hello 闭包
```

当然我们也可以利用闭包可以封住数据的特性实现数据的缓存，下面我们就写个除法计算的例子：

```javascript
var divide = (() => {
    var cache = {}; // 缓存计算结果
    return (x, y) => {
        const key = `${x}/${y}`;
        if(cache[key]) return cache[key];
        const result = x/y;
        cache[key] = result;
        return result;
    }
})
```
在实际的项目中我们可以缓存更复杂的计算结果。
##### 设计模式
在 JavaScript 实现的设计模式中，闭包的应用非常广泛。比如单例模式、发布-订阅模式等。
单例模式：

```javascript
var Singleton = function( name ){
    this.name = name;
};

Singleton.prototype.getName = function(){
    console.log ( this.name );
};

Singleton.getInstance = (function(){
    var instance = null;
    return function( name ){
        if ( !instance ){
            instance = new Singleton( name );
        }
        return instance;
    }
})();

var name1 = Singleton.getInstance('name1');
var name2 = Singleton.getInstance('name2');
name1 === name2; // true
name1.getName(); // name1
name2.getName(); // name1
```
上面通过Singleton.getInstance获取唯一的实例，第二次执行并不会修改已经存在的这个实例。

##### 经典面试题
提到闭包还会有个经典的面试题可以看看：
> 每隔一秒依次打印出 0, 1, 2, 3, 4

```javascript
for(var i=0;i<5;i++){
    setTimeout(function(){
        console.log(i)
    },1000 * i)
}
```
上面的代码可以实现每隔一秒打印一次，但是却是每隔一秒打印出一个 '5'。

想要让代码正确执行应该怎么处理呢？
最简单的方法就是利用 ES6 的 let 关键字, 形成一个块级作用域：
```javascript
for(let i=0;i<5;i++){
    setTimeout(function(){
        console.log(i)
    },1000 * i)
}
```
也可以利用闭包实现：
```javascript
for(let i=0;i<5;i++){
    (j) => {
        setTimeout(function(){
            console.log(j)
        },1000 * j)
    }(i)
}
```
#### 总结
闭包是一种非常强大的特性，为我们带来了很多便利。但是闭包对一个局部作用域持续引用，会使一些数据无法及时销毁，在使用闭包时需要我们手动的释放变量。
