---
title: Java重写、重载、多态的具体区别
date: 2019-09-24 15:09:28
tags:
- Java基础
categories:
- Java基础
---
在复习Java基础时，对重写、重载、多态的概念进行了一下梳理
<!--more-->
#### 重写 override
父类与子类有同样的方法名和参数，这叫方法重写。(Override)
```
	class Parent{
        void func(){
            System.out.println("parent func()");
        }
    }
    class child extends Parent{
        @Override
        void func() {
            System.out.println("child func()");
        }
    }
```
#### 重载 overload
一个方法名，参数不同，这叫方法重载。(Overload)
```
void func(String str);
void func(int val);
void func(char ch);
```
#### 多态
多态的概念稍微复杂   
官方一点说是：** 多态就是同一个接口，使用不同的实例而执行不同操作。 **   
通俗一点说就是：** 多态父类引用指向子类对象，调用方法时会调用子类的实现，而不是父类的实现。**

也就是
* 存在继承关系
* 子类实现父类方法重写
* 父类数据类型的引用指向子类对象

** 代码举例 **
```
	class Parent{
        void func(){
            System.out.println("parent func()");
        }
    }
    class child extends Parent{
        @Override
        void func() {
            System.out.println("child func()");
        }
    }
```
测试类
```
public class test {
    public static void main(String[] args) {
        Parent person=new child();
        person.func();
    }
}
```
结果
```
child func()
```
当父类类型指向子类对象时，调用父类的对象，实际会调用子类的实现的方法