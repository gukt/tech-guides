数组是 JavaScript 应用程序中使用最频繁的数据结构之一，有必要把常用方法的使用搞透彻了。



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

`start` 也可以为负数，最后一位元素用 `-1` 表示（内部会用 `-1 + 数组长度` 来换算成正数值）。

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

`splice` 的作用是用以拼接数组，会更改元素数组，返回被切下的数组。它能够从指定位置开始连续删除指定个数的元素后，再插入多个元素。`start` 可以为负数，`-1` 表示指向最后一位元素（内部用 `-1 + 数组长度` 换算成正数 `start` 值）。

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

`push` 和 `pop` 是在数组的尾部进行操作。可以一次 `push` 多个元素。但 `pop` 只能一次移除一个。

```js
let a = [1, 2]
a.push(3) // [1, 2, 3]
a.push(4, 5) // [1, 2, 3, 4, 5]
a.push([6, 7]) // [1, 2, 3, 4, 5, [6, 7]]
a.pop() // [1, 2, 3, 4, 5]
a.pop() // [1, 2, 3, 4]
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

`join` 方法比较简单，可实现将数组中各元素连接成字符串，并可以提供分隔符（默认逗号 `,`）

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

