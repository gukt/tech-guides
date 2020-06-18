数组是 JavaScript 应用程序中使用最频繁的数据结构之一，有必要把常用方法的作用和使用搞透彻了。



# Array 方法大全

以下包含传统 Array 的方法，也包含 ES6 对 Array 的扩展方法。



##  1、slice

语法：`slice(?start，?end)`

`slice` 是切片的意思，顾名思义，就是从原数组中指定范围  `[start, end)`  内切出一片，并返回新的数组。

`start` 省略时默认为 `0` ；`end` 省略时默认为数组的长度。

```js
let a = [1, 2, 3, 4]
let b = a.slice() // [1, 2, 3, 4]
a == b // false
a.slice(0) // [1, 2, 3, 4]
a.slice(1) // [2, 3, 4]
```

`start` 或 `end` 大于数组长度时，会校准为数组长度。

```js
let a = [1, 2, 3, 4]
a.slice(100) // []
a.slice(0, 100) // [1, 2, 3, 4]
```

`start` 大于或等于 `end` 时，返回空数组。

```js
let a = [1, 2, 3, 4]
a.slice(2, 1) // []
a.slice(2, 2) // []
a.slice(2, 3) // [3]
```

`start` 也可以为负数，最后一位元素用 `-1` 表示。

```js
let a = [1, 2, 3, 4]
a.slice(-1) // [4]
a.slice(-2) // [3, 4]
a.slice(-2, -1) // [3]

// 以下语句等效于 a.slice(3, 3), 在 start >= end 时返回 []
a.slice(-1, 3) // []
a.slice(-1, 4) // [4]
```



## 2、splice

语法：`splice(start, ?deleteCount, ...items)`

`splice` 的作用是用以拼接数组，会更改元素数组，返回被切下的数组。它能够从指定位置开始连续删除指定个数的元素后，再插入多个元素。`start` 可以为负数，`-1` 表示指向最后一位元素。

> 关于 start 参数需要重点说明一下，由于 start 即是作为删除的位置索引，又作为新增元素的参考位置。所以要理解它在两种行为下的规则：
>
> 1. 对于删除来说，从 start 位置开始删除（包含 start）。
> 2. 对于插入新元素来说，在 start 位置之前插入新元素。

```js
let a = [0, 1, 2, 3, 4, 5, 6]
let b = a.splice(0, 1)
console.log(b) // [0]
console.log(a) // [1, 2, 3, 4, 5, 6]
```

删除后添加多个元素：

```js
let a = [0, 1, 2, 3, 4, 5, 6]
let b = a.splice(1,5, ...[7, [8, 9]])
console.log(b) // [1, 2, 3, 4, 5]
console.log(a) // [0, 7, [8, 9], 6]
```

`splice` 可以实现 `push`、`pop`、`shift`、`unshift` 等效的功能，但是可读性差，不建议这么用，下面只是演示其功能。

```js
let a = [0, 1, 2, 3]
a.splice(-1, 1) // [0, 1, 2], 等效于 a.pop()
a.splice(0, 1) // [1, 2], 等效于 a.shift()
a.splice(0, 0, 9) // [9, 1, 2], 等效于 a.unshift(9)
a.splice(a.length, 0, 8) // [9, 1, 2, 8], 等效于 a.push(8)
```



## 3、push、pop、shift、unshift

`push` 和 `pop` 是在“数组的尾部”进行操作。可以一次 `push` 多个元素。但 `pop` 只能一次移除一个。

```js
let a = [1, 2]
a.push(3) // [1, 2, 3]
a.push(4, 5) // [1, 2, 3, 4, 5]
a.push([6, 7]) // [1, 2, 3, 4, 5, [6, 7]]
a.pop() // [1, 2, 3, 4, 5]
a.pop() // [1, 2, 3, 4]
```

注意：上面说的“数组的尾部”元素是指最后一个**非字符串 key** 对应的元素，因为数组允许添加字符串 key。

```js
let a = [2,3]
a.foo = 'bar' // (2) [2, 3, foo: "bar"]
a.length // 2
a.push(4) // (3) [2, 3, 4, foo: "bar"]
a.pop() // [2, 3, foo: "bar"]
```

