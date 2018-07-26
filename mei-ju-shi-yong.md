### 实现原理

> 定义枚举：

```
public enum Color{
  RED, BLUE, GREEN, BLACK
}
```

> 反编译后：

```
public final class Color extends Enum
{
    public static Color[] values(){
        return (Color[])$VALUES.clone();
    }
    public static Color valueOf(String s){
        return (Color)Enum.valueOf(org/fast/clean/Color, s);
    }
    private Color(String s, int i){
        super(s, i);
    }
    public static final Color RED;
    public static final Color BLUE;
    public static final Color GREEN;
    public static final Color BLACK;
    private static final Color $VALUES[];
    static{
        RED = new Color("RED", 0);
        BLUE = new Color("BLUE", 1);
        GREEN = new Color("GREEN", 2);
        BLACK = new Color("BLACK", 3);
        $VALUES = (new Color[] {
            RED, BLUE, GREEN, BLACK
        });
    }
}
```

通过反编译代码我们可以看到，枚举实际上是一个继承了Enum抽象类，名称的为枚举名称（Color）类，枚举内的值是该类的静态实例化对象。因为是final类，所以枚举类不能被继承。因为已经继承了Enum类，所以枚举类也不能继承别的类，但可以实现其他接口。而且枚举类的构造方法也是私有的。因为枚举内变量都是静态的，所以非常的消耗内存，在某些场景下不推荐使用。

### 枚举类的定义

> 最简单方式：

```
public enum Color {  
  RED, GREEN, BLANK, YELLOW  
}
```

> 枚举类使用自定义方法和属性：

```
public enum Color {
    RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);
    // 成员变量
    private String name;
    private int index;
    // 构造方法
    private Color(String name, int index) {
        this.name = name;
        this.index = index;
    }

    // 普通方法
    public static String getName(int index) {
        for (Color c : Color.values()) {
            if (c.getIndex() == index) {
                return c.name;
            }
        }
        return null;
    }

    // get set 方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }
}
```

> 枚举类继承其他接口：

```
public enum Color implements Behaviour{}
```

### 枚举的使用

> 枚举默认方法使用：

```
//获取枚举名称,默认使用RED
Color.RED.name()
//获取枚举索引，默认使用RED顺序
Color.RED.ordinal()
```

> 自定义枚举类：

通过自定义方法获取，如上面的Color枚举类：

```
Color.RED.getName()
Color.RED.getIndex()
```

注：因为是继承了Enum类，自定义枚举仍然有name和ordinal，如RED的name为RED和ordinal为0，不过自定义枚举中舍弃不用。

> switch中枚举的使用

JDK1.6之前的switch语句只支持int,char,enum类型：

```
public class TrafficLight {
    Signal color = Signal.RED;
    public void change() {
        switch (color) {
        case RED:
            color = Signal.GREEN;
            break;
        case YELLOW:
            color = Signal.RED;
            break;
        case GREEN:
            color = Signal.YELLOW;
            break;
        }
    }
}
```

### 枚举集合

> EnumSet：

EnumSet中的所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet时显式或隐式地指定。

```
//初始化enumSet 
EnumSet enumSet = EnumSet.allOf(Color.class);
//添加枚举元素
enumSet.add(Color.RED);
```

> EnumMap：

EnumMap是一个与枚举类一起使用的Map实现，EnumMap中所有key都必须是单个枚举类的枚举值。创建EnumMap时必须显式或隐式指定它对应的枚举类。

```
EnumMap enumMap = new EnumMap(Color.class);
enumMap.put(Color.RED, "这是红色");
```



