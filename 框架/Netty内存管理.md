 ![img](https://upload-images.jianshu.io/upload_images/10356017-96a39394c1d2b710.png?imageMogr2/auto-orient/strip|imageView2/2/w/1000/format/webp) 

 1个Arena由两个PoolSubpage数组和多个ChunkList组成。 

 两个PoolSubpage数组分别为tinySubpagePools和smallSubpagePools。多个ChunkList按照双向链表排列。

每个ChunkList里包含多个Chunk， 每个Chunk里包含多个Page（默认2048个），每个Page（默认大小为8k字节）由多个Subpage组成。



 申请内存分配时，会讲分配的规格分为几类：TINY，SAMLL，NORMAL和HUGE，分别对应不同的范围，处理过程也不相同。



**那么PoolChunk要如何管理才能兼顾分配效率(指尽可能快的找出可分配的内存且保证此次分配的内存是连续的)和使用效率(尽可能少的避免内存浪费，做到物尽其用)的？** 

 Netty采用了Jemalloc的想法。
首先PoolChunk通过一个完全二叉树来组织内部的内存。以默认的ChunkSize为16M, PageSize为8K为例，一个PoolChunk可以划分成2048个Page。将这2048个Page看作是叶子节点的宽度，可以得到一棵深度为11的树(2^11=2048)。

我们让每个叶子节点管理一个Page，那么其父节点管理的内存即为两个Page(其父节点有左右两个叶子节点)，以此类推，树的根节点管理了这个PoolChunk所有的Page(因为所有的叶子结点都是其子节点)，而树中某个节点所管理的内存大小即是以该节点作为根的子树所包含的叶子节点管理的全部Page。

这样做的好处就是当你需要内存时，很快可以找到从何处分配内存（你只需要从上往下找到所管理的内存为你需要的内存的节点，然后将该节点所管理的内存分配出去即可)，并且所分配的内存还是连续的(只要保证相邻叶子节点对应的Page是连续的即可)。



如何得知chunk内哪些page节点有内存可用？

- memoryMap[] 和 depthMap[] 初识化完成时，如上图所示，数组 index 代表树的节点编号（从1开始，1-4095），数组 value 存出当前节点编号在树中的高度（从0开始，0-11）。两个数组的内容完全相同。
- depthMap[] 初始化完成后，便永远不会变化，仅用来通过节点编号快速获取树的高度。depthMap[1024]=10、depthMap[2048]=11，毕竟数组查询 O(1) 的时间复杂度，不需要每次在进行计算
- memoryMap[] 初识化完成后，根据节点的分配情况，value 值会进行相应的更改。以及根据 value 值判断该节点是否可以被分配。

memoryMap[] 中的 value 值从小到大，会有下述三种状态：

1. memoryMap[id] = depthMap[id] ，该节点没有被分配。如初始化完成时此种状态。
2. depthMap[id] < memoryMap[id] < 最大高度(12)。至少有一个子节点被分配，但尚未完全被分配，不能再分配该高度对应的内存，只能根据实际分配较小一些的内存。
3. memoryMap[id] = 最大高度(12) ，该节点及其子节点已被完全分配，没有剩余空间。



 [Netty-真实内存池 - 简书 (jianshu.com)](https://www.jianshu.com/p/4875dfa6eba9) 

 [谈谈Netty内存管理 - insaneXs - 博客园 (cnblogs.com)](https://www.cnblogs.com/insaneXs/p/13726158.html) 

 [面试官：谈谈Netty内存管理 ！ (qq.com)](https://mp.weixin.qq.com/s/6RWwwnXwf4c84BQYy2_6fQ) 