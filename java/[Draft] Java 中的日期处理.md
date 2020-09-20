Gregorian calendar， 格列高利历，公历。





## 时间概念

JDK 8 中时间 API 的概念有：时间点，时间段，时区、时间单位、时间调整，格式化等。



### 时间点

常见表示时间点的对象有 `LocalDateTime`、`LocalDate`、`LocalTime`、`Instant`，他们都实现了 [java.time.temporal](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/Temporal.html) 接口。

其中 `LocalDateTime`、`LocalDate`、`LocalTime` 表示本地时间，使用系统时区；而 `Instant` 是 UTC 时间。

```java
System.out.println(Instant.now()); // 2020-07-19T18:40:35.548Z
System.out.println(LocalDateTime.now()); // 2020-07-20T02:40:35.600
```

> Instant.now() 输出的字符串个是为：[ISO8601](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html) 日期和时间格式

可以看出，两个时间相差了 8 个小时，这是因为北京时间比 `UTC` 时间要早 8 个小时。

`LocalDate` 只包含日期部分，`LocalTime` 只包含时间部分，而 `LocalDateTime` 包含时间和日期。

```java
LocalDateTime now = LocalDateTime.now(); // 2020-07-20 03:05:13.117
LocalDate date = now.toLocalDate(); // 2020-07-20
LocalTime time = now.toLocalTime(); // 03:05:13.117
```



### 时间段

时间段的基本接口是 [java.time.temporal.TemporalAmount](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/TemporalAmount.html)。

实现该接口的类有：Period，Duration， Period 表示日期部分，Duration 表示时间部分。

TODO Duration，Peroid 详解





```
get(TemporalUnit): long
getUnits(): List<TemporalUnit>
addTo(Temporal): Temporal
subtractFrom(Temporal): Temporal
```



### 时区

可以将一个 `Instant` 对象转换为任意时区的时间：

```java
now.atZone(ZoneId.of("Asia/Shanghai")) // 2020-07-20T02:45:00.389+08:00[Asia/Shanghai]
```

也可以指定时区偏移，将其转换为其他时区的时间：

```java
now.atOffset(ZoneOffset.ofHours(8)) // 2020-07-20T02:45:00.389+08:00
```

比较两个不同时区的时间：

```java

```



### 时间单位





[java.time.temporal](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/Temporal.html) 接口：

```java
minus(long amountToSubtract, TemporalUnit unit);  // - amountToSubtract * unit
minus(TemporalAmount amount);                     // - amount

plus(long amountToAdd, TemporalUnit unit);        // + amountToSubtract * unit
plus(TemporalAmount amount);                      // + amount

with(TemporalAdjuster adjuster);
with(TemporalField field, long newValue);
```



Java 中使用 [ChronoUnit](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/ChronoUnit.html) 枚举表示时间单位。

最长用的有：年、月、日、小时、分钟、秒、毫秒、微秒、纳秒。

```java
ChronoUnit.YEARS; // 年
ChronoUnit.MONTHS; // 月
ChronoUnit.DAYS; // 日
ChronoUnit.HOURS; // 小时
ChronoUnit.MINUTES; // 分钟
ChronoUnit.SECONDS; // 秒
ChronoUnit.MILLIS; // 毫秒
ChronoUnit.MICROS; // 微秒
ChronoUnit.NANOS // 纳秒
```

不是很常用的有：

```java
ChronoUnit.CENTURIES // 世纪
ChronoUnit.DECADES //
ChronoUnit.ERAS
ChronoUnit.FOREVER
ChronoUnit.HALF_DAYS
ChronoUnit.MILLENNIA
ChronoUnit.NANOS
```





## Temporal



## LocalDateTime、LocalDate、LocalTime

获取当前时间（本地时间，不含时区）：

```java
LocalDateTime.now() // 2020-07-20T01:39:13.645
```

获取时间的年、月、日、时、分、秒、毫秒、微秒、纳秒

```java
LocalDateTime dateTime = LocalDateTime.now(); // 2020-07-20T01:49:50.491
dateTime.getYear(); // 2020，年份数值
dateTime.getMonth(); // JULY，月份，Month 对象
dateTime.getMonthValue(); // 7, 月份数值，1-12
dateTime.getDayOfWeek(); // MONDAY，星期几，DayOfWeek 对象
dateTime.getDayOfWeek().getValue(); // 1, 星期数值，周一到周日为 1-7
dateTime.getDayOfMonth(); // 20，一个月中的第几天
dateTime.getDayOfYear(); // 202，一年中的第几天
dateTime.getHour(); // 1,
dateTime.getMinute(); // 49
dateTime.getSecond(); // 50
dateTime.getNano(); // 491000000，纳秒, 没有获取毫秒和微秒的方法，可通过纳秒进行换算
```

由于 `LocalDateTime` 类实现 `Temporal` 接口，所以可以对时间进行加减操作。

```java
plus(TemporalAmount amount)
plus(long amountToAdd, TemporalUnit unit)
minus(TemporalAmount amount)
minus(long amountToAdd, TemporalUnit unit) 
```

