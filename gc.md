**gc回收过程**

1.判断对象是否存活
判断对象是否存活，早期采用对象是否存在引用来判断，但是这个方法对循环引用不起作用，
所以现在一般不采用，一般使用引用链方法，判断gc Roots 到对象是否存在路径,gc Roots 一般是一些全局性的应用

2.标记
将不再存活的对象标记为可清除

3.清除

**垃圾回收算法**

1.Mark-Sweep(标记清除算法)
最基本的垃圾回收算法，基于** gc 回收过程中描述一样**
存在着两点缺陷
从时间上来看，标记和清除过程都特别耗时
从空间上来看，垃圾回收后，会产生很多小的内存碎片。造成内存的不连续，无法给大的对象分配内存空间，
更快的触发下一次gc

2.复制算法

将内存分为两块，一次只使用一块，当执行垃圾回收时，将存活的对象copy到未使用的内存空间，然后将剩下的内存一次性全部清除

优点：时间上，不需要标记操作了，提高了清除效率，空间上解决了内存碎片化问题。
缺点：有另一块内存空间闲置不用，造成了内存浪费；
需要保证较小一块内存空间>每次gc时候存活的对象大小；
当存活对象比较多，复制操作就会比较耗时

3.标记-整理

在标记-清除 算法基础上，改进了清除过程
标记好对象后，不进行清理，将存活的对象移除到一端，然后将边界以外的对象都清除掉

优点：不需要考虑gc时，存活对象的占比
缺点:相比较复制算法，效率要低

4.分代收集

根据对象存活的周期，将对象分成不同的内存区域，比如新生代，老生代。
不同的区域采用不同的垃圾回收算法。
比如新生代可以采用复制算法
老生代采用 标记-整理算法

**垃圾收集器**

1.Serial 收集器

最基本的收集器。单线程，新生代采用复制算法，老生代采用标记-整理 算法
gc时，必须将正常工作的线程都停掉

优点是简单高效，没有线程开销，适合一些client模式的桌面应用，需要回收的垃圾在一两百M 以内

2.ParNew 收集器

Serial收集器的多线程版本
主要用于server模式的新生代垃圾收集器。
Serial 收集器与ParNew 是唯二可以与CMS 一起使用的新生代虚拟机

3.Parallel Scavenge

使用复制算法，并行的多线程收集器。
关注于提高throughput（执行代码/(执行代码+gc时间）
而其他收集器关注于gc时，用户线程的执行停顿时间。
所以其他收集器比较适用于与用户交互的程序，而Parallel Scavenge 收集器适合后台运算，不需要太多交互的应用
