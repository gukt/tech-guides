自 JDK 8开始，引入了 [Optional](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html) 对象，用以表示一个方法返回值、或一个变量有可能为 `null`。它能帮我们的代码减少出现 `NullPointerException` 的机会。

`Optional` 在语义上更精确，且提供很多便利的方法，可以让我们简化代码，使其更优雅、易读。

另外，主流 IDE 、或代码静态检查工具，也提供对 `Optional` 不严谨使用的地方做静态检查，并会提示警告信息。

本文内容主要包括：

- 如何实例化一个 `Optional` 对象？
- 两个 `Optional` 对象之间的比较。
- 如何获取值，提供默认值，抛出异常等。
- 值过滤、值映射。
- 将传统的层层嵌套判断简化为使用 `Optional` 链式调用。
- 一些实践建议。



## 实例化

`Optional` 类没有提供公开的构造函数，它有三个静态方法用于初始化实例：[empty](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html#empty())、[of](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html#of(T))、[ofNullable](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html#ofNullable(T))。

静态方法 `empty` 用于创建一个没有值的 `Optional` 对象：

```java
Optional<String> result = Optional.empty();
```

静态方法 `of` 用于提供一个非 `null` 值：

```java
Optional<String> result = Optional.of("hello");
```

如果静态方法 `of` 提供的值为 `null`，则抛出 `NullPointerException` ( 以下称 NPE )：

```java
String s = null;
Optional<String> result = Optional.of(s); // java.lang.NullPointerException
```

因此，在使用静态方法 `of` 初始化实例时，必须要保证参数一定不是 null。

如果值可能为 `null`，则使用 `ofNullable`：

```java
Optional result = Optional.ofNullable(null);
assertFalse(result.isPresent());
```

实际上 `ofNullable(null)` 和 `Optional.empty()` 是等效的，源码如下：

```java
public static <T> Optional<T> ofNullable(T value) {
  return value == null ? empty() : of(value);
}
```



## 对象比较

两个 `Optional` 对象使用 `equals` 比较时，实际上是用他们引用的 `value` 进行 `equals` 比较，源码如下：

```java
public boolean equals(Object obj) {
  if (this == obj) {
    return true;
  }

  if (!(obj instanceof Optional)) {
    return false;
  }

  Optional<?> other = (Optional<?>) obj;
  return Objects.equals(value, other.value);
}
```

以下使用 `equals` 和 `==` 比较，注意它们之间的差异：

```java
Integer n1 = 1;
Integer n2 = 1;
Optional<Integer> result1 = Optional.of(n1);
Optional<Integer> result2 = Optional.of(n2);
assertTrue(result1.equals(result2));
assertFalse(result1 == result2);
```

因为 `result1` 和 `result2` 是两个不同对象，因此 `result1 == result2` 返回 `false`。



## 获取值

要想获取 `Optional` 对象的值，使用 `get` 方法：

```java
Optional<String> result = Optional.of('hello');
System.out.println(result.get());
```

如果在 `value` 为 `null` 的 `Optional` 对象上调用 get 方法，会抛出 `NoSuchElementException`：

```java
System.out.println(Optional.empty().get());
```

因此，在调用 `get` 方法之前，一般需使用 `isPresent` 方法先判断，防止抛出 `NPE`：

```java
if (result.isPresent()) {
  System.out.println(result.get());
}
```

`ifPresent(Consumer<? super T> consumer)` 方法可以指定一个 Consumer 来消费值（如果有的话，否则什么都不做）。

因此，以上使用了 isPresent 的判断语句可以简写为：

```java
result.ifPresent(System.out::println);
```

JDK 11 新增的方法 `isEmpty` 和 `isPresent` 正好相反。

JDK 9新增了 `ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)` 方法，如果值存在则由 Consumer 消费；反之，执行第二个参数指定的动作。



## 返回默认值或抛出异常

使用 `orElse` 也可以获取值，该方法可以指定在值不存在时指定默认值： 

```java
Optional<String> result = Optional.empty();
System.out.println(result.orElse("hello")); // hello
```

与 `orElse` 类似的还有两外两个方法：`orElseGet`、`orElseThrow`，这两个方法都可以接受一个 `Supplier` 函数作为参数，由 `Supplier` 函数提供默认值。

以下语句等效于 `result.orElse("hello")`：

```java
String s = result.orElseGet(() -> "hello");
```

JDK 9 新增了 `Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)` 方法，和 `orElseGet` 类似，不同点是它返回类型为 `Optional` ：

```java
Optional<String> optional = result.or(() -> Optional.of("hello"));
```

方法 `<X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X` 用以在获取不到值时抛出指定的异常：

```java
result.orElseThrow((() -> new RuntimeException("No result."));
```

JDK 10 新增了 `orElseThrow` 无参数版，当值没有的时候，抛出 NoSuchElementException 异常。这和直接调用 `get` 行为是一样的，只是语义上更明确。



## 值过滤/测试

`filter` 方法用于在值上应用一个测试，如果测试成功，返回对象本身；如果值不存在、或测试不成功时，返回 `Optional.empty()`。

```java
Optional<String> result = Optional.of('foo@domain.com').filter(s -> s != null && s.contians("@"));
assertTrue(result.isPresent());
```



## 值映射/转换

 `map`、`flatMap`方法可以提供一个 `mapper` 函数，该函数用以对 `Optional` 对象的值进行映射或转换，如果值存在，就应用 `mapper`，返回一个新的 `Optional` 对象，新 Optional 对象的值由 `mapper` 函数的返回值决定；反之，返回 `Optional.empty()` 。

以下是将 `Optional<User>` 类型映射成 `Optional<String>` 类型对象，并将名称转换为小写：

```java
Optional<String> result = Optional.of(User.of("Tom")).map(u -> u.getName().toLowerCase());
```

`flatMap` 方法和 `map` 方法的唯一不同是 `mapper` 函数的返回值类型不同：

```java
Optional<String> result1 = Optional.of("1");
Optional<Integer> result2 = result1.flatMap(s -> Optional.of(Integer.parseInt(s) * 100));
result2.ifPresent(System.out::println); // 100
```

从示例可以看出，`flatMap`方法中的 `mapper` 参数返回的是 `Optional` 对象，而 `map` 方法返回的是 value。



## 链式调用

假设有一个这样的旧的代码调用：

```java
User user = User.of("Tom")
String name = user.getAddress().getCity().getName().toLowerCase();
```

相关类的定义如下：

```java
@Getter
@RequiredArgsConstructor(staticName = "of")
private static class User {
  @NonNull private String name;
  private String phone;
  private String email;
  private Address address;
}

private static class Address {
  @Getter private City city;
}

static class City {
  @Getter String name;
}
```

上面的代码调用面临最大的问题是： `getAddress()`、`getCity()`、`getName()` 每个方法都有可能返回 `null`。所以产生 `NPE` 隐患无处不在。

为了严谨，每一步都要进行判断：

```java
String name = "unknown";
if(user.getAddress() != null && user.getAddress().getCity() !=null) {
  name = user.getAddress().getCity().getName();
  if(name != null) {
    name = name.toLowerCase()
  }
}
```

或者这样：

```java
String name = "unknown";
if(user.getAddress() != null) {
  Address addr = user.getAddress();
  if(addr != null) {
    City city = addr.getCity();
    if(city != null) {
      if(city.getName() != null) {
        name = city.getName().toLowerCase();
      }
    }
  }
} 
```

然而，无论哪种写法，都是即冗余又难读。我们可以使用 `Optional` 改写：

```java
Optional.ofNullable(user.getAddress())
  .map(addr -> addr.getCity()) // ➊
  .map(city -> city.getName()) // ➋
  .map(name -> name.toLowerCase()) // ➌
  .orElse('unknown') // ➍
```

➊ 如果 `getAddress()` 有值，则返回值为 city 的新的 Optional 对象；反之，返回 `Optional.empty()`。

➋ 如果 `getCity()` 有值，则返回值为 name 的新的 Optional 对象；反之，返回 `Optional.empty()`。

➌ 如果 `getName()` 有值，则返回值为 name 的小写形式的 Optional 对象；反之，返回 `Optional.empty()`。

➍ 最后，如果值为空，返回字符串 "unknown" 。

代码变得更加简洁优雅，同时具备更好的可读性。



## 实践建议

1、不建议作为方法或构造函数的参数

```java
public ApiResponse(int code, Optional<String> error, Optional<Object> data)
```

调用：

```java
ApiResponse resp = new ApiResponse(0, Optional.empty(), "ok");
ApiResponse resp = new ApiResponse(1, "Bad request", Optional.empty()) 
```

这实在是太丑陋了，对于方法调用，若要表示不存在，传递 null 并结合重载方法更为简洁。

2、不建议用在对象字段上

由于 Optional 类没有实现 Serializable 接口，因此在使用 JDK 序列化机制的对象字段上不要使用 Optional 类型。

> 注意：Jackson 库可以支持对 Optional 类型的字段的序列化和反序列化。

3、主要用于返回值类型

```java
Optional<User> findByName(String name) {
  User user = userRespository.findByName(name);
  return Optional.ofNullable(user);
}
```

Optional 类型的返回值在语义上更明确。比使用 @Nullable 注解要好。

```java
@Nullable
User findByName(String name) {
  return userRespository.findByName(name);
}
```

尽管使用了 `@Nullable` 注解可以提醒开发者注意返回值可能为 `null`，且 IDE 也会帮我们检查错误并发出警告，但开发者经常会忽略这些信息而写出有缺陷的代码，而 Optional 语义上更明确，更能引起开发者注意。

4、当明确值不能为 null 时，推荐使用 `of` 而非 `ofNullable`

很多人可能觉得：使用 `of` 时，如果提供的 `value` 是 `null`，则会产生 `NPE`，而 `ofNullable` 就不会，所以认为 `ofNullable` 应该更安全、更应该推荐使用。

实际上，这种理解是错误的。

因为，当我们明确某 value 在真实的逻辑中一定不能为 null 时，此时如果你使用了 `ofNullable` ，本质上意味着你内心假设这种情况允许发生，但实际上这种假设是错误的，因为和真正逻辑要求正好是矛盾的 ，如果你错误地使用了 `ofNullable` 而非 `of`，尽管当 value 为 null 不会产生 NPE， 且代码会继续运行。但这只是将真正的逻辑问题隐藏起来了，并延迟暴露了而已，真正好的实践是 `fail-fast`，及早暴露问题、并修复代码缺陷。



## 总结

不建议在字段，方法或构造器的参数中使用 `Optional` ，如果方法的返回值可能为 `null`，则建议使用 `Optional` 作为返回值类型。

在使用 JDK 序列化机制的环境中，要考虑到 `Optional` 并没有实现 `Serializer` 接口。

在实例化 `Optional` 时，如果逻辑上明确值一定不能为 `null` 时，则强烈建议使用 `of` 静态方法，反之，使用 `ofNullable` 静态方法。



## 参考

1. [Optional 官方文档](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html) 