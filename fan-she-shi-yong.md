## 反射

#### 什么是反射

通过java反射机制，可以在程序中访问已经装载到JVM中的java对象的描述，实现访问、检测和修改描述java对象本身信息的功能，java.lang.reflect包中提供了对该功能（Class类实现）的支持。

#### 为什么可以反射

因为Java的.class文件的字节码是可以逆向解析的，我们可以通过加载进jvm的字节码信息获取到完整的类信息，而C却不可以，因为C是二进制文件，我们无法根据二进制推导出C语句。正是这种机制让java的对象创建可以分为三个过程：加载类、创建对象、校验对象。new关键字正是合并了这三个过程。也正是这种机制让Java动态编译成为可能，我们可以在JVM中解析java字节码，然后再编译执行，然后再在JVM中校验生成的对象。

#### 如何获取使用反射

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

#### 反射的应用和优点

主要可以提高框架的灵活性，比如spring，我们可以把**加载类**和**创建对象**交由框架来做，而**类的创建**和**对象的校验**交给用户来做，这样框架就可以管理各种bean而不至于拘泥于某种类和接口。

#### Class对象的使用

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

反射是解析和获取已经加载到内存的类信息，而将类加载到内存就需要类加载器，如Class.forName\(\)是默认先调用了类加载器将类加载到内存，然后再获取其Class对象，如下forName0\(className, true, ClassLoader.getClassLoader\(Reflection.getCallerClass\(\)\)\)。new对象则是默认调用线程中setContextClassLoader方法指定的加载器。线程对象可以通过Thread.currentThread\(\).setContextClassLoader\(classLoader\)设置。

#### 自定义类加载器

自定义类加载器需要继承java.lang.ClassLoader类，重写findClass\(\)方法（ClassLoader默认该方法为空），如果需要破坏双亲委派机制，则需要重写loadClass\(\)方法，但不推荐。**虽然自定义加载器和AppClassLoader以及ExtClassLoader都继承自ClassLoader，但自定义加载器的父加载器是AppClassLoader，这也是在不破坏loadClass\(\)方法的情况下，自定义类加载器遵循双亲委派的原因。**

```
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
public class MyClassLoader extends ClassLoader {
    public static final String drive = "d:/";
    public static final String fileType = ".class";

    public Class<?> findClass(String name) {
        byte[] data = loadClassData(name);
        return defineClass(name, data, 0, data.length);// 将一个 byte 数组转换为 Class// 类的实例
    }

    public byte[] loadClassData(String name) {
        FileInputStream fis = null;
        byte[] data = null;
        try {
            fis = new FileInputStream(new File(drive + name + fileType));
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            int ch = 0;
            while ((ch = fis.read()) != -1) {
                baos.write(ch);
            }
            data = baos.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return data;
    }
}
```

#### 自定义类加载器的使用

详细见反射一节。

#### JDK提供的类加载器

> jdk提供的各个加载器类关系

继承关系图：

![](/assets/classloader.png)

除了Bootstrap ClassLoader是由C/C++编写的，虚拟机自身提供的，其他加载器都是ClassLoader的子类。自定义类加载器、AppClassLoader、ExtClassLoader以及Bootstrap ClassLoader是单向关联关系：自定义加载器的程序员变量parent是AppClassLoader，AppClassLoader的parent是ExtClassLoader，ExtClassLoader的parent是Bootstrap ClassLoader，而Bootstrap ClassLoader的parent为null。

> 关于URLClassLoader

RLClassLoader可以使用在网络加载上：

```
public class Main {
    public static void main(String[] args) {
        String jarURL = "http://example.modor.io/rmi/Test.jar";
        try {
            URLClassLoader classLoader = new URLClassLoader(new URL[]{new URL(jarURL)});
            Class cls = classLoader.loadClass("io.modor.test.Test");
            Method method = cls.getDeclaredMethod("test");
            method.invoke(cls.newInstance());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 动态代理

动态代理是在程序运行时，运用反射机制动态创建而成的代理访问类。**JDK只提供了针对接口生成代理，故不能只针对某一个类生成代理。如果需要为某一个类实现一个代理的话，考虑使用CGLIB等第三方字节码（一种字节码增强技术）。**

### 动态代理应用一：数据源连接池屏蔽close\(\)方法

* 自定义连接池

```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.sql.Connection;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;

public class MyDataSource {
    static int size = 10;
    private static List<Connection> pool = Collections.synchronizedList(new LinkedList<Connection>());
    static {
        for (int i = 0; i < size; i++) {
            Connection conn = JDBCUtil.getConnection();
            pool.add(conn);
        }
    }

    public static Connection getConnection() {
        final Connection conn = pool.remove(0);
        Connection proxyConn = (Connection) Proxy.newProxyInstance(conn.getClass().getClassLoader(), conn.getClass()
                .getInterfaces(), new InvocationHandler() {

            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // TODO Auto-generated method stub
                Object retValue = null;
                if ("close".equals(method.getName())) {
                    pool.add(conn);
                } else {
                    retValue = method.invoke(conn, args);
                }
                return retValue;
            }
        });
        return proxyConn;
    }
    
    public static int getSize() {
        return pool.size();
    }
}
```

* 连接池使用

```
Connection conn = MyDataSource.getConnection();
......
conn.close();
```

### 动态代理应用二：给业务层增加事务控制

```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class BeanFactory {
    public static CustomerService getCustomerService() {
        final CustomerService customerService = new CustomerServiceImpl();
        CustomerService cs = (CustomerService) Proxy.newProxyInstance(cs.getClass().getClassLoader(), cs.getClass()
                .getInterfaces(), new InvocationHandler() {

            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // TODO Auto-generated method stub
                try {
                    TransactionManager.beginTransaction();
                    Object retValue = method.invoke(cs, args);
                    TransactionManager.commit();
                    return retValue;
                } catch (Exception e) {
                    TransactionManager.rollback();
                    throw new RuntimeException(e);
                } finally {

                }
            }
        });
        return cs;
    }
}
```



