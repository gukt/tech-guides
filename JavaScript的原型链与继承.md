历史背景

​	为什么会搞出原型链这种继承方式

​	和基于“类”的语言有何不同？

什么是原型链？

JavaScript 只有一种结构：对象。没有“类”，每个对象都有一个私有属性（`__prot__`）,指向构造函数的原型对象。



## 历史背景

想深入了解  JavaScript 的继承思想，有必要了解一下它的历史，及作者当时的设计初衷。

JavaScript 之父 [Brendan Eich](http://brendaneich.com/) 当年创建这门语言时， Java 或 C++ 等基于“类”的面向对象语言已经大行其道，但它认为脚本语言不需要那么复杂，引入类和面向对象会增大新手的学习难度。

> 你丫整的这套新玩意，我看似乎也没给新学者减低学习理解继承机制的难度啊？

他甚至认为继承都不需要，但后来还是加上了。就是本文后面着重要讲的基于原型链的继承模式。



## 创建对象实例

JavaScript 中只有一种数据结构，那就是对象，这和 Java 语言中一切皆对象理念相同。用关键字 new 来创建对象实例。

```js
var obj = new Object()
```

由于没有"类"，那 `Object` 是什么呢？

```js
Object
// ƒ Object() { [native code] }
typeof Object 
// function
```

由输出可以看出，Object 其实构造构造函数，和 new 关键字配合用来生成一个对象实例。



甚至连我们经常定义的 function 也是一个 Object 。

```js
typeof Function // function
let func = function foo() {}
typeof func // function
```





每一个对象实例都有一个私有属性 `__proto__`，指向构造函数的 prototype 。

```js
function Dog(name) {
  this.name = name
}
let dogA = new Dog('大黄')
dogA.__proto__ === Dog.prototype // true
```

该原型对象也有一个私有属性 `__proto__` ，

```js
console.log(dogA.__proto__)
// constructor: ƒ Dog(name)
// __proto__: Object
```

dogA.__proto__ 