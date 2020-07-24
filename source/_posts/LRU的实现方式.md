---
title: LRU的实现方式
date: 2020-07-23 16:35:58
tags:
- 数据结构
categories:
- 数据结构
---
面试时被问到LRU的实现，决定尝试使用Java实现一下

### 一、基于LinkedHashMap
LinkedHashMap中使用了hashmap和双链表，只要重写removeEldestEntry方法，就会在超过固定大小时移除最早的节点
```
public class LRU {
    private LinkedHashMap<Integer,Integer> cache;
    private final int CAPACITY=10;
    public LRU(int capacity){
        cache=new LinkedHashMap<Integer, Integer>(capacity,0.75f,true){
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return size()>CAPACITY;
            }
        };
    }
    public int get(int key){
        return cache.getOrDefault(key,-1);
    }
    public void put(int key,int value){
        cache.put(key,value);
    }
}
```
其中``getOrDefault``方法
```    
public V getOrDefault(Object key, V defaultValue) {
       Node<K,V> e;
       if ((e = getNode(hash(key), key)) == null)
           return defaultValue;
       if (accessOrder)
           afterNodeAccess(e);
       return e.value;
   }
```
跟踪到``afterNodeAccess``方法
```
void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }
```
通过源码可以得知当accessOrder为true时，则会在访问了节点以后把当前被访问的节点放到链表的末尾
### 二、HashMap+双链表

```
public class LRUCachel {
    class Node{
        int key;
        int value;
        Node pre;
        Node next;

        public Node(int key, int value){
            this.key = key;
            this.value = value;
        }
    }
    private int capacity;
    HashMap<Integer,Node> map=new HashMap<>();
    Node head;
    Node end;
    //初始化大小，缓存是有大小限制的，超过规定的大小时就得移除
    public LRUCachel(int capacity) {
        this.capacity = capacity;
    }
    //获取一个缓存数据之后，应该把这个数据在当前位置中移除，并重新添加到头的位置，这些都是在返回数据之前完成的
    public int get(int key){
        if(map.containsKey(key)){
            Node node=map.get(key);
            remove(node);
            setHead(node);
            return node.value;
        }
        return -1;
    }
    //移除元素分为，N的前边和N的后边都要看是怎么样的情况
    public void remove(Node node){
        if(node.pre!=null){
            node.pre.next=node.next;
        }else {
            head=node.next;
        }
        if(node.next!=null){
            node.next.pre=node.pre;
        }else {
            end=node.pre;
        }
    }
    //放刚刚访问到的放到头部
    public void setHead(Node node){
        node.next=head;
        node.pre=null;
        if(head!=null){
            head.pre=node;
        }
        head=node;
        if(end==null){
            end=head;
        }
    }
    //设置看原位置是否有元素，如果有的话就替换，这证明使用过了，然后将其替换为头结点的元素，若果是一个新的节点就要判断它的大小是否符合规范
    public void set(int key,int value){
        if(map.containsKey(key)){
            Node old =map.get(key);
            old.value=value;
            remove(old);
            setHead(old);
        }else {
            Node created=new Node(key,value);
            if (map.size() > capacity) {
                map.remove(end.key);
                remove(end);
                setHead(created);
            }else {
                setHead(created);
            }
            map.put(key,created);
        }
    }
}
```