以下以 `plus` 举例，`minus` 方法类似。

使用 `plus(TemporalAmount amount)` 方法增加日期或时间，`TemporalAmount` 的子类有 `Period`、`Duration`，用来表示一段时间：

新增日期部分使用 `Period`：

```java
LocalDateTime dateTime = LocalDateTime.now(); // 2020-07-20T02:00:52.381
dateTime.plus(Period.ofYears(1)); // 2021-07-20T02:00:52.381，加一年
dateTime.plus(Period.ofMonths(1)); // 2020-08-20T02:00:52.381，加一个月
dateTime.plus(Period.ofDays(1)); // 2020-07-21T02:00:52.381，加一天
dateTime.plus(Period.ofWeeks(1)); // 2020-07-27T02:00:52.381，加一个星期
```

新增时间部分使用 `Duration`：

```java
LocalDateTime dateTime = LocalDateTime.now(); // 2020-07-20T02:07:16.531
System.out.println(dateTime.plus(Duration.ofDays(1))); // 2020-07-21T02:07:16.531，加一天
System.out.println(dateTime.plus(Duration.ofHours(1))); // 2020-07-20T03:07:16.531，加一小时
System.out.println(dateTime.plus(Duration.ofMinutes(1))); // 2020-07-20T02:08:16.531，加一分钟
System.out.println(dateTime.plus(Duration.ofSeconds(1))); // 2020-07-20T02:07:17.531，加一秒
System.out.println(dateTime.plus(Duration.ofMillis(1))); // 2020-07-20T02:07:16.532，加一毫秒
System.out.println(dateTime.plus(Duration.ofNanos(1))); // 2020-07-20T02:07:16.531000001，加一纳秒
```

使用 `plus(long amountToAdd, TemporalUnit unit)` 方法：

```java
dateTime.plus(1, ChronoUnit.DAYS); // 添加一天
```

`LocalDateTime` 还提供了了一些便利方法，用以添加日期或时间部分，比如：

```java
dateTime.plusDays(1);
dateTime.plusMinutes(1);
```

这个和 `dateTime.plus(Duration.ofDays(1))` 或 `dateTime.plus(Period.ofDays(1))`  等效，但更简洁。





## 常用写法

### 获取一天的起点（零点）

```java
LocalDate.now().atStartOfDay()
LocalDate.now().atTime(0, 0);
LocalDate.now().atTime(LocalTime.of(0, 0))
```

还可以指定更具体的时间参数： hour、minutes、seconds、nanoSeconds。

昨天零点：

```java
LocalDate.now().atStartOfDay().minusDays(1)
```

先得到今天的零点，再减去 1 天。还可以减去任意的时间段。

### 计算两个时间点之间的间隔

使用

```java
LocalDateTime now = LocalDateTime.now(); // 2020-07-21T22:24:45.616
LocalDateTime startOfToday = LocalDate.now().atStartOfDay();
// 获取间隔的毫秒数
System.out.println(ChronoUnit.MILLIS.between(startOfToday, now)); // 80685640

```

计算两个时间点间隔的天数

是否跨天，可以指定零点时间偏移

获得今天剩余的毫秒数，可以指定零点时间偏移



### 在某时间点加上一个时间段的几种表示方法

增加 1 年 2 个月 3 天 4 小时 5 分钟 6 秒：

```java
LocalDateTime now = LocalDateTime.now();
// 第一种方法，使用 plus(long amountToAdd, TemporalUnit unit)
LocalDateTime t1 = now.plus(1, ChronoUnit.YEARS)
        .plus(2, ChronoUnit.MONTHS)
        .plus(3, ChronoUnit.DAYS)
        .plus(4, ChronoUnit.HOURS)
        .plus(5, ChronoUnit.MINUTES)
        .plus(6, ChronoUnit.SECONDS);
// 第二种方法：使用 plus(TemporalAmount amountToAdd)
LocalDateTime t2 = now.plus(Period.ofYears(1))
        .plus(Period.ofMonths(2))
        .plus(Period.ofDays(3))
        .plus(Duration.ofHours(4))
        .plus(Duration.ofMinutes(5))
        .plus(Duration.ofSeconds(6));
// 第三种方法：使用便利方法
LocalDateTime t3 =
        now.plusYears(1).plusMonths(2).plusDays(3).plusHours(4).plusMinutes(5).plusSeconds(6L);

System.out.println(t1.equals(t2)); // true
System.out.println(t2.equals(t3)); // true
System.out.println(t1.equals(t3)); // true
```

如果要减少一个时间段，使用 `minus*` 方法。

另外还可以使用 `TemporalUnit.addTo` 方法，但不建议使用这种:

```java
ChronoUnit.HOURS.addTo(now, 1);
```



\#364603 - My VPS is currently offline, And failed to restart



Hi,
My VPS cannot be connected currently, so I entered the user management, and found the status of VPS shows 'offline', I tried to click the 'reboot VPS' and 'start VPS' many times, but I got the error message '1) The operation could not be completed' each time. Could you please help on this, thanks!

Regards,
Gu