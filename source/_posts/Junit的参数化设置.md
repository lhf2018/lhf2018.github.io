---
title: Junit的参数化设置
date: 2019-11-05 20:06:05
tags:
- Junit
categories:
- Junit
---
使用junit进行测试，对junit进行参数化设置，能够更容易的修改传入参数  
要被测试类的代码
```
public class TaskTest {
    public int add(int x,int y){
        return x+y;
    }
}
```
用来进行测试的类
```
@RunWith(Parameterized.class)
public class ParameterTest {
    int excepted=0;
    int input1=0;
    int input2=0;
    
    @Parameterized.Parameters
    public static Collection<Object[]> t(){
        return Arrays.asList(new Object[][]{
                {2,1,2},
                {2,2,2}
        });
    }

    public ParameterTest(int excepted, int input1, int input2) {
        this.excepted = excepted;
        this.input1 = input1;
        this.input2 = input2;
    }
    @Test
    public void testadd(){
        Assert.assertEquals(excepted,new TaskTest().add(input1,input2));
    }
}
```
执行testadd，输出结果
![微信截图_20191105201332.png](https://i.loli.net/2019/11/05/aHd7z8ZNp2SLV4G.png)

参数化设置的要求
* 更改默认的测试运行器为RunWith(Parameterized.class)
* 声明变量来存放预期值和结果值
* 声明一个返回值为Collection的公共静态方法，并使用@Parameters进行修饰
* 为测试类声明一个带有参数的公共构造函数，并在其中为之声明变量赋值