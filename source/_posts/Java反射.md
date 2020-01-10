---
title: Java反射
date: 2019-12-17 12:26:06
tags:
- Java
categories:
- Java基础
---
#### Java反射概念
反射是Java的特征之一，它允许运行中的Java获取自身的信息，并且可以操作类或对象内部的属性。
Java的反射机制是在编译并不确定是哪个类被加载了，而是在程序运行的时候才加载、探知、自审。使用在编译期并不知道的类。这样的特点就是反射。
#### 反射的作用
假如你从磁盘文件或者网络连接中获取了一串字节，并且你被告知这些字节代表了一个类。既然这个类在编译器编译之后很长一段时间才出现，那么可以通过反射来使用这个类
#### 使用反射创建对象
创建一个Person类
```
public class Person {
    private String name;
    private int age;
    
}
```
通过class对象直接使用newInstance()方法创建实例
```
public static void main(String[] args) {
        try {
            String classpath="test.Person";
            Class pclass=Class.forName(classpath);
            Object person=pclass.newInstance();
            System.out.println(person);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    ```
结果
```
test.Person@4554617c
```
或者通过Constructor创建实例对象

```
public static void main(String[] args) {
        try {
            String classpath="test.Person";
            Class pclass=Class.forName(classpath);
            Constructor c=pclass.getConstructor();
            Object person=c.newInstance();
            System.out.println(person);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
```
##### 使用Class.newInstance()和Constructor.newInstance()区别
Class.newInstance()只能反射无参的构造器；
Constructor.newInstance()可以反任何构造器；

Class.newInstance()需要构造器可见(visible)；
Constructor.newInstance()可以反私有构造器；
#### 使用反射访问属性
```
    public static void main(String[] args) {
        try {
            String classpath="test.Person";
            Class pclass=Class.forName(classpath);
            Field f=pclass.getDeclaredField("name");
            System.out.println(f);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    ```
结果
```
private java.lang.String test.Person.name
```
#### 使用反射调用方法
在person类中添加一个方法
```
public class Person {
    private String name;
    private int age;
    public void hello(){
        System.out.println("hello world");
    }
}
```
```
public static void main(String[] args) {
        try {
            String classpath="test.Person";
            Class pclass=Class.forName(classpath);
            Method m=pclass.getDeclaredMethod("hello");
            m.invoke(pclass.newInstance());
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    ```
运行结果
```
hello world
```