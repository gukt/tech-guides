findById

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

返回一个 Optional 对象，是定义在 CrudRepository 接口中的。

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

返回一个到实体对象的引用，这个实体对象与指定的 ID关联。如果关联的实体对象不存在，则抛出 javax.persistence.EntityNotFoundException 。



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

