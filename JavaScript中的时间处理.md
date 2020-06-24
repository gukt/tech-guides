处理时间是最常见的需求，有必要熟练掌握，本文针对 JavaScript 的内置 Date 对象进行详细介绍。



## 1、简介

JavaScript 提供了内置对象 `Date`，用于处理时间，它是基于 [Unix Time Stamp](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_16)（Unix 时间戳）的，即从 1970 年 1 月 1 日 0 点 0 时 0 分（UTC）到此刻经过的毫秒数（下文称该时间为 `Unix Zero Time`）。

```js
Date.now() // 1593002422045
```

返回自 Unix Zero Time 以来，到代码运行时刻的时间戳。



## 2、创建时间对象

创建一个新的 `Date` 对象唯一的方法是通过 `new` 操作符。如果直接调用 `Date` 函数，返回字符串，而非 `Date` 对象。

`Date` 有四种构造函数：

### 2.1、无参数

调用无参数构造函数，返回代码执行时刻的时间。

```js
let now = new Date()  // Wed Jun 24 2020 20:54:21 GMT+0800 (中国标准时间)
```



### 2.2、Unix 时间戳作为参数值

提供 `Unix` 时间戳作为参数值，该值表示自 “`1970 年 1 月 1 日 00:00:00`” 以来的毫秒数。

```js
// 当前时间
let now = new Date() 
// Wed Jun 24 2020 20:59:39 GMT+0800 (中国标准时间)

// 两小时以后的时间
let later = new Date(now.getTime() + 2 * 60 * 60 * 1000) 
// Wed Jun 24 2020 22:59:39 GMT+0800 (中国标准时间)
```



### 2.3、日期字符串

