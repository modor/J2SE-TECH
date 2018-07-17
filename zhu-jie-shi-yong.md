注解（Annotation），也叫元数据。它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。注解并不会直接影响到程序的语义，只是作为注解（标识）存在，我们可以通过反射机制编程实现对这些元数据（用来描述数据的数据）的访问。

### 自定义注解

自定义注解由`@interface`关键字和四个元注解`@Target`、`@Retention`、`@Inherited`、`@Documented`构成，元注解是交由编译器和JVM解析的注解。

```
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Description {
    String desc();
    String author();
    int age() default 18;
}
```

* **@Target（作用目标）**   ElementType枚举当中定义了作用目标：TYPE，接口、类、枚举、注解；FIELD，字段、枚举的常量；METHOD，方法；PARAMETER，方法参数；CONSTRUCTOR，构造函数；LOCAL\_VARIABLE，局部变量；ANNOTATION\_TYPE；注解；PACKAGE，包。
* **@Retention（保留策略）**   RetentionPolicy枚举当中定义了保留策略：SOURCE，注解仅存在于源码中；CLASS， 默认的保留策略，在class字节码文件中存在，但运行时无法获得；RUNTIME，在class字节码文件中存在，在运行时可以通过反射获取到。
* **@Inherited（可被子类继承）**
* **@Document（将包含在文档当中）**

### 注解的使用

* 注解需要注解的目标。

```
@Description(desc="i am Color",author="boy",age=18)
public String Color() {
    return "red";
}
```

* 通过反射获取注解值并注入到相应位置。

```
略，详见反射一章。
```

### JDK实现的注解

* **@Override**   Target为ElementType.METHOD；Retention为RetentionPolicy.SOURCE，由编译解析该注解。
* **@Deprecated**   Target为CONSTRUCTOR, FIELD, LOCAL\_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE；Retention为RetentionPolicy.RUNTIME，由JVM解析该注解
* **@SuppressWarnings**   Target为TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL\_VARIABLE；Retention为RetentionPolicy.SOURCE，由编译器解析该注解。



