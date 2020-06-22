

## JavaScript 中的数据类型

最新的 ECMAScript 定义了 8 种数据类型：

1、7 种基本类型（primitive values）

boolean，string，number，bigint，null，undefined，symbol（ECMAScript 2016 新增）

2、Object 类型



## 基本类型的包装对象

7 种基本类型中，除了 null、undefined 之外，都有对应的包装对象，分别是：Boolean、String、Number、BigInt、Symbol。

包装对象的 valueOf() 方法返回基本类型的值。

```js
let b1 = true // true
typeof b1 // "boolean"
let b2 = new Boolean(1) // Boolean {true}
typeof b2 // "object"
b2.valueOf() // true
b2.valueOf() === b1 // true
```

可以看出，对于包装对象 `b2` 使用 `typeof` 操作符判断为 `object` 类型。而 `typeof b1` 得到的类型为 `booelan`。



## 基本类型（Primitive Value）

基本类型（Primitive Value），又称基础数值、基本数据类型、原始类型，是一种即不是对象也没有方法的数据。

所有基本类型的值**都是不可改变**的。

```js
let foo = 'foo'
foo[0] = 'b'
foo // foo
```

这里有个概念很容易混淆，即变量和基本类型要分开。



### 1、布尔类型（boolean）

布尔类型有两个值：true，false

```js
let b1 = true // true
typeof b1 // boolean
let b2 = new Boolean(1) // Boolean {true}
typeof b2 // object
```

Boolean 是原始布尔类型的包装对象，由上面输出可以看出 b2 是 Object 类型，而不是原始类型 boolean。

### 2、数字类型







原始类型





基本数据类型的



其中包括 7 种基本数据类型和 Object。

7 种基础类型（Primitive Value）：boolean