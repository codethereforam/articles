# 做一个不复制粘贴的程序员[1]: 使用模板方法模式（2）- 对象更新比较器实例

在进入正题之前，说一些废话，谈谈对于我的前一篇文章被移出博客园首页的想法。不谈我对于其他首页文章的看法，光从我自身找找原因。下面分析下可能的原因：
1. 篇幅太短：我觉得篇幅不能决定文章的质量，要说清楚一个问题，肯定字数越少越好
2. 代码过多，文字太少：Talk is cheap. Show me the code. 我觉得code比talk更有说服力，而且大多数程序员相对更喜欢看代码。我觉得我的代码说的比我文字说的好（相对而言，我没说我代码写的好 : ) ）
3. 质量不行：只有我觉得能给大家启发的我才会选择发布到首页。上篇文章的例子是我实际工作中遇到的问题，思考出来并且经过时间、实践检验的东西

不给自己找理由，我承认自己水平有限、能力不足，希望自己以后努力能达到要求，欢迎大家在评论区和我交流。

在前一篇文章中，我已经用分页查询的实例，说明了如何用模板方法模式去消除代码重复。那个例子相对比较简单，下面分享一个稍微难一点的例子，加深大家的理解。

## 一、场景描述

说一个我工作中遇到的场景，有一个消息队列（MQ）监听上游系统推送过来的消息，只对接数据库表中的几个字段，大概流程如下：
1. 先根据唯一键去查询数据库中是否存在，如果不存在则插入一条新记录
2. 如果存在，则对比上游推送的对象和数据库查出来的对象，比较对接的那些字段是否相同
3. 如果都相同，就什么都不操作。如果有不同的，就用那不同的字段去更新数据库中的记录

## 二、蹩脚的方法

假设和上游系统对接的是用户模块，用户表中有id、name、age、sex四个字段，id是唯一键，现在只要对接id、name、age四个字段。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private Integer age;
    private Integer sex;
}
```

```java
public class UserDAO {
    private User oneUser = new User(1, "u1", 18, 1);

    public User getById(Integer id) {
        if (id != 1) {
            return null;
        }
        return oneUser;
    }

    public void updateById(User user) {
        Integer id = user.getId();
        if (id == null || id != 1) {
            return;
        }
        if (user.getName() != null) {
            oneUser.setName(user.getName());
        }
        if (user.getAge() != null) {
            oneUser.setAge(user.getAge());
        }
        if (user.getSex() != null) {
            oneUser.setSex(user.getSex());
        }
    }
}
```

```java
    /**
     * 对比两个对象，获取用于更新的对象
     *
     * @param toUpdate 要更新的对象
     * @param original 原来的对象
     * @return 用于更新的对象。如果要比较的字段都一样则返回null
     */
    private User getUserUpdate(User toUpdate, User original) {
        User updateUser = new User();
        if (!original.getName().equals(toUpdate.getName())) {
            updateUser.setName(toUpdate.getName());
        }
        if (!original.getAge().equals(toUpdate.getAge())) {
            updateUser.setAge(toUpdate.getAge());
        }
        if (Stream.of(updateUser.getName(), updateUser.getAge())
                .allMatch(Objects::isNull)) {
            // 没有更新
            return null;
        }
        return updateUser;
    }
```

MQ监听的方法
```java
    // MQ监听方法接收到上游系统推送过来的一条记录，只对接id、name、age字段。id是唯一键（实际中一般不会是id）
    User toUpdate = new User(1, "uu1", 20, null);
    System.out.println("toUpdate user: " + toUpdate);
    // 根据唯一键键去数据库查询查询查询
    User original = userDAO.getById(toUpdate.getId());
    System.out.println("original user: " + original);
    // 如果查到则用上游系统推送的记录去更新这条记录，如果没查到则插入一条新记录
    if (original != null) {
        // 对比两个对象，获取用于更新的对象
        User updateUser = getUserUpdate(toUpdate, original);
        // 如果两对象要比较的字段都一样就不操作，否则更新不同的字段
        if (updateUser != null) {
            // 设置主键id
            updateUser.setId(toUpdate.getId());
            System.out.println("update user: " + updateUser);
            // 根据主键id去更新
            userDAO.updateById(updateUser);
            System.out.println("updated user: " + userDAO.getById(toUpdate.getId()));
        }
    } else {
        // 插入一条新记录
    }
