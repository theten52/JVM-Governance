# 前言

《Java 平台标准版 HotSpot 虚拟机垃圾回收调优指南》介绍了 Java HotSpot 虚拟机（Java HotSpot VM）中包含的垃圾回收方法，并帮助您确定哪种方法最适合您的需求。



## 受众

本文档适用于希望提高应用程序性能的应用程序开发人员和系统管理员，尤其是那些处理大量数据、使用多个线程且事务率较高的应用程序。



## 文档可访问性

有关甲骨文公司在无障碍访问方面的承诺的信息，请访问甲骨文无障碍访问计划网站： `http://www.oracle.com/pls/topic/lookup?ctx=acc&id=docacc` 。

**访问 Oracle 技术支持**

已购买支持服务的 Oracle 客户可通过“我的 Oracle 支持”获得电子支持服务。如需了解相关信息，请访问 `http://www.oracle.com/pls/topic/lookup?ctx=acc&id=info` ；如果您有听力障碍，请访问 `http://www.oracle.com/pls/topic/lookup?ctx=acc&id=trs` 。

## 相关文档

有关更多信息，请参阅以下文档：

- Java 虚拟机技术
  http://docs.oracle.com/javase/8/docs/technotes/guides/vm/index.html`
  
- Java SE HotSpot 概览
  `http://www.oracle.com/technetwork/java/javase/tech/index-jsp-136373.html`

- HotSpot 虚拟机常见问题解答（FAQ）

  `http://www.oracle.com/technetwork/java/hotspotfaq-138619.html`

- 理查德·琼斯（Richard Jones）和拉斐尔·林斯（Rafael Lins），《垃圾回收：自动动态内存管理算法》（Garbage Collection: Algorithms for Automated Dynamic Memory Management），威利父子出版公司（Wiley and Sons）（1996 年），ISBN 0-471-94148-4

## 约定

本文档使用以下文本约定：

| 约定       | 意义                                                         |
| ---------- | ------------------------------------------------------------ |
| **粗体字** | 粗体字表示与某个操作相关的图形用户界面元素，或文本中或词汇表中定义的术语。 |
| *斜体*     | 斜体字表示书名、强调内容或需你提供具体值的占位变量。         |
| `等宽字体` | 等宽字体表示段落中的命令、URL、示例中的代码、屏幕上显示的文本或您输入的文本。 |