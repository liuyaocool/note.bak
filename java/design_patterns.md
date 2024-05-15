
**todo 缓慢输出中。。**


# 设计原则

## 1. 单一职责原则：粒度小，功能单一的类

**官方定义**

一个类或者模块只负责完成一个职责（或者功能）。

**通俗解释**

在类的设计中 我们不要设计大而全的类，而是要设计粒度小、功能单一的类.

例如

```java
public class User {
    private String userId;
    private String username;
    // 如果地区属性 没有其他地方使用，此类就符合单一职责
    private String province;
    private String city;
    private String district;
}
```

## 2. 开闭原则(设计模式核心目标)：对扩展开放，对修改关闭

**官方定义**

在面向对象编程领城中，开闭原则规定软件中的对象、类、模块和函数对扩展应该是开放的：得对手修改是封闭的。这意味着应该用抽象定义结构，用具体实现扩展细节，以此确保软件系统开发和维护过程的可靠性。

**通俗解释**

定义： 对扩展开放，对修改关闭
优点：
1. 新老逻辑解耦，需求发生改变不会影响老业务的逻辑
2. 改动成本最小，只需要追加新逻辑，不需要改的老逻辑
3. 提供代码的稳定性和可扩展性

例如

```java

```

## 3. 里氏替换原则：看接口就知道其实现类要做什么

**官方定义** 

如果S是T的子类型，对于S类型的任意对象，如果将他们看作是T类型的对象，则对象的行为也理应与期望的行为一致。

**通俗解释**

- **什么是替换：** 前提是多态
  ```java
  // 此方法的参数可以替换为所有实现Runnable接口的实现类, 则此方法的扩展性就提高了
  public void start(Runnable r);
  ```
- **什么是期望的行为一致的替换：** 在不了解派生类的情况下，仅通过接口或基类的方法，即可清楚的知道方法的行为，而不管哪种派生类的实现、都与接口或基类方法的期望行为一致。
  ```java
  
  ```

## 4. 接口隔离原则：接口为上层业务拆分

**官方定义**

客户端不应该被迫依赖于它不使用的方法。该原则还有另外一个定义：一个类对另一个类的依赖应该建立在最小的接口上

**通俗解释**

要为各个类建立它们需要的专用接口，而不要试图去建立一个很庞大的接口供所有依赖它的类去调用。

```java
// 多接口 同一个实现类， 这样可以将专门的接口提供给特定业务层
public UserServiceImpl implements UserQueryService, UserModifyService {

}
```

##  5. 依赖倒置原则：面向抽象编程

**官方定义**

依赖倒置原则 (Dependence Inversion Principle，DIP)是指在设计代码架构时，高层模块不应该依赖于底层模块，二者都应该依赖于抽象。抽象不应该依赖于细节，细节应该依赖于抽象。

**通俗解释**

由于在软件设计中，细节具有多变性，而抽象层则相对稳定，因此以抽象为基础搭建起来的架构要比以细节为基础搭建起来的架构要稳定得多。
![在这里插入图片描述](img/design_patterns_01.png)

**PK：控制反转 依赖注入**

- 依赖倒置：是一种通用设计原则,主要用来指导框架层面的设计
- 控制反转：它是一种框架设计常用的模式,但不是具体方法
- 依赖注入：依赖注入是实现控制反转的手段,它是一种具体的编码技巧.


## 6. 迪米特法则：最少知识，强调多使用中间人

**官方定义**

迪米特法则又叫最少知识原则（LKP： Least Knowledge Principle），指的是一个类/模块对其他的类/模块有越少的了解越好。

**通俗解释**

不该有直接依赖关系的类之间，不要有依赖；有依赖关系的类之间，尽量只依赖必要的接口。

## 7. ? 组合、聚合原则：少继承，多注入

新对象中使用一些已有的对象，使之成为新对象的一部分。就是说要尽量使用合成和聚合，而不是通过继承关系到达复用的目的。

# 设计模式
## 创建型-1. 单例

### 1. 饿汉

