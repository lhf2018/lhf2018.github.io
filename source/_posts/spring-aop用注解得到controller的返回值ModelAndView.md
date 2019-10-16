---
title: spring aop用注解得到controller的返回值ModelAndView
date: 2019-10-16 21:29:06
tags:
- spring
categories:
- spring
---
在Advice（增强方法）上面添加**@AfterReturning**  
returning中就是返回值，不过是Object类，在参数位置写上Object view，用来接受参数，在后面通过类型转换转为ModelAndView

```
@AfterReturning(returning = "view",pointcut = "execution(* controller.testController.*(..))")
public void insertMessage(Object view){
	System.out.println(view);
}```
