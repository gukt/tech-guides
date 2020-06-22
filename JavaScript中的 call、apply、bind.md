# 1、call 方法

`call` 方法用指定的第一个参数 this 和后面跟着的一个或多个参数来调用函数。

语法：`func.call(thisArgs, arg1, arg2, ...)`，

返回被调用函数的返回值，没有则返回 `undefined`。



## 使用场景 1：调用父构造函数

先看一个 Java 版本的继承类调用父类构造函数的例子，然后写一个 JavaScript版本的对比理解 `call` 方法。

Java 版本：

```java
class Animal(String name) {
  private String name;
  
  public Animal(String name) {
    this.name = name;
  }
}

class Dog extends Animal {
  private int age;
  public Dog(String name, int age) {
    super(name)
    this.age = age
  }
}
```

JavaScript版本：

```js
function Animal(name) {
  this.name = name
}
function Dog(name, age) {
  Animal.call(this, name)
  this.age = age
}
let dog1 = new Dog('wangwang', 2)
// Dog {name: "wangwang", age: 2}
```

上述示例中，调用构造函数 `Dog` 实例化 `dog1` 实例时，`this` 指向的是 `dog1` 实例，因此当 `Animal.call(this, name)` 这行执行时，`this` 指向的是 `dog1` 实例，运行函数 `Animal` 后，相当于给 `dog1` 实例新增了 `name` 属性。所以，最后 `dog1` 实例同时具有 `name` 和 `age` 两个属性。

这是一种典型的继承类构造函数调用父类构造函数的应用场景。



## 使用场景 2：调用匿名函数

下面的例子给每个 `Dog` 对象实例添加一个 `say` 方法。

```js
let dogs = [
  { name: '大黄', age: 2 },
  { name: '小黄', age: 0 }
]
for (let dog of dogs ) {
  (function() {
    this.say = function() {
      console.log(`I am ${this.name}`)
    }
  }).call(dog)
}
dogs[0].say() // I am 大黄
```

上述示例中，匿名函数通过 call 方法被调用后，内部给传入 this 对象（本例中传入的是 dog 实例）添加一个 say 方法。say 方法打印出 this 对象的 name 属性。



## 可以不提供 call 方法的 this 参数

`call` 方法的第一个参数 `this` 可以不用指定，或指定为 `null`、`undefined`，在“严格模式”和“非严格模式”下，有两种不同的处理方式：

1、不严格模式下：this 被绑定为全局对象。

```js
function print() {
  console.log('this =', this)
}
print.call()
print.call(null)
print.call(undefined)
// 浏览器中显示：this = Window {...}
```

2、严格模式下，this 指向 undefined。

```js
'use strict'
function print() {
  console.log('this =', this)
}
print.call() // this = undefined
```



# 2、apply 方法

`apply` 方法和 `call` 方法非常相似，唯一不同是它接受数组类型参数列表，而 `call` 方法接受数组序列参数列表。

语法：`func.apply(thisArg, argsArray)`

```js
function print() {
  console.log(...arguments)
}
print.call(null, 1, 2, 3) // 1 2 3
print.call(null, [1, 2, 3]) // 1 2 3
print.apply(null, [1, 2, 3]) // [1, 2, 3]
```

上述示例中，如果 `call` 方法提供数组作为参数，则函数内部会将数组参数**作为一个参数对待**。

由于 `ES6` 中提供了扩展运算符，可以将一个数组转为用逗号分隔的参数序列，因此 `apply` 方法很多情况下可以用 `call` 方法代替。

```js
function print() {
  console.log(...arguments)
}
print.call(null, ...[1, 2, 3]) // 1 2 3
```

从 ES5 开始，apply 方法可以使用“类数组对象（array like object）”作为参数。

```js
let arrayLike = {
  0: 'a',
  1: 'b',
  length: 2
}
function print() {
  console.log(...arguments)
}
print.apply(null, arrayLike) // a b
```

由于 ES6 的“扩展运算符”不适用于“类数组对象”，因此这种情况下就只能使用 apply 方法了。

```js
print.call(null, ...arrayLike)
// Uncaught TypeError: object is not iterable (cannot read property Symbol(Symbol.iterator))
print.apply(null, arrayLike) // a b
```



## 使用场景 1：合并数组

在 `ES6` 之前，由于没有扩展运算符，合并数组可以使用 `apply`。

```js
let a = [1, 2]
let b = [3, 4]
Array.prototype.push.apply(a, b) 
```

因为 `push` 方法接受的是参数序列。如果直接 `push` 数组 `b`，会将整个数组 `b` 作为一个单独的数组元素。

```js
let a = [1, 2]
let b = [3, 4]
a.push(b) // [1, 2, [3, 4]]
```

在 `ES6` 中，就简单多了。

```js
a.push(...b) // [1, 2, 3, 4]
```

数组的 `concat` 方法并不能实现这一需求，因为该方法返回一个新的数组，原数组 `a` 不会发生变化。

```js
let a = [1, 2]
let b = [3, 4]
let c = a.concat(b)
console.log(a, b, c) // [1, 2], [3, 4], [1, 2, 3, 4]
```