`shift`、`unshift` 正好相反，是在数组的头部进行操作。 `shift` 一次只能从头部移除一个元素。

```js
let a = [10]
a.unshift(9) // [9, 10]
a.shift() // [10]
```

也可以一次 `unshift` 多个元素，但要注意元素被添加到数组中的顺序

```js
let a = [10]
a.unshift(8,9) // [8, 9, 10]
```

`a.unshift(8,9)` 等效于先 `a.unshift(9)` 再 `a.unshift(8)`。



## 4、concat

用于把一个或多个数组或多个值与原数组拼接起来，返回拼接后的新数组。

```js
// 使用 const 定义变量是想说明在调用 a1.concat 后, a1 的值不会被改变
const a1 = [1]
const a2 = a1.concat([2, 3]) // [1, 2, 3]
```

参数可以出现单个元素和数组的混合，第一层数组会被解构成单个元素序列，数组内的嵌套数组元素则不会被解构。

```js
const a1 = [1]
a1.concat(2, [3, 4]) // [1, 2, 3, 4]
a1.concat(2, [3, 4, [5, 6]]) // [1, 2, 3, 4, [5, 6]]
```

没有 `ES6` 扩展运算符（`spread`）前复制数组可以使用 `concat` 变通实现。

```js
const a1 = [1, 2]
const a2 = a1.concat([])
console.log(a1 === a2) // false
// ES6 中利用扩展运算符就优雅多了
const a2 = [...a1]
```



## 5、join、sort、reverse

`join` 方法比较简单，可实现将数组各元素连接成字符串，并可以指定分隔符（默认逗号 `,`）

```js
let a = ['hello', 'world']
a.join() // hello,world
a.join(' ') // hello world
```

`sort` 方法用于将数组元素排序，默认是按字母升序排列。

```js
let a = [1, 3, 11, 2]
a.sort() // [1, 11, 2, 3]
```

`sort` 方法可以提供一个排序函数，实现自定义排序。

```js
let a = [1, 3, 11, 2]
a.sort((a, b) => {
  return a - b
})
// [1, 2, 3, 11]
```

`reverse` 方法用来颠倒数组中元素顺序

```js
[...'hello'].reverse().join('') // olleh
```



## 6、filter

filter 方法用于从数组中**过滤得到**（留下满足条件的元素）满足条件的元素。第一个参数是一个过滤函数。

```js
let a = [1, 2, 3]
a.filter(item => {
  return item > 2
})
// [3]
```



## 7、indexOf、lastIndexOf

`indexOf` 方法从参数 `fromIndex`（包含，默认为 0）到数组结尾的范围内搜索指定的元素，返回元素所在数组中的索引。`fromIndex` 如果为负数表示从结尾开始搜索，-1 表示数组最后一位。

语法：`indexOf(searchElement, ?fromIndex)`

```js
let a = [1, 2, 3, 1]
a.indexOf(1) // 0
a.indexOf(1, 1) // 3
a.indexOf(1, -1) // 3
```

`lastIndexOf` 方法从参数 `fromIndex`（包含，默认为 0）到结尾的范围内搜索指定的元素，返回元素所在数组中的索引。

语法：`lastIndexOf(searchElement, ?fromIndex)`

```js
let a = [1, 2, 3, 1]
a.lastIndexOf(1) // 3
```



## 8、Array.isArray、Array.of、Array.from

`Array.isArray` 用以判断指定的对象是否是 `Array` 类型。

```js
// 以下均返回 true
Array.isArray([])
Array.isArray([...'foo'])
Array.isArray(new Array())

// 以下均返回 false
Array.isArray(null)
Array.isArray(undefined)
Array.isArray(1)
Array.isArray(true)
Array.isArray('foo')
Array.isArray(new Set())
Array.isArray(new Map())
```

Array.of 用以创建 Array 实例。可以一次指定多个元素值用以初始化数组。

```js
Array.of(3, 4, 5) // [3, 4, 5]
Array.of(3) // 3
```

我们知道还可以使用构造函数 new Array() 实例化数组，但这种方式在只指定一个参数时有歧义。

