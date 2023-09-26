Linux中的零拷贝

what

减少数据拷贝次数

why

拷贝降低数据传输的效率

拷贝需要CPU进行IO中断，带来上下文切换的资源消耗

how

mmap()

sendFile()
