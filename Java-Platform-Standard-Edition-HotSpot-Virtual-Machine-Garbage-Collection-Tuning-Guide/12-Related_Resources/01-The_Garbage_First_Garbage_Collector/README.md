# G1 垃圾回收器

原文链接：https://www.oracle.com/java/technologies/javase/hotspot-garbage-collection.html

#### 介绍

G1（Garbage-First）垃圾回收器在 Oracle JDK 7 更新 4 及后续版本中得到全面支持。G1 回收器是一种服务器风格的垃圾回收器，适用于具有大内存的多处理器机器。它能大概率满足垃圾回收（GC）暂停时间目标，同时实现高吞吐量。诸如全局标记等全堆操作会与应用程序线程并发执行。这避免了与堆或存活数据大小成比例的中断。

#### 技术描述

G1 收集器通过多种技术实现了高性能和停顿时间目标。

堆被划分为一组大小相等的堆区域，每个区域都是一段连续的虚拟内存范围。G1 执行并发全局标记阶段，以确定整个堆中对象的存活状态。标记阶段完成后，G1 会知道哪些区域大部分是空的。它会首先收集这些区域，这通常会产生大量的空闲空间。这就是为什么这种垃圾回收方法被称为“垃圾优先”（Garbage-First）。顾名思义，G1 将其收集和整理活动集中在堆中可能充满可回收对象（即垃圾）的区域。G1 使用停顿预测模型来满足用户定义的停顿时间目标，并根据指定的停顿时间目标选择要收集的区域数量。

G1 识别出的适合回收的区域会使用转移（evacuation）方式进行垃圾回收。G1 将对象从堆中的一个或多个区域复制到堆中的单个区域，在此过程中既对内存进行了压缩，又释放了内存。这种转移操作会在多处理器上并行执行，以减少停顿时间并提高吞吐量。因此，每次进行垃圾回收时，G1 都会在用户定义的停顿时间内持续努力减少内存碎片。这是之前的两种方法都无法做到的。CMS（并发标记清除，Concurrent Mark Sweep）垃圾回收不会进行内存压缩。ParallelOld 垃圾回收仅对整个堆进行压缩，这会导致相当长的停顿时间。

需要注意的是，G1 不是实时收集器。它有很高的概率达到设定的停顿时间目标，但并非绝对确定。基于之前收集的数据，G1 会估算在用户指定的目标时间内可以收集多少个区域。因此，收集器对收集这些区域的成本有一个相当准确的模型，并使用该模型来确定在停顿时间目标范围内要收集哪些区域以及收集多少个区域。

有关使用和配置 G1 的更多信息，请参阅命令行选项。

#### G1 的推荐使用场景

G1 的首要关注点是为运行需要大堆内存且垃圾回收（GC）延迟有限的应用程序的用户提供解决方案。这意味着堆内存大小约为 6GB 或更大，并且稳定且可预测的暂停时间低于 0.5 秒。

如果应用程序具备以下一个或多个特征，那么目前使用 CMS 或 ParallelOld 垃圾回收器运行的应用程序在切换到 G1 垃圾回收器后将受益。

- 超过 50% 的 Java 堆被存活数据占用。
- 对象分配率或晋升率差异显著。
- 不期望出现的长时间垃圾回收或内存整理暂停（超过 0.5 到 1 秒）

#### 未来

G1 计划作为并发标记 - 清除收集器（CMS）的长期替代方案。将 G1 与 CMS 进行比较，有一些差异使 G1 成为更好的解决方案。一个差异是 G1 是一种压缩式收集器。G1 进行充分的压缩，以完全避免使用细粒度的空闲列表进行内存分配，而是依赖于内存区域。这大大简化了收集器的部分功能，并且在很大程度上消除了潜在的内存碎片问题。此外，与 CMS 收集器相比，G1 提供了更可预测的垃圾回收停顿，并且允许用户指定期望的停顿目标。

如果您有兴趣帮助改进 G1，请试用它并通过 OpenJDK 和 hotspot-gc-use@openjdk.java.net 邮件列表提供反馈。

#### 资源

- HotSpot 垃圾收集器说明：《Java HotSpot 虚拟机中的内存管理》白皮书：链接（PDF） TODO PDF
- 原始的 CMS 论文：Printezis, T. 和 Detlefs, D. 2000 年。一种分代的大部分并发垃圾回收器。见第二届国际内存管理研讨会会议论文集（美国明尼苏达州明尼阿波利斯，2000 年 10 月 15 - 16 日）。https://dl.acm.org/citation.cfm?id=362422.362480（需要访问 ACM 的门户） TODO 翻译
- 原始 G1 论文：Detlefs, D.、Flood, C.、Heller, S.和 Printezis, T. 2004 年。Garbage-first 垃圾回收。见第 4 届国际内存管理研讨会会议记录（加拿大不列颠哥伦比亚省温哥华，2004 年 10 月 24 - 25 日）。https://dl.acm.org/citation.cfm?id=1029879（需访问 ACM 门户）TODO 翻译

#### Resources

- Description of HotSpot GCs: Memory Management in the Java HotSpot Virtual Machine White Paper: [link](https://www.oracle.com/technetwork/java/javase/memorymanagement-whitepaper-150215.pdf) (PDF)
- The original CMS paper: Printezis, T. and Detlefs, D. 2000. A generational mostly-concurrent garbage collector. In *Proceedings of the 2nd international Symposium on Memory Management* (Minneapolis, Minnesota, United States, October 15 - 16, 2000). https://dl.acm.org/citation.cfm?id=362422.362480 (requires access to ACM's portal)
- The original G1 paper: Detlefs, D., Flood, C., Heller, S., and Printezis, T. 2004. Garbage-first garbage collection. In Proceedings of the 4th international Symposium on Memory Management (Vancouver, BC, Canada, October 24 - 25, 2004). https://dl.acm.org/citation.cfm?id=1029879 (requires access to ACM's portal)