该字符串必须符合 [IETF-compliant RFC 2822 timestamps](http://tools.ietf.org/html/rfc2822#page-14) 或 [version of ISO8601 ](http://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15) 格式。

```js
// 提供 ISO8601 格式的日期字符串
new Date('2020-06-24T16:06:26.860Z') 
// Thu Jun 25 2020 00:06:26 GMT+0800 (中国标准时间)

// 提供 RFC 2822 格式的日期字符串
new Date('24 Jun 2020 16:06:26 GMT') 
// Thu Jun 25 2020 00:06:26 GMT+0800 (中国标准时间)
new Date('Wed, 24 Jun 2020 16:06:26 GMT') 
// Thu Jun 25 2020 00:06:26 GMT+0800 (中国标准时间)
```

> 注意：
>
> **强烈建议不要使用**这种指定日期字符串的方式构造 `Date` 对象，因为浏览器差异，会导致解析不一致。同时，也不建议使用 `Date.parse()` 方法解析时间。
>
> 考虑自己写一个方法解析时间字符串，或使用一些时间处理框架。



### 2.4、分别提供日期成员

语法：`Date(year, monthIndex, ?day, ?hours, ?minutes, ?seconds, ?milliseconds)`

> `year` - 如果填 0-99 ，会映射为 1900 到 1999年，其他值表示实际年份，也可以小于 1970。
>
> `monthIndex` - 表示月份的索引，从 0 开始表示第 1 月，默认为 0。
>
> `day` - 表示一个月中的第几天，从 1 开始，默认为 1。
>
> `hours` - 表示一天中的小时数（24 小时制），默认为 0。
>
> `minutes` - 表示分钟，默认为 0。
>
> `seconds` - 表示秒，默认为 0。
>
> `milliseconds` - 表示毫秒，默认为 0。

最少提供 `2` 个参数：`year`，`monthIndex`，最多 7 个。

你可能期望仅提供一个参数 `year`，实际上这会被当成“时间戳构造方式”来处理。

```js
new Date(2000)
// Thu Jan 01 1970 08:00:02 GMT+0800 (中国标准时间)
```

下面我们来创建一个 `Date` 对象，希望表示 21 世纪第一天。

```js
// 1月份以 0 开始的
new Date(2000, 0, 1)
// Sat Jan 01 2000 00:00:00 GMT+0800 (中国标准时间)
```

实际上，以上时间并非 `UTC` 时间，传入的参数默认表示的是本地时间。

如果想要转换为 `UTC` 时间，可以调用 `Date.UTC()` 得到时间戳后，再传给 Date 构造函数。

注意：默认创建的对象是表示本地时间的，而非 UTC 时间。因此，以上代表的时间只是北京时间到了世纪之初，而非世界时已经进入了新世纪，因为我们在东八区，比 UTC 时间晚 8 个小时。

```js
let millis = Date.UTC(2000, 0, 1)
// 这才真正的21世纪第一天
new Date(millis)
// Sat Jan 01 2000 08:00:00 GMT+0800 (中国标准时间)
```

如果参数超出合理范围，会自动进位。

```js
new Date(2000, 0, 1)
// Sat Jan 01 2000 00:00:00 GMT+0800 (中国标准时间)

// 月份超出了 11，多了 1 个月，进位到下一年的 1 月。
new Date(2000, 12, 1)
// Mon Jan 01 2001 00:00:00 GMT+0800 (中国标准时间)

// 日期超出了 1 月 的最大值 31，多了 1 天，进位为 2 月 1 日
new Date(2000,0, 32)
// Tue Feb 01 2000 00:00:00 GMT+0800 (中国标准时间)

// 日期超出的天数可以跨多个月
new Date(2000, 0, 60)
// Tue Feb 29 2000 00:00:00 GMT+0800 (中国标准时间)

// 碰到跨 2 月时，还可以自动处理闰月的天数问题
// 2000 年 2 月是 29 天
new Date(2000, 0, 61)
// Wed Mar 01 2000 00:00:00 GMT+0800 (中国标准时间)

// 其他 hour, minutes, minute, milliseconds 类似，不再赘述
```



## 3、方法

### 3.1、各种 toString 方法

```js
let now = new Date(2000, 0)

// 以下输出为本地时间表示的字符串
now.toString() // Sat Jan 01 2000 00:00:00 GMT+0800 (中国标准时间)
now.toDateString() // "Sat Jan 01 2000"
now.toTimeString() // "00:00:00 GMT+0800 (中国标准时间)"
now.toLocalString() // "2000/1/1 上午12:00:00"
now.toLocaleDateString() // "2000/1/1"
now.toLocaleTimeString() // "上午12:00:00"

// 以下输出为 UTC 时间表示的字符串
now.toISOString() // "1999-12-31T16:00:00.000Z"
now.toUTCString() // "Fri, 31 Dec 1999 16:00:00 GMT"
now.toGMTString() // "Fri, 31 Dec 1999 16:00:00 GMT"
now.toJSON() // "1999-12-31T16:00:00.000Z"
```

上述示例可以看出：`toJSON` 方法和 `toISOString` 的输出格式是一样的；`toUTCString` 和 `toGMTString` 的输出格式是一样的。



### 3.2、获取时间各部分的方法

`getter` 方法: 

`getFullYear`，`getMonth`，`getDate`，`getHours`，`getMinutes`，`getSeconds`，`getMilliseconds`，`getUTCFullYear`，`getUTCMonth`，`getUTCDate`，`getUTCHours`，`getUTCMinutes`，`getUTCSeconds`，`getUTCMilliseconds`

`setter` 方法:

`setFullYear`，`setMonth`，`setDate`，`setHours`，`setMinutes`，`setSeconds`，`setMilliseconds`，`setUTCFullYear`，`setUTCMonth`，`setUTCDate`，`setUTCHours`，`setUTCMinutes`，`setUTCSeconds`，`setUTCMilliseconds`

```js
let now = new Date(2000, 0) // Sat Jan 01 2000 00:00:00 GMT+0800 (中国标准时间)
// 输入为 ISO8601 格式，方便查看
now.toISOString() // "1999-12-31T16:00:00.000Z"

// 以下方法基于本地时间输出
now.getFullYear() // 2000
now.getMonth() // 0, 注意：这里以 0 开始的 monthIndex，因此 1 月输出为 0
now.getDate() // 1
now.getHours() // 0
now.getMinutes() // 0
now.getSeconds() // 0
now.getMilliseconds() // 0
// 以上 getter 方法对应的 setter 方法的示例说明略

// 以下方法基于 UTC 的时间
now.getUTCFullYear() // 1999
now.getUTCMonth() // 11，注意：这里以 0 开始的 monthIndex，因此 12 月输出为 11
now.getUTCDate() // 31
now.getUTCHours() // 16
now.getUTCMinutes() // 0
now.getUTCSeconds() // 0
now.getUTCMilliseconds() // 0
// 以上 getter 方法对应的 setter 方法的示例说明略
```

> 注意
>
> `getYear` 已废弃，请使用 `getFullYear`，虽然有些浏览器还支持，但不建议再使用它，因为很明显它有“千年虫问题”（作者也堪称程序界老油条啊，95 年发布 JavaScript，他就没想到 2000 年只有几年就要到了吗？还是明知道就是不想去改？）



### 3.3、其他方法

使用 `getTimezoneOffset` 可以获得本地时区和 `UTC` 之间的偏移分钟数。

```js
new Date().getTimezoneOffset() // 480
```

返回值 `480`分钟，即 `8` 小时，因为本地时间为北京时间，处于东八区。

`valueOf()` 返回时间戳， `getTime()`，`Date.now()` 也返回时间戳。

```js
let timestamp = Date.now() // 1593013216800
let now = new Date(timestamp)
now.valueOf() // 1593013216800
now.getTime() // 1593013216800
```



## 4、总结

1. 强烈建议不要使用 `new Date(dateString)` 或 `Date.parse(dateString)` 方法解析日期，因为各浏览器的实现差异可能会造成解析失败。
2. `getYear`, `setYear` 已经废弃，虽然浏览器还支持它，但不建议使用。
3. 调用那些不带 `UTC` 的方法名，一般都是基于本地时间的；如果想基于 `UTC` 时间，使用那些带 `UTC` 的方法。
4. 以“分别指定时间各部分”的方式构造 `Date` 实例时，默认是基于本地时间的，如果要想基于 `UTC` 时间，可以将各时间参数先传入 `Date.UTC()` 方法，通过该方法返回的时间戳，再传给 `new Date(timestamp)` 创建。