---
title: 平滑加权轮询的Java实现
date: 2020-03-24 18:18:57
tags:
- Java
categories:
- Java
---
原算法参考文章  
[负载均衡算法 — 平滑加权轮询](https://www.fanhaobai.com/2018/12/load-balance-smooth-weighted-round-robin.html)

本文只是实现了其Java版本
```
public class LoadBalance {
    public static void main(String[] args) {
        int[] config_weight=new int[3];//配置权重
        Map<Integer,String> map=new HashMap<>();
        map.put(0,"A");
        map.put(1,"B");
        map.put(2,"C");
        config_weight[0]=5;
        config_weight[1]=1;
        config_weight[2]=1;
        int[] eff_weight=config_weight.clone();//有效权重,初始化为配置权重
        List<String> list=new ArrayList<>();
        for(int i=0;i<7;i++){
            list.add(getServer(config_weight,eff_weight,map));
        }
        System.out.println(list);

    }
    public static String getServer(int[] config_weight,int[] eff_weight,Map<Integer,String> map){
        int currentPos=-1;
        int max=0;
        int curWeightSum=0;
        for(int i=0;i<eff_weight.length;i++){
            if(eff_weight[i]>max){
                currentPos=i;
                max=eff_weight[i];
            }
            curWeightSum+=eff_weight[i];
        }
        eff_weight[currentPos]-=curWeightSum;
        for(int i=0;i<eff_weight.length;i++){
            eff_weight[i]=eff_weight[i]+config_weight[i];
        }
        return map.get(currentPos);
    }
}
```