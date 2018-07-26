## 反射

### 什么是反射

通过java反射机制，可以在程序中访问已经装载到JVM中的java对象的描述，实现访问、检测和修改描述java对象本身信息的功能，java.lang.reflect包中提供了对该功能（Class类实现）的支持。

### 为什么可以反射

因为Java的.class文件的字节码是可以逆向解析的，我们可以通过加载进jvm的字节码信息获取到完整的类信息，而C却不可以，因为C是二进制文件，我们无法根据二进制推导出C语句。正是这种机制让java的对象创建可以分为三个过程：加载类、创建对象、校验对象。new关键字正是合并了这三个过程。也正是这种机制让Java动态编译成为可能，我们可以在JVM中解析java字节码，然后再编译执行，然后再在JVM中校验生成的对象。

### 如何获取使用反射

我们可以通过类的Class对象被动获取其反射信息，也可以通过类加载器来主动获取Class对象，当然，前者获取本质是通过类加载器实现：

* ```
  Class clazz  = Class.forName("CLASSPATH")
  ```
* ```
  Class clazz  = Object.class
  ```
* ```
  Object o = new Object();
  Class clazz = o.getClass();
  ```
* ```
  使用JDK默认类加载器AppClassLoader：
  ClassLoader loader = this.class.getClassLoader();
  Class clazz = loader.loadClass("CLASSPATH");
  或自定义加载器继承ClassLoader：
  ClassLoader loader = new MyClassLoader();
  Class clazz = loader.loadClass("CLASSPATH");
  ```

### 反射的应用和优点

主要可以提高框架的灵活性，比如spring，我们可以把**加载类**和**创建对象**交由框架来做，而**类的创建**和**对象的校验**交给用户来做，这样框架就可以管理各种bean而不至于拘泥于某种类和接口。

### Class对象的使用

* 获取泛型反射

```
Type type = this.getClass().getGenericSuperclass();
ParameterizedType pType = (ParameterizedType) type;
Type[] types = pType.getActualTypeArguments();
for(int i; i<types.length; i++){
    Class c = (Class)types[i];
    String name = c.getName();
}
```

* 获取注解反射

```
获取指定类型的注解:
MyAnno myAnno = O.class.getAnnotation(MyAnno.class);
myAnno.name();
myAnno.age();
获取所有：
Annotation[] ans = O.class.getAnnotations();
```

* 获取属性

![](/assets/attr.jpg)

* 获取方法

![](/assets/getmethod.jpg)

* 获取构造方法

![](/assets/getCons.jpg)

* 获取其他信息

![](/assets/getother.jpg)

## 类加载机制



## 动态代理



