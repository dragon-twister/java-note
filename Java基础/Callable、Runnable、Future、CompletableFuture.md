Callable、Runnable、Future、CompletableFuture

- Callable，有结果的同步行为，比如做蛋糕，产生蛋糕
- Runnable，无结果的同步行为，比如喝牛奶，仅仅就是喝
- Future，异步封装Callable/Runnable，比如委托给师傅（其他线程）去做糕点
-  CompletableFuture，封装Future，使其拥有回调功能，比如让师傅主动告诉我蛋糕做好了。



 [Callable接口及Futrue接口详解 - 纵码万水千山 - 博客园 (cnblogs.com)](https://www.cnblogs.com/guanbin-529/p/11784914.html#_label1_2) 



##### `CompletableFuture`并不是响应式，因为主线程会阻塞

