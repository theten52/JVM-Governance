# 11 其他注意事项

本节涵盖了影响垃圾回收的其他情况。

## 终结（Finalization）以及弱引用、软引用和虚引用。

一些应用程序通过使用终结（Finalization）机制以及弱引用、软引用或虚引用与垃圾回收进行交互。

然而，不建议使用终结（Finalization）方法。它可能会导致安全、性能和可靠性方面的问题。例如，依赖终结（Finalization）方法来关闭文件描述符会使外部资源（描述符）依赖于垃圾回收的及时性。

### 终结（Finalization）

一个类可以声明一个终结器（Finalization）—— 方法 `protected void finalize()` —— 其方法体用于释放任何底层资源。垃圾回收器（GC）会调度不可达对象的终结器（Finalization），该终结器（Finalization）会在垃圾回收器回收对象的内存之前被调用。

当从垃圾回收根（GC root）到某个对象不存在引用路径时，该对象就会变得不可达，从而符合垃圾回收的条件。（有关可达性的更多信息，请参阅“引用对象类型”。）垃圾回收根（GC root）包括来自活动线程的引用和 JVM 内部引用；它们是使对象保留在内存中的引用。

请参阅《Java 平台标准版故障排除指南》中的“监控待终结的对象”部分，以确定可终结对象是否在您的系统中累积。此外，您可以使用以下工具之一：

- JDK Mission Control：

  1. 在 JVM 浏览器中，右键单击您的 JVM 并选择“启动 JMX 控制台”。
  2. 在 MBean 浏览器的 MBean 树中，展开 java.lang 并选择 Memory。
  3. 在 MBean 特性中，属性 ObjectPendingFinalizationCount 是待终结（Finalization）处理的对象的大致数量。
- `jcmd`:

  - 运行以下命令以打印有关 Java 终结（Finalization）队列的信息；值 `*<pid>*` 是你的 JVM 的进程 ID：

    `jcmd *<pid>* GC.finalizer_info`

### 从终结（Finalization）机制迁移

要避免资源终结（Finalization）处理，请使用 `try-with-resources` 语句。这是一个 `try` 语句，用于声明一个或多个资源。资源是指在程序使用完毕后必须关闭的对象。 `try-with-resources` 语句可确保在代码块结束时关闭每个资源，即使发生一个或多个异常也是如此。有关更多信息，请参阅“带资源的 try 语句”。

### 引用对象类型

有三种引用对象类型： `SoftReference` 、 `WeakReference` 和 `PhantomReference` 。每种引用对象类型对应不同的可达性级别。以下是从最强到最弱的不同可达性级别，它们反映了对象的生命周期：

- 如果一个对象可以被某个线程在不遍历任何引用对象的情况下访问到，那么该对象就是强可达的。新创建的对象对于创建它的线程来说是强可达（*strongly reachable*）的。
- 如果一个对象不是强可达（*strongly reachable*）的，但可以通过遍历软引用（SoftReference）访问到，那么该对象就是软可达（*softly reachable*）的。
- 如果一个对象既不是强可达（*strongly reachable*）也不是软可达（*softly reachable*），但可以通过遍历弱引用访问到，那么该对象就是弱可达（*weakly reachable*）的。当指向一个弱可达（*weakly reachable*）对象的弱引用（WeakReference）被清除时，该对象就符合终结（Finalization）条件了。
- 如果一个对象既不是强可达（*strongly reachable*）、软可达（*softly reachable*），也不是弱可达（*weakly reachable*）的，它已经被终结（Finalization），并且有某个虚引用（PhantomReference）指向它，那么该对象就是虚可达（*phantom reachable*）的。
- 当一个对象无法通过上述任何方式被访问时，它就变得不可达（*unreachable*），因此可以被回收。

每个引用对象类型都封装了对特定对象的单个引用，该特定对象称为被引用对象。引用对象提供了清除被引用对象的方法。

以下是引用对象实例最常见的用途：

- 在仍允许系统在需要释放内存时对对象进行垃圾回收的同时，保持对该对象的访问（例如，一个在需要时可以重新生成的缓存值）
- 当一个对象达到特定的可达性级别时（结合 `ReferenceQueue` 类），确定并可能采取一些行动

## 显式垃圾回收

应用程序与垃圾回收进行交互的另一种方式是通过调用 `System.gc()` 显式地触发完整的垃圾回收。这可能会在不必要的时候强制进行一次大回收（例如，一次小回收就足够的情况），因此一般应避免这样做。可以通过使用标志 `-XX:+DisableExplicitGC` 禁用显式垃圾回收来衡量其对性能的影响，该标志会使虚拟机忽略对 `System.gc()` 的调用。

显式垃圾回收最常见的应用场景之一是远程方法调用（RMI）的分布式垃圾回收（DGC）。使用 RMI 的应用程序会引用其他虚拟机中的对象。在这些分布式应用程序中，如果不偶尔调用本地堆的垃圾回收，就无法回收垃圾，因此 RMI 会定期强制进行完整的垃圾回收。可以通过属性来控制这些垃圾回收的频率，如下例所示：

```
java -Dsun.rmi.dgc.client.gcInterval=3600000
    -Dsun.rmi.dgc.server.gcInterval=3600000 ...
```