```js
new Array(3, 4, 5) // [3, 4, 5]
new Array(3) // [empty × 3]
```

我们也许期望 `new Array(3)` 得到一个仅包含一个元素 `3` 的数组`[3]`，但实际得到的是有三个空元素的数组。所以为了避免歧义应该多使用 `Array.of` 而避免使用构造函数来初始化数组。

`Array.from` 方法用以将两类对象转换为真正的数组。

1. 类数组对象（`array-like`），如： 函数体内 `arguments` 属性， `NodeList` 对象等等。
2. 部署了 Iterator 接口的对象，原生具备 `Iterator` 接口的的数据类型有：`Array`，`Map`，`Set`，`String`，`TypedArray`，`Argument`，`NodeList`。

```js
let arrayLike = {
    '0': 'a',
    '2': 'c',
    length: 3
};
// ES6
Array.from(arrayLike) // (3) ["a", undefined, "c"]

// ES5
let a = [].slice.call(arrayLike)
```

另一个使用场景是用于数组的复制，但是注意这种复制仅仅是“浅拷贝”。

```js
let a = [1, 2, 3]
// ES6 之前数组的复制只能使用变通办法
let b = a.concat([])
// ES6 可以使用 Array.from() 
b = Array.from(a)
a === b // false
```



## 9、keys、values、entries

keys 方法用以获取数组元素对应的索引值集合，返回迭代器类型（`Iterator`），可用 `for...of` 进行遍历；或使用扩展运算符（ 三个点 `...` ）进行解构赋值。

```js
let a = [7, 8, 9]
a.keys() // Array Iterator {}
console.log(...a.keys()) // 0, 1, 2
```

注意：该方法只返回数组的数字 `keys`，**不包含字符串 `key`**。

```js
let a = [7, 8]
a.foo = 'bar'
a.push(9)
console.log(a) // (3) [7, 8, 9, foo: "bar"]
console.log(a.length) // 3
console.log(...a.keys()) // 0 1 2
```

`values` 方法同 `keys` 方法类型，用以获取数组中所有的元素集合，返回迭代器类型（`Iterator`）。也不包含字符串 `key` 对应的 `value`。

```js
let a = [7, 8]
a.foo = 'bar'
[...a.values()] // (2) [7, 8]
```

`entries` 方法返回 `entry`（key, value） 集合，也是返回迭代器类型（`Iterator`）。也不包含字符串 `key` 对应的 `entry`。

```js
let a = [7, 8]
a.foo = 'bar'
[...a.entries()] // [[0, 7], [1, 8]]
```



## 10、every、some

`every` 方法是对数组中的每一项运行给定函数，如果该函数对每一项返回 `true` ,则返回 `true`。用以检测数组中所有元素是否均满足指定的条件。

语法：`every(callbackfn, ?thisArg)`

```js
let a = [1, 2, 3]
a.every(item => item < 10) // true
a.every(item => item < 3) // false
```

`some` 方法是对数组中每一项运行指定函数，如果该函数对任一项返回 `true`，则返回`true`。用以检测数组中所有元素是否有任意一个满足指定的条件。

语法：`every(callbackfn, ?thisArg)`

```js
let a = [1, 2, 3]
a.every(item => item < 3) // false
a.some(item => item < 3) // true
```

个人觉得 `some` 方法命名为 `any` 也许更贴切。



## 11、find、findIndex

语法：`find/findIndex(predicate, ?thisArgs)`

`find` 方法用于找出第一个符合条件的数组元素，如果找不到返回 `undefined`。

```js
let a = ['foo', 'bar', 'baz']
a.find(item => item.startsWith('b')) // bar
a.find(item => item.startsWith('f')) // foo
a.find(item => item.startsWith('a')) // undefined
```

`findIndex` 方法用于找到第一个符合条件的数组元素所在的索引，找不到返回 `-1`。

```js
let a = ['foo', 'bar', 'baz']
a.findIndex(item => item.startsWith('b')) // 1
a.findIndex(item => item.startsWith('f')) // 0
a.findIndex(item => item.startsWith('a')) // -1
```



