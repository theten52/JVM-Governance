# Java平台标准版HotSpot虚拟机垃圾回收调优指南，第8版【中文翻译】

# 目录

## [前言](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/preface.html#gct_preface)

* [读者对象](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/preface.html#gct_audience)
* [文档可访问性](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/preface.html#gct_accessibility)
* [相关文档](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/preface.html#gct_related)
* [约定](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/preface.html#gct_conventions)

## [1 引言](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/introduction.html#sthref3)

## [2 自适应优化（Ergonomics）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#ergonomics)

* [垃圾回收器、堆和运行时编译器默认选择](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref5)
* [基于行为的优化](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref11)
  * [最大暂停时间目标](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref12)
  * [吞吐量目标](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref13)
  * [内存占用目标(Footprint Goal)](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref14)
* [调优策略](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#sthref15)

## [3 分代（Generations）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/generations.html#sthref16)

* [性能考量](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/generations.html#sthref19)
* [度量](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/generations.html#sthref20)

## [4 分代大小调整](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html#sizing_generations)

* [总堆大小](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html#sthref22)
* [新生代](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html#sthref24)
  * [幸存区（Survivor Space）大小调整](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html#sthref25)

## [5 可用的垃圾回收器](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/collectors.html#sthref27)

* [选择垃圾回收器](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/collectors.html#sthref28)

## [6 并行垃圾回收器（The Parallel Collector）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector)

* [分代](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_generations)
* [并行垃圾回收器的自动优化机制](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_ergonomics)
  * [目标优先级](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_priority)
  * [代大小调整](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_gen_size)
  * [默认堆大小](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#default_heap_size)
    * [客户端 JVM 默认初始堆大小和最大堆大小](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#sthref30)
    * [服务器 JVM 默认初始堆大小和最大堆大小](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#sthref31)
    * [指定初始和最大堆大小](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#sthref32)
* [垃圾回收时间过长和内存溢出错误（OutOfMemoryError）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_excessive_gc)
* [度量](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#parallel_collector_measurements)

## [7 主要并发收集器](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/concurrent.html#mostly_concurrent)

* [并发开销](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/concurrent.html#sthref33)
* [其他参考资料](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/concurrent.html#sthref34)

## [8 并发标记清除（Concurrent Mark Sweep，CMS）垃圾回收器](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#concurrent_mark_sweep_cms_collector)

* [并发模式失败](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#concurrent_mode_failure)
* [垃圾回收时间过长和内存溢出错误（OutOfMemoryError）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref35)
* [浮动垃圾（Floating Garbage）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref36)
* [暂停](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref37)
* [并发阶段](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref38)
* [启动并发收集周期](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref39)
* [调度暂停](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref40)
* [增量模式](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#CJAGIIEJ)
  * [命令行选项](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref41)
  * [推荐选项](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#i_cms_recommended_options)
  * [基本故障排除](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref43)
* [度量](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#sthref45)

## [9 垃圾优先（Garbage-First，G1）垃圾回收器](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#garbage_first_garbage_collection)

* [分配（疏散）失败](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#allocation_evacuation_failure)
* [浮动垃圾](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#sthref47)
* [暂停](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#pauses)
* [卡表（Card Tables）和并发阶段](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#sthref48)
* [启动并发收集周期](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#sthref49)
* [暂停时间目标](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#pause_time_goal)

## [10 垃圾优先（G1）垃圾回收器调优](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#g1_gc_tuning)

* [垃圾回收阶段](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#sthref50)
* [新生代垃圾回收](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#sthref51)
* [混合垃圾回收](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#sthref52)
* [标记周期的阶段](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#sthref53)
* [重要默认值](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#important_defaults)
* [如何解锁实验性 VM 标志](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#how_to_unlock_experimental_vm_flags)
* [建议](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#recommendations)
* [溢出和耗尽的日志消息](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#sthref61)
* [巨型对象（Humongous Objects）和巨型分配（Humongous Allocations）](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html#humongous)

## [11 其他注意事项](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/considerations.html#sthref62)

* [终结（Finalization）以及弱引用、软引用和虚引用](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/considerations.html#sthref63)
* [显式垃圾回收](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/considerations.html#sthref64)
* [软引用](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/considerations.html#sthref65)
* [类元数据](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/considerations.html#sthref66)

---

# 相关链接

英文版文档链接: [Java Platform, Standard Edition HotSpot Virtual Machine Garbage Collection Tuning Guide](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/)
