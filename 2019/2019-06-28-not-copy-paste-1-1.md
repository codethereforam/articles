# 做一个不复制粘贴的程序员[1]: 使用模板方法模式（1）- 分页查询实例

对于重复的代码，如果是重复的字符串，我们会想到提出一个变量。如果是重复的代码块，我们会想到提取出一个方法。

但如果这重复的代码块中有一处或几处是会变化的，那么就没那么容易提取出一个方法。说起来有点抽象，下面看一个例子。

## 一、分页查询
写过CRUD的同学肯定写过很多分页查询，分页查询的主要步骤是先校验前端传过来的查询条件（包括哪一页以及每页显示记录数等），如果不合法则设置默认值。然后根据条件查询符合条件的总记录数，以及查询符合条件的一页记录，最后经过处理返回给前端。

这一段话中会变的部分，也就是每个分页查询不同的部分，就是两个查询。这不就是一个模板，对于不同的分页查询，只要往其中填两块代码。

不过有的同学可能会说平时用分页插件，不需要写查询总数的方法，不过有时还是需要自己写查询总数的方法来优化SQL滴。

## 二、Java8之前的方式

下面是一些核心类

```java
/**
 * 分页查询结果对象
 *
 * @param <T> 分页查询对象类型
 */
@AllArgsConstructor
@Getter
@ToString
public final class Page<T> implements Serializable {
    /**
     * 总记录数
     */
    @NonNull
    private final Long total;
    /**
     * 当前记录集合
     */
    @NonNull
    private final List<T> rows;
}
```

```java
/**
 * 分页查询模板（Java8之前的写法）
 */
public abstract class AbstractPageTemplate<E> {
    /**
     * "pageNumber"
     */
    public static final String PAGE_NUMBER = "pageNumber";
    /**
     * "pageSize"
     */
    public static final String PAGE_SIZE = "pageBegin";
    /**
     * "pageBegin"
     */
    private static final String PAGE_BEGIN = "pageBegin";

    /**
     * 获取分页结果对象
     * <p>
     * 示例:
     *
     * <pre>
     * {@code
     *      Page<FooDTO> fooDTOPage = PageUtil.page(mapper::selectPageCount1, mapper::selectPageEntities1, paramMap)
     * }
     * </pre>
     *
     * @param paramMap 分页查询参数（key需要包含”pageNumber“和”pageSize“，否则默认查询第一页的20条记录）
     * @return 分页结果对象集合
     */
    public Page<E> page(Map<String, Object> paramMap) {
        Objects.requireNonNull(paramMap);
        // 获取页数
        Integer pageNumber = (Integer) paramMap.get(PAGE_NUMBER);
        // 校验页数，不合法则设置默认值
        pageNumber = pageNumber == null || pageNumber <= 0 ? 1 : pageNumber;
        // 获取页大小，不合法设置默认值
        Integer pageSize = (Integer) paramMap.computeIfAbsent(PAGE_SIZE, k -> 20);
        // 计算SQL中limit的offset（Mysql）
        paramMap.put(PAGE_BEGIN, (pageNumber - 1) * pageSize);
        // 查询符合条件的总记录数
        long total = pageCount(paramMap);
        if (total <= 0) {
            return new Page<>(0L, new ArrayList<>());
        } else {
            // 查询符合条件的一页记录
            return new Page<>(total, pageList(paramMap));
        }
    }

    /**
     * 查询符合条件的总记录数
     *
     * @param paramMap 分页查询参数（key需要包含”pageNumber“和”pageSize“，否则默认查询第一页的20条记录）
     * @return 总记录数
     */
    abstract long pageCount(Map<String, Object> paramMap);

    /**
     * 查询符合条件的所有记录
     *
     * @param paramMap 分页查询参数（key需要包含”pageNumber“和”pageSize“，否则默认查询第一页的20条记录）
     * @return 分页结果集合
     */
    abstract List<E> pageList(Map<String, Object> paramMap);
}

```

下面是一些为demo准备的类

```java
@Data
public class User {
}
```

```java
public class UserDAO  {
    public long pageCount(Map<String, Object> paramMap) {
        // select count(*) from user where ...
        return 0;
    }

    public List<User> pageList(Map<String, Object> paramMap) {
        // select * from user where ... limit pageBegin, pageSize
        return new ArrayList<>();
    }
}
```

```java
public class UserService extends AbstractPageTemplate<User> {
    private UserDAO userDAO = new UserDAO();

    @Override
    public long pageCount(Map<String, Object> paramMap) {
        return userDAO.pageCount(paramMap);
    }

    @Override
    public List<User> pageList(Map<String, Object> paramMap) {
        return userDAO.pageList(paramMap);
    }
}
```

下面是demo