## 12、map、flat、flatMap

`map` 方法允许提供一个回调函数，将回调函数的返回值组成新的数组返回。

语法：`map(callbackfn, ?thisArg)`

`callbackfn` 默认有三个参数：`value`，`index`，`self`

```js
let a = [1, 2, 3]
a.map(item => item * 2) // [2, 4, 6]
```

`flat` 方法用于将嵌套数组“压平”，变成一位数组。返回新的数组，原数组不变。

注意：该方法默认只拉平第一层，第二层原样输出。第一个参数可以指定拉平的层数，默认为 `1`。

```js
let a = [1, [2, [3, 4]], 5]
a.flat() // [1, 2, [3, 4], 5]
a.flat(2) // [1, 2, 3, 4, 5]
```

如果不管有多少层都要拉平，可以指定一个大致的必定超越层数的数字，或直接用 `Infinity`。

```js
[1, [2, [3, 4]], 5].flat(Infinity)
```

`flat` 会忽略空位（注意：空位是指某位置上没有值，而不是指空值 `null`，`undefined` 等）

```js
let a = [1,,3]
// [empty, 1]
//	0: 1
//	2: 3
//	length: 3
//	__proto__: Array(0)
[1,,3].flat() // [1, 3]
[1, undefined, 3].flat() // [1, undefined, 3]
[1, null, 3].flat() // [1, null, 3]
```

`flatMap` 方法对原数组的每个成员执行一个函数，然后对返回值组成的数组执行 `flat` 方法。返回一个新数组，原数组不变。

语法: `flatMap(callback, ?thisArg)`

`callback` 默认有三个参数：`value`，`index`，`self`

```js
// 先 map 为 [[1,2], [2, 4], [3, 6]],再 flat 
[1, 2, 3].flatMap(item => [item, item * 2]) // [1, 2, 2, 4, 3, 6]
```

`flatMap` 只能展开一层数组，第二个元素是 `NaN`，是因为它不是数值。

```js
[1, [2, [3, 4]], 5].flatMap(item => item * 2) // [2, NaN, 10]
```

如果想先压平再 `map` 可以链式调用。

```js
[1, [2, [3, 4]], 5].flat(Infinity).map(item => item * 2) // [2, 4, 6, 8, 10]
```



## 13、reduce、reduceRight

reduce 方法用于迭代数组中的每一个元素，需提供一个迭代函数，每次迭代函数的返回值，都会作为下一次迭代的第一个参数值。

语法：`reduce(callback, ?initial)`

`callback` 有四个参数：`prev`、`now`、`index`、`self`，返回值作为下一次循环时的第一个参数 `prev` 的值。

`initial` 用于指定一个初始值，**如果省略，则将数组的第一个元素作为初始值，从第二个元素开始遍历**。

```js
let a = [1, 2, 3, 4, 5]
a.reduce((prev, now) => {
    console.log('prev:', prev, 'now:', now)
    return prev + now
})
prev: 1 now: 2
prev: 3 now: 3
prev: 6 now: 4
prev: 10 now: 5
15
```

如果指定 `initial`，则从数组的第一个元素遍历，第一次遍历时 `prev` 等于这个提供的 `initial` 参数值。

```js
let a = [1, 2, 3, 4, 5]
a.reduce((prev, now) => {
    console.log('prev:', prev, 'now:', now)
    return prev + now
}, 10)
prev: 10 now: 1
prev: 11 now: 2
prev: 13 now: 3
prev: 16 now: 4
prev: 20 now: 5
25
```

实现类似 `join` 方法的输出

```js
let a = [1, 2, 3, 4, 5]
a.reduce((prev, now) => prev + ',' + now) // "1,2,3,4,5"
```

`reduceRight` 方法和 `reduce` 方法类似，唯一不同的是它从数组的后面向前迭代。



## 14、toString、toLacaleString

`toString` 方法返回一个字符串，将数组元素以逗号分隔，和 `join(',')` 等效。

当一个数组被作为文本值或者进行字符串连接操作时，将会自动调用其 `toString` 方法。

