# IO流

[TOC]

## 字符流、字节流

根据数据的流向分为：**输入流** 和 **输出流**。

根据数据的类型分为：**字节流** 和 **字符流**。

| |输入流|	输出流|
|---|---|---|
|字节流|	字节输入流 InputStream|	字节输出流 OutputStream|
|字符流|	字符输入流 Reader |	字符输出流 Writer|

## 缓冲流

缓冲流在内存中设置一个缓存区，缓冲区先存储足够的待操作数据后，再与内存或磁盘进行交互。这样，在总数据量不变的情况下，通过提高每次交互的数据量，减少了交互次数。 

我们搬砖的时候，一块一块地往车上装肯定是很低效的。我们可以使用一个小推车，先把砖装到小推车上，再把这小推车推到车前，把砖装到车上。这个例子中，小推车可以视为缓冲区，小推车的存在，减少了我们装车次数，从而提高了效率。 

## IO流使用的设计模式

装饰者模式主要实现的是功能的动态扩展。通常的扩展方法是通过继承，但是导致动态扩展类创建过多的类。

如读取文件的时候会用到fileinputstream，当我们希望输入流读取更快的时候会把FileInputStream传入BufferedInputStream的构造函数做一个装饰也就是功能的拓展，让文件缓存到内存读取更快。

```
FileInputStream fps = new FileInputStream(b.txt);
BufferedInputStream bis = new BufferedInputStream(fps)
```

### BufferedInputStream为什么能起到缓存作用？

因为FileInputStream.read()每次只能从内核中读取一个字节，而BufferedInputStream每次都从内核中读取多个字节存放在buf缓冲区中，BufferedInputStream.read（）的时候从buf取出一个字节。因此BufferedInputStream的缓存能够减少数据从内核空间到用户空间的拷贝次数。但当使用int read(byte b[], int off, int len)方法时，已经相当于使用了缓存，每次都复制多个字节到用户空间，BufferedInputStream和FileInputStream的效率就相差不大了。

## 参考资料：

 [史上最骚最全最详细的IO流教程，没有之一！ - 宜春 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yichunguo/p/11775270.html) 