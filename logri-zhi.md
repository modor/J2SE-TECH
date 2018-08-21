记录日志是程序开发非常重要不可或缺的一部分，JDK也提供了记录日志的log模块，是在JDK 1.4引入的，比log4j出现的要晚。

### logging组成

> **七种日志级别**

JDK内置 Logger提供了如下七种Logger级别，从高到低依次是：SEVERE-&gt;WARNING-&gt;INFO-&gt;CONFIG-&gt;FINE-&gt;FINER-&gt;FINESET。 

> **两种Formatter**

* SimpleFormatter：简单文本的格式记录日志

* XMLFormatter：xml格式记录日志

> **五种handler**

* ConsoleHandler：将日志输出的控制台界面

* SocketHandler：将日志输出到绑定的主机端口

* FileHandler：日志输出到指定文件，可以按照文件大小飞分割日志

* MemoryHanlder：输出日志到内存缓冲区中

* StreamHandler：输出日志到输入输出流对象中，是ConsoleHandler、SocketHandler和FileHandler的基类

> 配置文件

* 默认配置文件：jre\lib\logging.properties
* 设置配置文件

```
System.setProperty("java.util.logging.config.class", "Your class");
```

或

```
System.setProperty("java.util.logging.config.file", "Your properties file");
```

### 日志轮询案例

```
package io.modor.test;

import java.util.logging.FileHandler;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.util.logging.SimpleFormatter;

/**
 * Hello world!
 * 
 */
public class App {
    public static void main(String[] args) throws Exception, Exception {
        Logger logger = Logger.getLogger("io.modor.test.App");
        FileHandler fh = new FileHandler("d:\\%.log", 10*1024*1024, 10, false);     //10M为单位，分割十个文件
        fh.setFormatter(new SimpleFormatter());
        logger.addHandler(fh);
        logger.setLevel(Level.ALL);
        for (;;) {
            logger.info("test");
        }
    }
}
```



