### 实现原理

RMI（Remote Method Invocation）为远程方法调用，是允许运行在一个Java虚拟机的对象调用运行在另一个Java虚拟机上的对象的方法。其原理是：方法调用从客户对象通过占位程序（Stub\)、远程引用层\(Remote Reference Layer\)和传输层（Transport Layer）向下，传递给主机，然后再次经传 输层，向上穿过远程调用层和骨干网（Skeleton），到达服务器对象。 占位程序扮演着远程服务器对象的代理的角色，使该对象可被客户激活。

在java 1.2之前，与Stub对象直接对话的是Skeleton对象，在Stub对象将调用传递给Skeleton的过程中，其实这个过程是通过JRMP协议实现转化的，通过这个协议将调用从一个虚拟机转到另一个虚拟机。在Java 1.2之后，与Stub对象直接对话的是Server程序，不再是Skeleton对象了。

### 服务端定义

* **定义Service接口**

定义的接口需要继承Remote类，其中的方法需要抛出异常：

```
package io.modor.service;

import java.rmi.Remote;
import java.rmi.RemoteException;

public interface Service extends Remote {
    void add(String name) throws RemoteException;

    void del(String name) throws RemoteException;

    void modify(String oldName, String newName) throws RemoteException;
}

```

* **定义Service实现类**

实现类需要继承UnicastRemoteObject类：

```
package io.modor.service.impl;

import io.modor.service.Service;
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;

public class ServiceImpl extends UnicastRemoteObject implements Service {

    private static final long serialVersionUID = 7139032615433690011L;

    public ServiceImpl() throws RemoteException {
        super();
        // TODO Auto-generated constructor stub
    }

    @Override
    public void add(String name) throws RemoteException {
        // TODO Auto-generated method stub
        System.out.println("添加"+name+"成功");
    }

    @Override
    public void del(String name) throws RemoteException {
        // TODO Auto-generated method stub
        System.out.println("删除"+name+"成功");
    }

    @Override
    public void modify(String oldName, String newName) throws RemoteException {
        // TODO Auto-generated method stub
        System.out.println("修改"+name+"成功");
    }

}
```

* **Service注册**

```
public class RmiServerTest {  
    public static void main(String[] args) throws Exception {        
        Service  s = new ServiceImpl();
        Registry registry=LocateRegistry.createRegistry(8888);//开启远程调用服务
        Naming.rebind("rmi://127.0.0.1:8888/Service",s);//将对象注册到远程调用服务中
    }
}  
```

### 客户端调用

客户端调用时候输出到控制台会输出到server的控制台：

```
public class RmiClientTest {  
    public static void main(String[] args) throws Exception {        
        Service s = (Service) Naming.lookup("rmi://127.0.0.1:8888/Service");
        s.del("wang");
    }
}  
```