```js
a = [1, 2, 3, 4]
a.toString() // 1,2,3,4
a.join(',') // 1,2,3,4
```

`toLocaleString` 返回一个字符串表示数组中的元素序列。数组中的元素将使用各自的 `toLocaleString` 方法转成字符串，这些字符串将使用一个特定语言环境的字符串（例如一个逗号 ","）隔开。

```js
let a = [1, 'a', new Date()]
a.toString() // "1,a,Thu Jun 18 2020 14:51:44 GMT+0800 (中国标准时间)"
a.toLocaleString('en') // "1,a,6/18/2020, 2:51:44 PM"
a.toLocaleString('zh', {timeZone: 'Asia/Shanghai'}) // "1,a,6/18/2020, 2:51:44 PM"
a.toLocaleString('zh', {timeZone: 'Asia/Shanghai'}) // "1,a,2020/6/18 下午2:51:44"
```



## 15、fill

`fill` 方法用一个固定值填充一个数组中从起始索引（包含）到终止索引（不包含）内的全部元素。该方法会改变原数组。返回值是原数组。

语法：`fill(value, ?start, ?end)`

```js
let a = [1, 2, 3, 4]
// 如果不指定 start 和 end 表示填充所有位置
a.fill(9) // [9, 9, 9, 9]
// 如果 end 省略表示数组长度。
a.fill(8, 1) // [9, 8, 8, 8]
// 由于 end 位置不包含，所以以下操作不会发生变化
a.fill(7, 1, 1) // [9, 8, 8, 8]
// 如果 end 超过数组长度，会校准为数组长度
a.fill(6, 0, 100) // [6, 6, 6, 6]
// 和数组中其他方法一样，索引位都是可以用负数的，-1 表示最后一位
a.fill(5, -1) // [6, 6, 6, 5]
```



## 16、copyWithin

copyWithin 方法用于浅拷贝数组中从起始索引（包含）到结束索引（不包含）内的元素到指定原数组中的指定位置。该方法会导致原数组发生改变，但原数组的长度不会发生改变。

语法：`copyWithin(target, start, ?end)`

target 表示要拷贝到的位置， `start` 表示拷贝范围的开始（`include`），end 表示拷贝范围的结束（`exclude`），可省略，省略或大于数组长度时均表示数组长度。

```js
let a = [1, 2, 3, 4, 5, 6]
a.copyWithin(1, 3) // [1, 4, 5, 6, 5, 6]
```



## 17、forEach

`forEach` 方法用来迭代数组，是 `ES5` 新增的方法，没有返回值，优点是迭代代码更优雅，缺点是不支持 `break`、`continue`、`return` 等循环流程控制语句。

语法： `forEach(callback)`

`callback` 有三个参数：`value`，`index`，`self`

```js
let sum = 0
[1,2,3].forEach(item => sum += item)
console.log(sum) // 6
```



# 关于 Array 操作在 Vue 中的更新

以上数组方法可以分为两类：

1. 变更方法（执行后原数组数据发生变更）- `pop`、`push`、`shift`、`unshift`、`splice`、`sort`、`reverse`
2. 非变更方法（执行后始终返回一个新数组，原数组不变）- `slice`、`concat`、`filter`

数组是一个对象引用，如果不进行特殊处理的话，Vue 是不能感知到数组内部数据发生了变更。可喜的是，**Vue 对数组的变更方法进行了包裹**，使得我们直接操纵它们**可以触发视图更新**。

对于**非变更方法**，如果希望触发视图更新，可以有几种方法实现：

> 注：以下本小节代码示例来自 Vue [官方文档](https://cn.vuejs.org/v2/guide/list.html#%E6%95%B0%E7%BB%84%E6%9B%B4%E6%96%B0%E6%A3%80%E6%B5%8B)

1、使用新数组替换旧数组

```js
this.items = this.items.filter(item => {
  return item > 0
})
```

2、使用计算属性

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```

```js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

3、使用 method

在计算属性不适用的情况下，可以使用方法：

```html
<ul v-for="set in sets">
  <li v-for="n in even(set)">{{ n }}</li>
</ul>
```

```js
data: {
  sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

