# String

**String 为什么设计为不可变**

字符串常量池，一个值有多个引用，如果字符串可变的话有，只改变一个值，会影响多个引用。

final指的是栈内存的引用所指向的内存地址不可变，但是堆里面存在的数据是可变的。

所以改变String值的办法还是有的，通过反射拿出内部私有成员变量，也就是存储字符串的数组，然后修改。



**String、StringBuffer、StringBuilder的区别**

String 不可变，因此线程安全

StringBuilder不是线程安全

StringBuffer是线程安全的，内部使用synchronized进行同步

StringBuffer、StringBuilder可以被多次修改、不产生新的对象



**Java中的substring()用法**

- Method1:  substring(int beginIndex)

返回从起始位置（beginIndex）至字符串末尾的字符串

```
String str = "Hello Java World!";
str.substring(2);  
//return "llo Java World!";　　
```

- Method2:  substring(int beginIndex, int endIndex)

返回从起始位置（beginIndex）到目标位置（endIndex）之间的字符串，但不包含目标位置（endIndex）的字符

```
String str = "Hello Java World!";
str.substring(2,4);　
//return "ll";
```



