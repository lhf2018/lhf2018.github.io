---
title: spring三种创建bean方式
date: 2019-10-15 20:10:13
tags:
- spring
categories:
- spring
---
#### 1.通过构造方法实例化bean   

创建bean1类
在applicationContext中添加
```
<bean class="test.Bean1" id="bean1"/>```
测试代码
```
ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
Bean1 bean1=context.getBean("bean1",Bean1.class);
System.out.println(bean1);
```
结果
```
test.Bean1@34b7ac2f```

#### 2.通过静态方法实例化bean

这种方式是基于工厂模式来创建bean2
创建class bean2
```
public class Bean2 {
}```
创建Bean2Factory
```
public class Bean2Factory {
    public static Bean2 getBean2(){
        return new Bean2();
    }
}
```
在applicationContext中添加
```
    <bean class="test.Bean1" id="bean1"/>
    <bean class="test.Bean2Factory" factory-method="getBean2" id="bean2"/>```
测试
```
ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
Bean2 bean2=context.getBean("bean2",Bean2.class);
System.out.println(bean2);
```
结果
```
test.Bean2@9353778
```
#### 3.通过实例方法实例化Bean  

创建Bean3类
创建bean3factory类
```
public class Bean3Factory {
    public Bean3 getBean(){
        return new Bean3();
    }
}
```
在applicationContext中添加
```
    <bean class="test.Bean3Factory" id="bean3Factory"/>
    <bean class="test.Bean3" factory-bean="bean3Factory" factory-method="getBean" id="bean3"/>```
测试
```
ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
Bean3 bean3=context.getBean("bean3",Bean3.class);
System.out.println(bean3);
```
结果
```
test.Bean3@c267ef4
```