此示例指定每小时进行一次显式垃圾回收，而非默认的每分钟一次。不过，这也可能导致某些对象需要更长时间才能被回收。如果不希望对分布式垃圾回收（DGC）活动的及时性设置上限，这些属性可以设置为 `Long.MAX_VALUE` ，从而使显式回收之间的时间间隔实际上变为无限长。

## 软引用（Soft References）

在服务器虚拟机中，软引用（Soft References）的存活时间比在客户端虚拟机中更长。可以使用命令行选项 `-XX:SoftRefLRUPolicyMSPerMB=<N>` 来控制清理速率，该选项指定了堆中每兆字节（MB）的可用空间下，软引用（Soft References）（一旦它不再被强引用（*strongly reachable*）可达）将存活的毫秒数（ms）。默认值是每兆字节 1000 毫秒，这意味着对于堆中每兆字节的可用空间，软引用（Soft References）（在对象的最后一个强引用（*strongly reachable*）被回收后）将存活 1 秒。这只是一个近似值，因为软引用（Soft References）仅在垃圾回收期间被清理，而垃圾回收可能会不定期发生。

## 类元数据

在 Java HotSpot 虚拟机中，Java 类有其内部表示形式，被称为类元数据。在 Java HotSpot 虚拟机的早期版本中，类元数据是在所谓的永久代中分配的。在 JDK 8 中，永久代被移除，类元数据在本地内存中分配。默认情况下，可用于类元数据的本地内存量是无限制的。使用选项 `MaxMetaspaceSize` 来对用于类元数据的本地内存量设置上限。

Java HotSpot 虚拟机显式管理用于元数据的空间。会向操作系统请求空间，然后将其划分为多个块。类加载器从其块中为元数据分配空间（一个块绑定到特定的类加载器）。当某个类加载器的类被卸载时，其块会被回收以供重用或返回给操作系统。元数据使用由 `mmap` 分配的空间，而不是由 `malloc` 分配的空间。

如果开启了 `UseCompressedOops` 并且使用了 `UseCompressedClassesPointers` ，那么将使用本地内存中两个逻辑上不同的区域来存储类元数据。 `UseCompressedClassPointers` 会使用 32 位偏移量来表示 64 位进程中的类指针， `UseCompressedOops` 对于 Java 对象引用也是如此。会为这些压缩类指针（32 位偏移量）分配一个区域。该区域的大小可以通过 `CompressedClassSpaceSize` 进行设置，默认大小为 1 吉字节（GB）。压缩类指针的空间在初始化时作为由 `mmap` 分配的空间进行预留，并根据需要进行提交。 `MaxMetaspaceSize` 适用于已提交的压缩类空间和其他类元数据空间的总和。

当对应的 Java 类被卸载时，类元数据会被释放。Java 类会因垃圾回收而被卸载，并且为了卸载类和释放类元数据，可能会触发垃圾回收。当为类元数据分配的空间达到一定水平（高水位线）时，会触发一次垃圾回收。垃圾回收之后，高水位线可能会根据从类元数据中释放的空间量而升高或降低。提高高水位线是为了避免过早触发另一次垃圾回收。高水位线最初设置为命令行选项 `MetaspaceSize` 的值。它会根据选项 `MaxMetaspaceFreeRatio` 和 `MinMetaspaceFreeRatio` 进行升高或降低。如果类元数据可用的已分配空间占类元数据总已分配空间的百分比大于 `MaxMetaspaceFreeRatio` ，则高水位线将降低。如果小于 `MinMetaspaceFreeRatio` ，则高水位线将升高。

为选项 `MetaspaceSize` 指定一个更高的值，以避免因类元数据而提前触发垃圾回收。为应用程序分配的类元数据量取决于应用程序本身，并且对于 `MetaspaceSize` 的选择没有通用准则。 `MetaspaceSize` 的默认大小取决于平台，范围从 12 MB 到大约 20 MB。

堆的打印输出中包含了有关元数据所用空间的信息。示例 11 - 1“典型的堆打印输出”展示了一个典型的输出。

示例 11 - 1 典型的堆打印输出

```

Heap
  PSYoungGen      total 10752K, used 4419K
    [0xffffffff6ac00000, 0xffffffff6b800000, 0xffffffff6b800000)
    eden space 9216K, 47% used
      [0xffffffff6ac00000,0xffffffff6b050d68,0xffffffff6b500000)
    from space 1536K, 0% used
      [0xffffffff6b680000,0xffffffff6b680000,0xffffffff6b800000)
    to   space 1536K, 0% used
      [0xffffffff6b500000,0xffffffff6b500000,0xffffffff6b680000)
  ParOldGen       total 20480K, used 20011K
      [0xffffffff69800000, 0xffffffff6ac00000, 0xffffffff6ac00000)
    object space 20480K, 97% used 
      [0xffffffff69800000,0xffffffff6ab8add8,0xffffffff6ac00000)
  Metaspace       used 2425K, capacity 4498K, committed 4864K, reserved 1056768K
    class space   used 262K, capacity 386K, committed 512K, reserved 1048576K
```

在以 `Metaspace` 开头的行中， `used` 值表示已加载类所使用的空间大小。 `capacity` 值表示当前已分配块中可用于元数据的空间大小。 `committed` 值表示可用于块的空间大小。 `reserved` 值表示为元数据预留（但不一定已提交）的空间大小。以 `class space` 开头的行包含压缩类指针元数据的相应值。
