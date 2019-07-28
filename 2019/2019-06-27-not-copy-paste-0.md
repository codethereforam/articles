# 做一个不复制粘贴的程序员[0]: 概述

## 前言
Perl语言之父拉里·沃尔曾说过程序员有三大美德：懒惰、急躁、傲慢，很多程序员在平时工作中常常做很多重复的事情，写很多重复的代码，如果有懒惰的思想，就可以避免很多重复，从而提高开发效率，增加编程乐趣，我们需要的是一种智慧的懒惰。

举个生活中重复的例子，我们会关注一些大佬的博客，为了看他们有没有更新博文，我们经常会挨个点进他们的博客主页。如果会用RSS的话，只要点进RSS客户端首页，就能看到哪些博主有了新的文章。生活中重复的例子还有很多，本系列文章只谈谈编程中的重复。

说到代码层面的重复，不得不提起一个著名的软件设计原则：[DRY(Don’t Repeat Yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，大概说的是应该避免重复的代码。不过也有人写过《DRY原则的危害》的文章，主要说不要过度抽象，不要太教条。我觉得各种技术、思想、原则，如果正确地使用都会有好处的，反之则会带来一些危害，如果不会用那不如不用。

重复的危害大家应该都有体会，比如浪费时间、不利于重构、容易出错等。选择复制粘贴其实也是一种懒惰，懒于思考，这会让我们工作十年，每年写的是一样的代码，一直没有进步。顺带提一下复制粘贴的技巧，有种叫历史粘贴板的东西，IDEA和Win10都有这个功能。

## Talk is cheap. Show me the code. 

下面举一个避免复制粘贴的代码例子，这其实属于后面第四篇文章所谈的代码生成。Java项目中经常会写很多枚举类，一般这些枚举有两个字段，一是枚举值，对应数据库中存在的字段，二是枚举类型描述，用于展现，而且常常需要一个根据枚举值获取枚举实例的方法。我们可以使用IDE的模板文件功能，只要确定枚举类名，就可以自动生成所有的模板代码。IDEA配置在"Settings -> Editor -> File and Code Templates -> Files -> Enum"下粘贴以下代码（省略了注释），完整代码见我的Gist: [idea-enum-file-template.java ](https://gist.github.com/codethereforam/b722eccaba1e41acbe48c1d3070525e5). 当需要新建一个枚举类型时，只要在"Create New Class"对话框中输入类名，"Kind"选择"Enum"即可。

```java
    #if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
    #parse("File Header.java")
    public enum ${NAME} {
        ;

        private final int value;
        private final String desc;

        ${NAME}(int value, String desc) {
            this.value = value;
            this.desc = desc;
        }

        public int getValue() {
            return this.value;
        }

        public String getDesc() {
            return desc;
        }

        private static final Map<Integer, ${NAME}> MAP = Arrays.stream(${NAME}.values())
                .collect(Collectors.toMap(${NAME}::getValue, e -> e));

        public static ${NAME} getByValue(Integer value) {
            return MAP.get(value);
        }
    }
```

## 本系列的目录
给自己挖个坑，后面博文从以下方面谈谈如何做一个不复制粘贴的程序员：
1. 使用模板方法模式
2. 使用AOP
3. 代码生成