### 2. 懒汉
```java
public class Singleton implements Serializable{

    private static volatile Singleton instance;

    private Singleton() { }

    public static Singleton getInstance() {
        if (null == instance) {
            synchronized (Singleton.class) {
                if (null == instance) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    
    // 此方法可解决反序列化对单例的破坏，具体原因可看源码 ois.readObject();
    private Object readResolve() {
        return instance;
    }
    
    public static void main(String[] args) throws Exception{
        String pathname = "Singleton.obj";
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(pathname));
        oos.writeObject(Singleton.getInstance());
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File(pathname)));
        Singleton o = (Singleton) ois.readObject();
        System.out.println(o == Singleton.getInstance());
    }
}

```

### 3. 静态内部类

```java
public class Singleton {
    private Singleton() {
    	// 可避免反射对单例的破坏
        if (null != SingletonHandler.instance) {
        	throw new RuntimeException("单例类不允许被多次实例化");
        }
    }
    public static Singleton getInstance() {
        return SingletonHandler.instance;
    }
    private static class SingletonHandler {
        private static Singleton instance = new Singleton();
    }
}
```

### 4. 枚举单例

```java
public enum Singleton {

    INSTANCE;

    public static Singleton getInstance(){
        return Singleton.INSTANCE;
    }
	// 反射不能破坏枚举类型单例
    public static void main(String[] args) throws Exception {
        Class<Singleton> clz = Singleton.class;
        Constructor<Singleton> con = clz.getDeclaredConstructor(String.class, int.class);
        // 这一行会报错： “IllegalArgumentException: Cannot reflectively create enum objects”
        // 即 枚举方式可以避免反射对单例的破坏
        Singleton ni = con.newInstance("INSTANCE", 0);
        System.out.println(ni);
    }
    // 反序列化也不能破坏枚举类型单例
    // 在序列化的时候仅仅是将效类对豪的name 属性输出到了结果中，反序列化的时候，就会通过Enum的 valueof方法 来根据名字去查找对应枚举.
    public static void main(String[] args) throws Exception {
        String pathname = "Singleton.obj";
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(pathname));
        oos.writeObject(Singleton.getInstance());
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File(pathname)));
        System.out.println(ois.readObject() == Singleton.getInstance());// true
    }
}
```

## 创建型-2. 工厂模式

### 1. 简单工厂模式：静态方法通过参数返回不同实例

简单工厂不是一种设计模式，反而比较像是一种编程习惯。简单工厂模式又叫做静态工厂方法模式,它是**通过使用静态方法接收不同的参数来返回不同的实例对象**.

**实现方式：** 定义一个工厂类，根据传入的参数不同返回不同的实例，被创建的实例具有共同的父类或接口。

**适用场景：**

1. 需要创建的对象较少。
2. 客户端不关心对象的创建过程。

**结构**

- 抽象产品：定义了产品的规范，描述了产品的主要特性和功能。
- 具体产品：实现或者继承抽象产品的子类


**优点**

1. 封装了创建对象的过程,可以通过参数直接获取对象.把对象的创建和业务逻辑分开,避免了可能会修改客户代码的问题.
2. 如果要实现新产品,直接修改工厂类,不需要再源代码中进行修改,降低了客户端修改代码的可能性,更加容易扩展.

**缺点** 

1. 在增加新产品的时候还是要修改工厂类的代码,违背了开闭原则.


### 2. 工厂方法模式：用工厂封装对象创建的过程

封装对象创建的过程，提升创建对象方法的可复用性。

**主要角色：**

- 抽象工厂：提供了创建产品的接口，调用者通过它访问具体工厂的工厂方法来创建产品。
- 具体工厂：主要是实现抽象工厂中的抽象方法，完成具体产品的创建。
- 抽象产品：定义了产品的规范，描述了产品的主要特性和功能。
- 具体产品：实现了抽象产品角色所定义的接口，由具体工厂来创建，它同具体工厂之间一一对应。

**UML图：**
![在这里插入图片描述](img/design_patterns_02.png)
