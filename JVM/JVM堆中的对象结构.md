####  对象头部

  - Mark Word

    - lock:2位的锁状态标记位，由于希望用尽可能少的二进制位表示尽可能多的信息，所以设置了lock标记。该标记的值不同，整个mark word表示的含义不同。

      - biased_lock lock 状态

        0 01 无锁

        1 01 偏向锁

        0 00 轻量级锁

        0 10 重量级锁

        0 11 GC标记

    - biased_lock：对象是否启用偏向锁标记，只占1个二进制位。为1时表示对象启用偏向锁，为0时表示对象没有偏向锁。

    - age：4位的Java对象年龄。在GC中，如果对象在Survivor区复制一次，年龄增加1。当对象达到设定的阈值时，将会晋升到老年代。默认情况下，并行GC的年龄阈值为15，并发GC的年龄阈值为6。由于age只有4位，所以最大值为15，这就是-XX:MaxTenuringThreshold选项最大值为15的原因。

    - identity_hashcode：25位的对象标识Hash码，采用延迟加载技术。调用方法System.identityHashCode()计算，并会将结果写到该对象头中。当对象被锁定时，该值会移动到管程Monitor中。

    - thread：持有偏向锁的线程ID。

    - ptr_to_lock_record：指向栈中锁记录的指针。

    - ptr_to_heavyweight_monitor：指向管程Monitor的指针。

    - epoch：偏向时间戳。

  - class pointer 类型指针，虚拟机通过这个指针来确定这个对象是哪个类的实例.

  - array length

 

实际数据：

对象真正存储的有效信息就是放在这里的，也是在程序代码中所定义的各种类型的字段内容。无论是从父类继承下来的，还是在子类中定义的，都需要记录起来。 


 