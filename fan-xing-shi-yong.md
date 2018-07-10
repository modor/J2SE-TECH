Java泛型（generics）是JDK 5中引入的一个新特性, 泛型提供了**编译时类型安全检测**机制，该机制允许程序员在编译时检测到非法的类型。相当于替代Object类，当无法确定参数对象类型，但又避免使用Object无限扩大参数类型范围，故产生泛型，在编译时就做安全检查。

### 自定义泛型

* [ ] 泛型类

```
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```

* [ ] 泛型接口

```
public interface Generator<T> {
    public T next();
}
```

* [ ] 泛型方法

* 静态

```
public class Generic{ 
    public static <T> void print(T t){
        System.out.println(t);
    }
}
```

* 普通

```
public class Generic{ 
    public <T> void print(T t){
        System.out.println(t);
    }
}
```

### 泛型使用

* [ ] 普通使用

```
Generator<String>
```

* [ ] 继承使用

* 固定类型

```
public class Son extends Generic<String>{}
```

* 仍然是泛型

```
public class Son extends Generic<T>{}
```

* [ ] 泛型通配符

* &lt;?&gt;

```
Generic<?>
```

* &lt;? extends T&gt;表示该通配符所代表的类型是T类型的子类。

```
Generic<? extends Father>
```

* &lt;? super T&gt;表示该通配符所代表的类型是T类型的父类。

```
Generic<? super Son>
```







