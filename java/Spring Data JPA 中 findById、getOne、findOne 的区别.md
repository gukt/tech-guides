使用 Spring Data JPA 时，经常会看到 `findById`、`getOne`、`findOne` 三个方法。

从字面上理解，他们都是从数据中根据 `ID` 获取单个实体对象，但他们的底层获取机制、返回值类型、以及抛异常的机制是不一样的。



# `findById` 方法

`findById` 方法会立即（`EAGER`）访问数据库，并返回和 `ID` 关联的真实对象，如果没有找到，则返回 `Optional.empty()`。

方法的定义如下：

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {	
	/**
	 * Retrieves an entity by its id.
	 *
	 * @param id must not be {@literal null}.
	 * @return the entity with the given id or {@literal Optional#empty()} if none found.
	 * @throws IllegalArgumentException if {@literal id} is {@literal null}.
	 */
	Optional<T> findById(ID id);
}
```



# `getOne` 方法

`getOne` 方法并不会立即访问数据库，返回一个代理（`proxy`）对象，这个代理对象是对实体对象的引用。它是一个延迟加载方法。只有使用代理对象访问对象属性时才会去访问数据库并获取指定 `ID` 对应的真实对象，如果找不到，则抛出 `EntityNotFoundException`。

方法的定义如下：

```java
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {
	/**
	 * Returns a reference to the entity with the given identifier. Depending on how the JPA persistence provider is
	 * implemented this is very likely to always return an instance and throw an
	 * {@link javax.persistence.EntityNotFoundException} on first access. Some of them will reject invalid identifiers
	 * immediately.
	 *
	 * @param id must not be {@literal null}.
	 * @return a reference to the entity with the given identifier.
	 * @see EntityManager#getReference(Class, Object) for details on when an exception is thrown.
	 */
	T getOne(ID id);
}
```



# `findOne` 方法

除了 `findById`、`getOne` 外，`Spring Data JPA` 还有两个 `findOne` 方法：

- `Optional<T> findOne(@Nullable Specification<T> spec)`

- `<S extends T> Optional<S> findOne(Example<S> example);`

他们的定义如下：

```java
public interface QueryByExampleExecutor<T> {
/**
	 * Returns a single entity matching the given {@link Example} or {@literal null} if none was found.
	 *
	 * @param example must not be {@literal null}.
	 * @return a single entity matching the given {@link Example} or {@link Optional#empty()} if none was found.
	 * @throws org.springframework.dao.IncorrectResultSizeDataAccessException if the Example yields more than one result.
	 */
	<S extends T> Optional<S> findOne(Example<S> example);
}
```

```java
public interface JpaSpecificationExecutor<T> {
/**
	 * Returns a single entity matching the given {@link Specification} or {@link Optional#empty()} if none found.
	 *
	 * @param spec can be {@literal null}.
	 * @return never {@literal null}.
	 * @throws org.springframework.dao.IncorrectResultSizeDataAccessException if more than one entity found.
	 */
	Optional<T> findOne(@Nullable Specification<T> spec);
}
```

他们都是返回 `Optional` 对象的，且都是 `EGAER`（立即） 访问数据库，获取相应实体对象。

**综上总结**：以 `find` 开头的方法会立即访问数据库，且返回 `Optional` 对象。



# 如何选择

从上面我们可以看出，`findById`、`findOne` 和 `getOne` 最重要的区别就是：**延迟加载**还是**立即加载**的不同。

因此，问题就变得简单了：在需要延迟加载的地方使用 `getOne`。

举个例子：假如有一个 `Post` 对象和一个 `Comment` 对象，`Comment` 对象中有一个对 `Post` 对象的引用，`Post` 和 `Comment` 是一对多的关系。

`Post`：

```java
@Entity
@Table(name = "t_posts")
public class Post {

    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private Long id;

    private String title;
}
```

`Comment`：

```java
@Entity
@Table(name = "t_comments")
public class Comment {

    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private Long id;

    private String content;

    @ManyToOne  
    private Post post;
}
```

假设我们现在要添加一条评论，代码如下：

```java
public Comment addComment(long postId) {
  final Post p = postRepository.findById(postId); // #1
  Comment c = new Comment();
  c.setContent("xxx");
  c.setPost(p);
  commentRepository.save(c);
}
```

上面的代码中，如果使用的是 `findById`，则 `SQL` 输出为：

```sql
select id, title from t_posts where id=? 
insert into t_comments (id, content, postId) values (?, ?, ?)
```

如果使用的是 `getOne`，则 `SQL` 输出为：

```sql
insert into t_comments (id, content, postId) values (?, ?, ?)
```

很显然，新建 `Comment` 对象时，必须要设置它关联的 `Post` 对象，而如果使用 `findById` 来获取 `Post` 对象是立即加载的，此例中，并不需要立即加载，只需要返回一个代理对象即可，因此使用 `getOne` 这种延迟加载策略可以获得更优的性能。