```java
UserService userService = new UserService();

Page<User> userPage = userService.page(ImmutableMap.of(
                PageUtil.PAGE_NUMBER, 1,
                PageUtil.PAGE_SIZE, 20
                // 其他参数...
        ));
```

分析下上面这种传统的模板方法模式，我们把样板式的代码写到`AbstractPageTemplate#page()`方法中，当我们要写新的分页查询时，只要继承`AbstractPageTemplate`类，然后实现其中的两个方法，就可以很方便的获取到分页结果。

如果没有想到模板方法模式，项目中肯定会有大量的类似于`AbstractPageTemplate#page()`方法中的代码，而且每个人写的可能会不一样，如果后面要修改默认的每页大小，要找到所有的这些分页代码不是很容易，难免会有遗漏。模板方法模式为后期的重构、扩展提供了便利。

这种传统的方式写起来有点麻烦，一个模块有几个分页查询就要写几个Service类，去继承`AbstractPageTemplate`类。一个模块有多个service好像有点不合理，如果能把`AbstractPageTemplate#pageCount`方法和`AbstractPageTemplate#pageList`方法作为方法参数传入`AbstractPageTemplate#page()`方法中，那么就方便多了，不用再去写那么多Service类了。还好Java8之后有了lambda表达式，我们就可以把方法作为方法的参数。

## 三、Java8的方式

下面是核心类

```java
/**
 * 分页查询工具类
 */
public class PageUtil {
    /**
     * "pageNumber"
     */
    public static final String PAGE_NUMBER = "pageNumber";
    /**
     * "pageSize"
     */
    public static final String PAGE_SIZE = "pageSize";

    /**
     * "pageBegin"
     */
    private static final String PAGE_BEGIN = "pageBegin";

    private PageUtil() {
    }

    /**
     * 获取分页结果对象
     * <p>
     * 示例:
     *
     * <pre>
     * {@code
     *      Page<FooDTO> fooDTOPage = PageUtil.page(mapper::selectPageCount1, mapper::selectPageEntities1, paramMap)
     * }
     * </pre>
     *
     * @param pageCountFunction     查询分页总数的方法（参数类型：{@code Map<String, Object>}；返回值类型：{@code int}）
     * @param pageQueryListFunction 查询分页记录的方法（参数类型：{@code Map<String, Object>};；返回值类型：{@code List<E>}）
     * @param paramMap              分页查询参数（key需要包含”pageNumber“和”pageSize“，否则默认查询第一页的20条记录）
     * @return 分页结果对象集合
     */
    public static <E> Page<E> page(ToLongFunction<Map<String, Object>> pageCountFunction,
                                   Function<Map<String, Object>, List<E>> pageQueryListFunction,
                                   Map<String, Object> paramMap) {
        Objects.requireNonNull(pageCountFunction);
        Objects.requireNonNull(pageQueryListFunction);
        Objects.requireNonNull(paramMap);
        Integer pageNumber = (Integer) paramMap.get(PAGE_NUMBER);
        pageNumber = pageNumber == null || pageNumber <= 0 ? 1 : pageNumber;
        Integer pageSize = (Integer) paramMap.computeIfAbsent(PAGE_SIZE, k -> 20);
        paramMap.put(PAGE_BEGIN, (pageNumber - 1) * pageSize);
        long total = pageCountFunction.applyAsLong(paramMap);
        if (total <= 0) {
            return new Page<>(0L, new ArrayList<>());
        } else {
            return new Page<>(total, pageQueryListFunction.apply(paramMap));
        }
    }
}
```

下面是demo

```java
 Page<User> userPage = PageUtil.page(userService::pageCount, userService::pageList, ImmutableMap.of(
                PageUtil.PAGE_NUMBER, 1,
                PageUtil.PAGE_SIZE, 20
                // 其他参数...
        ));
```

分析下Java8以后的写法，对于一个模块有多个分页查询的情况，我们只要在Service中定义多个“查询符合条件的总记录数”和“查询符合条件的所有记录”的方法，然后在`PageUtil#page`方法中传入方法引用。

概括来说，Java8以后我们就可以**把方法作为方法的参数传入方法中**，从而省去了写很多类去继承抽象的模板类的麻烦。

## 四、模板方法模式

模板方法模式是一个在我们平时写代码中经常用到的模式，可以帮我们少写很多重复的代码，从而提高开发效率。在一些框架中也会经常看到，比如Spring的JdbcTemplate。GOF给模板方法模式下过以下定义：

> 定义一个操作中的算法骨架，而将一些步骤迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

如果你不明白这个定义也无所谓，只要你看懂了上面那个Java8的例子就可以了。在我看来，模板方法模式就是**把方法传入方法中**，有了lambda，就是把**把方法作为方法参数传入方法中**。