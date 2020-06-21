最原始的遍历是 `for` 循环写法。

```js
let a = [1, 2, 3]
for (let i = 0; i < a.length; i++) {
  console.log(a[i])
}
```

繁琐，可使用 `for...in` 取代。

```js
let a = [1, 2, 3]
for (let i in a) {
  console.log(a[i])
}
```

对于遍历部署了 `Iterator` 接口的数据结构，可以使用 `for...of` 进行遍历。

```js
let arr = ['a', b', 'c']
for (let element of arr) {
  console.log(element) // a, b, c
}
```

原生具备 `Iterator` 接口的的数据类型有：

`Array`，`Map`，`Set`，`String`，`TypedArray`，`Argument`，`NodeList`。

如果循环体内想同时使用“数组下标”和“值”，可借助数组（或对象）的 `entries` 方法。

```js
let arr = ['a', 'b', 'c']
for (let entry of arr.entries()) {
  console.log(entry) // [0, 'a'], [1, 'b'], [2, 'c']
}
```

这是因为 entrie() 方法返回一个 Iterator 对象，因此可以使用 `for...of` 进行遍历。

如果只想读取数组的下标，可借助数组的 `keys` 方法（也是返回 Iterator 对象）（与利用 `for...in` 遍历数组等效，见后面）。

```js
let arr = ['a', 'b', 'c']
for (let key of arr.keys()) {
  console.log(key) // 0, 1, 2
}
```

`for...in` 只能遍历数组或对象的键名（对于数组来说键名是索引下标），如果循环体内需要使用键值，则需要通过键名读取。

```js
var person = {name: 'tom', age: 20}
for (let prop in person) {
  console.log(prop, person[prop]) 
}
// name, tom
// age, 20

var arr = ['a', 'b', 'c']
for (let index in arr) {
  console.log(index, arr[index]) // 0, 1, 2
}
```

这样利用索引读取数组元素值相对有点麻烦，使用 `forEach` 可以同时读出键名和键值，但是这种写法的缺点是不能使用 `break`、`continue`、 `return` 等循环控制语句。

```js
let arr = ['a', 'b', 'c']
arr.forEach((element, index) => {
  console.log(index, element)
})
// 0 "a"
// 1 "b"
// 2 "c"
```

注意：**`for...of` 遍历数组时，仅返回数字索引的元素，而 `for...in` 可以返回非数字索引**。

```js
let arr = ['a', 'b', 'c']
arr.foo = 'bar'
for (let index in arr) {
  console.log(index) // 0, 1, 2, foo
}
for (let index of arr) {
  console.log(index) // 0, 1, 2
}
```

我们可能想当然认为，通过数组的 `values` 方法可以获得到数组索引 `foo` 对应的值 `bar`，实际上也只能返回数字索引对应的元素。

```js
for (let value of arr.values) {
  console.log(value) // 0, 1, 2
}
```

对于 `32` 位 `UTF-16` 字符，`for...in` 不能正确识别，而 `for...of` 可以。

```js
let s = '我是\uD83D\uDC0B'
console.log(s) // 我是🐋

for (let c of s){
  console.log(c) // 我是🐋
}
for (let index in s) {
  console.log(s[index]) // 我 是 � �
}
```

对于`普通对象`，如果没有部署 `Iterator` 接口，是不能利用 `for...of` 的，但可以使用 `for...in`。

```js
let person = { name: 'tom', age: 20 }
for (let i of person) {
  console.log(i)
}
TypeError: Invalid attempt to iterate non-iterable instance.
In order to be iterable, non-array objects must have a [Symbol.iterator]() method.
```

如果非要使用 `for...of` 的话，有两种方式：

1、可以借助 `Object.keys` ，`Object.values` ，`Object.entries` 方法遍历对象。

```js
let person = { name: 'tom', age: 20 }
for (let k of Object.keys(person)) {
  console.log(k, person[k])
}
// name tom
// age 20
```

注意：要区别 `Object.keys` ，`Object.values` ，`Object.entries` 返回的是数组对象，而数组对象上调用 `keys`，`values`，`entries` 方法返回的是 `Array Iterator {}`。

```js
let person = { name: 'tom', age: 20 }
Object.keys()
// (2) ["name", "age"]
// __proto__: Array(0)
let a = [1, 2, 3]
a.keys()
// Array Iterator {}
```

因为数组默认是 `Iterable` 的，因此无论是返回数组，还是  `Array Iterator {}` ，都是可以使用 `for...of` 遍历的。

2、使用 `Generator` 函数

```js
let person = { name: 'tom', age: 20 }

function* entries(obj) {
  for (let key of Object.keys(obj)) {
    yield [key, obj[key]];
  }
}

for (let [k, v] of entries(person)) {
  console.log(k, v);
}
```



**总结：**

1. 古老的 `for` 循环不建议使用，可使用其他遍历语法代替。
2. `for...in` 仅可以遍历键名，对于数组来说，只可以遍历数字索引对应的元素。`for...in` 循环时要想遍历值就只能通过 key 来读取。
3. `forEach` 虽然可以同时获取到键名和键值，但缺点是不支持 `break`，`continue`，`return` 等循环控制语句。
4. `for...in` 循环主要是为了遍历对象而设计的，不太适合遍历数组，遍历数组使用 `for...of` 更简洁。
5. `for...of` 同 `for...in` 一样语法简洁，且没有 `for...in` 的仅能遍历键名的缺点。比 `for...in` 使用更广泛，对于数组，借助 `entries`、`values`、`keys` 等方法，可以灵活的读取数据，而 `for...in` 是不能在这些方法结果上遍历的，因为它们的类型是 `Array Iterator {}`。
6. 遍历数组有个例外，就是当数组中有手动添加的非数组键名时，使用 `for...of` 读取不到非数字键名，这个时候需要 `for...in`。