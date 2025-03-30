---
title: 单例模式（Singleton Pattern）
date: 2023-11-22
category:
  - 设计模式(Pattern)
tag:
  - java
sticky: false
---

# Design Patterns Singleton



# 一、创建型模式：

## 1、单例模式

### 静态常量饿汉式

优点：写起来简单不费脑子、线程安全

缺点：类装载就完成了实例化，可能浪费内存（不太重要，因为要用到的话早晚装载对象）

```java
public class Test {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();
        System.out.println(singleton1.hashCode() == singleton2.hashCode());
    }
}
class Singleton {
    // private构造方法保证外部无法实例化:
    private Singleton() {}

    // 静态字段引用唯一实例:
    private static final Singleton INSTANCE = new Singleton();

    // 通过静态方法返回实例:
    public static Singleton getInstance() {
        return INSTANCE;
    }

}
```

### 静态内部类

优点：线程安全、延时装载对象

```java
public class Test {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();
        System.out.println(singleton1.hashCode() == singleton2.hashCode());
    }
}
class Singleton {
    // private构造方法保证外部无法实例化:
    private Singleton() {}

    // 通过内部类的静态字段引用唯一实例:
    private static class SingletonInstance{
        private static final Singleton INSTANCE = new Singleton();
    }

    // 通过静态方法返回实例:
    public static Singleton getInstance() {
        return SingletonInstance.INSTANCE;
    }

}
```

### 枚举方式

优点：线程安全、实现简单

缺点：新同事可能阅读代码理解困难

```java
public class Test {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.INSTANCE;
        Singleton singleton2 = Singleton.INSTANCE;
        System.out.println(singleton1.hashCode() == singleton2.hashCode());
    }
}
enum Singleton {
    INSTANCE;
    private String name = "world";

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```