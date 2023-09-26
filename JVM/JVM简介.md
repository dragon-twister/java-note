# JVM简介


**JDK、JRE、JVM的关系**

- JDK  
  JDK(Java Development Kit) 是 Java 语言的软件开发工具包（SDK）。JDK 物理存在，是 programming tools、JRE 和 JVM 的一个集合  

![](http://upload-images.jianshu.io/upload_images/634730-73b9b41d97382d8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

- JRE  
  JRE（Java Runtime Environment）Java 运行时环境，JRE 物理存在，主要由Java API 和 JVM 组成，提供了用于执行 java 应用程序最低要求的环境。  

- JVM  
  JVM(Java Virtual Machine) 是一种软件实现，执行像物理机程序的机器（即电脑）。  
  本来，JVM 通过执行 Java bytecode 可以使 java 代码运行在各种硬件之上。  

**JVM 的整体架构**

先看一下 java 代码执行过程  

![](http://upload-images.jianshu.io/upload_images/634730-089a64921220b40d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

* 类加载器 Class Loader

* 执行引擎 Excution Engin

  解释器：虚拟机启动时会根据预定义对字节码采用逐行解释的方式执行，将每条字节码文件中的内容解释为对应系统平台的本地机器指令执行；

  JIT编译器：虚拟机将源代码编译成本地机器平台相关的机器语言，并且寻找热点高频执行的代码将其放入元空间中，即元空间中存放的JIT缓存代码；

  垃圾回收：对于没有任何引用的对象标记为垃圾，会被回收释放内存空间。

*  运行数据区 Runtime Data Areas
  	