```

运行结果：

```
toUpdate user: User(id=1, name=uu1, age=20, sex=null)
original user: User(id=1, name=u1, age=18, sex=1)
update user: User(id=1, name=uu1, age=20, sex=null)
updated user: User(id=1, name=uu1, age=20, sex=1)
```

分析下上述代码，核心是`getUserUpdate`方法，如果实际中对接的字段有很多，那么这个方法中的代码很容易出错。这个方法看起来重复的代码是：先比较两对象的同一字段是否相等，如果相等则设值。能不能把这块代码提炼出一个方法来，请思考一会，然后看下面的章节。

## 三、模板方法

```java
/**
 * 对象更新比较器
 *
 * @param <T> 待比较的对象类型
 */
public final class UpdateDiffer<T> {
    /**
     * 原来的对象
     */
    private final T original;
    /**
     * 要更新的对象
     */
    private final T toUpdate;
    /**
     * ”原来的对象“和“要更新的对象”比较出来用于更新的对象
     */
    private final T difference;
    /**
     * 需要比较的字段的get方法
     */
    private final List<Function<T, ?>> getMethodList;

    /**
     * Initializes a newly created UpdateDiffer object
     *
     * @param original     原来的对象
     * @param toUpdate     要更新的对象
     * @param tConstructor T类型对象构造方法
     */
    public UpdateDiffer(T original, T toUpdate, Supplier<T> tConstructor) {
        Objects.requireNonNull(original);
        Objects.requireNonNull(toUpdate);
        Objects.requireNonNull(tConstructor);
        this.original = original;
        this.toUpdate = toUpdate;
        this.difference = tConstructor.get();
        getMethodList = new ArrayList<>();
    }

    /**
     * 比较字段是否相同，如果不同，把要更新的对象字段值设置到difference对象里
     *
     * @param getMethod get方法
     * @param setMethod set方法
     * @param <R>       get方法的返回值类型/set方法参数类型
     * @return this
     */
    public <R> UpdateDiffer<T> diffing(Function<T, R> getMethod, BiConsumer<T, R> setMethod) {
        Objects.requireNonNull(getMethod);
        Objects.requireNonNull(setMethod);
        R toUpdateValue = getMethod.apply(toUpdate);
        R originalValue = getMethod.apply(original);
        Objects.requireNonNull(originalValue, "数据库中的字段不应该为null");
        if (!originalValue.equals(toUpdateValue)) {
            setMethod.accept(difference, toUpdateValue);
        }
        // 保存已经调用的get方法
        getMethodList.add(getMethod);
        return this;
    }

    /**
     * 获取”原来的对象“和“要更新的对象”比较出来的对象，用于去数据库更新（更新前还要再设置id等字段）。
     *
     * @return ”原来的对象“和“要更新的对象”比较出来用于更新的对象。如果“原来的对象”和“要更新的对象”中所有要比较的字段都相同，返回null
     */
    public T diff() {
        // 如果difference对象中所有要比较的字段都为null
        if (
                getMethodList.stream()
                        .map(getFunction -> getFunction.apply(difference))
                        .allMatch(Objects::isNull)
        ) {
            return null;
        }
        return this.difference;
    }
}
```

用以下的代码替换上一节中的`getUserUpdate`方法
```java
 User updateUser = new UpdateDiffer<>(original, toUpdate, User::new)
                    .diffing(User::getName, User::setName)
                    .diffing(User::getAge, User::setAge)
                    .diff();
```

运行结果和上一节的结果一样。

是不是觉得代码比之前的清楚了很多，而且不容易出错。代码没啥解释的，我是由JDK中的`Comparator`启发想出来的，不明白的可以先看看`Comparator`的用法。

## 四、结语

模板模式至此就介绍完了，大家的"CTRL"、"C"、"V"键会不会因此增加几年寿命 : )