在 JavaScript 中，`null` 和 `undefined` 是数据类型。而且是原始数据类型（`primitive value`，`primitive data type`）。

如果定义了一个变量，但是没有赋值，则变量的初始化值就是 `undefined`。

```js
let a // undefined
typeof a // undefined
```

如果函数的形参在调用时没有对应的实参对其赋值，则形参值也为 `undefined`。

```js
function foo(a, b) {
  console.log(a, b) 
}
foo() // undefined undefined
```

`null` 和 `undifined` 经常容易造成理解上的混淆。在 JavaScript 中，`null` 用来表示地址引用，指向一个不存在或无效的 `Object`。从 `typeof` 操作符可以看出值为 `null` 的变量的类型为 `object`，注意：这和值为 `undefined` 的变量类型有区别。

```js
let a // undefined
typeof a // undefined
a = null // null, 此时设置为 null 表示变量 a 指向一个无效或不存的对象
typeof a // object
```

`null` 和 `undefined` 在判断语句中都视为 `false`。

```js
if (!null) { console.log('foo') } // foo
if (!undefined) { console.log('foo') } // foo
null || console.log('foo') // foo
undefined  || console.log('foo') // foo
```

赋值给 `Boolean` 包装对象时也视为 `false`。

```js
let b1 = new Boolean(null) // Boolean(false)
let b2 = new Boolean(undefined) // // Boolean(false)
```



总结：

1. `null` 和 `undefined` 在 JavaScript 中都是原始数据类型。
2. `null` 用于表示对象引用，所有对象都可以赋值为 `null`，表示指向一个无效的对象或空引用，而 `undefined` 表示未赋值的变量。
3. `typeof null` 输出 `object`，而 `typeof undefined` 输出 `undefined`，注意区别。
4. `null` 和 `undefined` 在参与条件判断，或给 `Boolean` 对象赋值时，均被视为 `false`。

