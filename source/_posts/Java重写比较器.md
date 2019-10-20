---
title: Java重写比较器进行排序
date: 2019-10-20 12:56:14
tags:
- Java基础
categories:
- Java基础
---
#### Comparator

Comparator 是比较器接口。

我们若需要控制某个类的次序，而该类本身不支持排序(即没有实现Comparable接口)；那么，我们可以建立一个“该类的比较器”来进行排序。这个“比较器”只需要实现Comparator接口即可。  
##### 重写Comparator的compare方法进行重新比较
 ```
 Arrays.sort(temp, new Comparator<Object>() {
    @Override
    public int compare(Object o1, Object o2) {
        return o1.val-o2.val;
    }
});```
        
其中Comparator&lt;T&gt;中的T为要比较的对象类型，但不能为int这样的数据基本类型，如果要使用这个方法对int数组进行排序，应该转换为Integer数组；但是支持int [ ]类型  
**实例**
```
int[][] temp={{2,3},{1,4}};
Arrays.sort(temp, new Comparator<int[]>() {
    @Override
    public int compare(int[] o1, int[] o2) {
        return o1[0]-o2[0];
    }
}); ```

``o1-o2``是升序排列，``o2-o1``是降序排列

#### Comparable
Comparator 是一个内部的排序接口，实现该接口意味着这个类可以被排序
```
public class Person implements Comparable<Person> {
    int age=0;

    public Person(int age) {
        this.age = age;
    }

    @Override
    public int compareTo(Person Person) {
        return this.age-Person.age;
    }

    public static void main(String[] args) {
        // 新建ArrayList(动态数组)
        ArrayList<Person> list = new ArrayList<>();
		// 添加对象到ArrayList中
        list.add(new Person(20));
        list.add(new Person(30));
        list.add(new Person(10));
        list.add(new Person(40));
        Collections.sort(list);
        for(int i=0;i<list.size();i++){
            System.out.println(list.get(i).age);
        }
    }
}```
输出结果
```
10
20
30
40
```
list会使用Comparable这个接口中的compareTo方法对类进行排序
 