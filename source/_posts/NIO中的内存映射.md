---
title: NIO中的内存映射
date: 2020-01-09 21:19:20
tags:
- Java
- NIO
categories:
- java
---

#### 内存映射概念
这个功能主要是为了提高大文件的读写速度而设计的。内存映射文件(memory-mappedfile)能让你创建和修改那些大到无法读入内存的文件。大多数操作系统都可以利用虚拟内存实现来讲一个文件或者文件的一部分“映射”到内存中，然后，这个文件就可以当作内存数组一样的访问。将文件的一段区域映射到内存中，比传统的文件处理速度要快很多。内存映射文件它虽然最终也是要从磁盘读取数据，但是它并不需要将数据读取到OS内核缓冲区，而是直接将进程的用户私有地址空间中的一部分区域与文件对象建立起映射关系，就好像直接从内存中读、写文件一样，速度会大大加快。
#### 代码示例
```
public static void main(String[] args) throws IOException {
        try {
            //内存映射方式读取文件
            File file=new File("E:\\书籍\\Tensorflow 实战.pdf");
            long len=file.length();
            long startTime=System.currentTimeMillis();
            MappedByteBuffer mappedByteBuffer=new RandomAccessFile(file,"r")
                    .getChannel()
                    .map(FileChannel.MapMode.READ_ONLY,0,len);

            for(int offset=0;offset<len;offset++){
                mappedByteBuffer.get();
            }
            long endTime=System.currentTimeMillis();
            System.out.println("使用内存映射方式读取文件总耗时： "+(endTime - startTime));

            //普通IO流的方式
            long startTime1=System.currentTimeMillis();
            BufferedReader bufferedReader=null;
            try{
                bufferedReader=new BufferedReader(new FileReader(file));
                while ((bufferedReader.readLine()) != null) {
                }
                bufferedReader.close();
            }catch (IOException e){

            }finally {
                if (bufferedReader != null) {
                    try {
                        bufferedReader.close();
                    }catch (IOException e){

                    }
                }
            }
            long endTime1=System.currentTimeMillis();
            System.out.println("使用普通IO流方式读取文件总耗时： "+(endTime1- startTime1));
        }catch (Exception e){

        }
    }
```
结果
```
使用内存映射方式读取文件总耗时： 62
使用普通IO流方式读取文件总耗时： 2291
```
可见使用内存映射 速度提高了很多
#### 参数说明
##### FileChannel支持的几种模式
* MapMode.READ_ONLY：只读，试图修改得到的缓冲区将导致抛出异常。
* MapMode.READ_WRITE：读/写，对得到的缓冲区的更改最终将写入文件；但该更改对映射到同一文件的其他程序不一定是可见的。
* MapMode.PRIVATE：所产生的缓冲区是可写的，但是任何修改对这个缓冲区来说都是私有的，不会传播到文件中

##### map中参数
* mode：FileChannel的模式
* position：文件映射起始位置
* size：映射区域的大小，必须为非负数，不能大于Integer.MAX_VALUE

#### 内存映射特点
* 通过使用内存映射IO，你可以将大文件加载到内存
* 内存映射IO比起Java中的IO流要快的多
* 内存映射文件主要用于性能敏感的应用，例如高频电子交易平台
* 现代操作系统一般根据需要将文件的部分映射为内存的部分，从而实现文件系统。Java 内存映射机制不过是在底层操作系统中可以采用这种机制时，提供了对该机制的访问。
* force()将缓冲区的内容强制刷新到存储设备中去、load()将存储设备中的数据加载到内存中

