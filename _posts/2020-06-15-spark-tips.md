---
layout: post
title: spark学习笔记
date: 2020-06-12 11:30:00 +0800
cacategory: spark
author: GIGI WANG
tags: spark;hadoop
---

# Spark

> * [Apache Spark - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/Apache_Spark)
> * [Spark 学习: spark 原理简述 - 知乎](https://zhuanlan.zhihu.com/p/34436165)
> * [翻译文档](https://spark-reference-doc-cn.readthedocs.io/zh_CN/latest/programming-guide/sql-guide.html)
> * [官方文档](https://spark.apache.org/docs/latest/)


##简述

**Apache Spark**是一个[开源](/wiki/%E5%BC%80%E6%BA%90 "开源")集群运算框架，最初是由加州大学柏克莱分校AMPLab所开发。相对于[Hadoop](/wiki/Apache_Hadoop "Apache Hadoop")的[MapReduce](/wiki/MapReduce "MapReduce")会在运行完工作后将中介数据存放到磁盘中，Spark使用了存储器内运算技术，能在数据尚未写入硬盘时即在存储器内分析运算。Spark在存储器内运行程序的运算速度能做到比Hadoop MapReduce的运算速度快上100倍，即便是运行程序于硬盘时，Spark也能快上10倍速度。[\[1\]](#cite_note-1)Spark允许用户将数据加载至集群存储器，并多次对其进行查询，非常适合用于[机器学习](/wiki/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0 "机器学习")算法。

### Hadoop 和 Spark 的关系

 * Hadoop

   Google 在 2003 年和 2004 年先后发表了 Google 文件系统 GFS 和 MapReduce 编程模型论文. 基于这两篇开源文档,2006 年 Nutch 项目子项目之一的 Hadoop 实现了两个强有力的开源产品:HDFS 和 MapReduce. Hadoop 成为了典型的大数据批量处理架构,由 HDFS 负责静态数据的存储,并通过 MapReduce 将计算逻辑分配到各数据节点进行数据计算和价值发现.之后以 HDFS 和 MapReduce 为基础建立了很多项目,形成了 Hadoop 生态圈.

* Spark

  Spark 则是UC Berkeley AMP lab 所开源的类Hadoop MapReduce的`通用并行框架`, 专门用于大数据量下的迭代式计算.是为了跟 Hadoop 配合而开发出来的,不是为了取代 Hadoop.
 Spark 运算比 Hadoop 的 MapReduce 框架快的原因是因为 Hadoop 在一次 MapReduce 运算之后,会将数据的运算结果从内存写入到磁盘中,第二次 Mapredue 运算时在从磁盘中读取数据,所以其瓶颈在2次运算间的多余 IO 消耗. Spark 则是将数据一直缓存在内存中,直到计算得到最后的结果,再将结果写入到磁盘,所以多次运算的情况下, Spark 是比较快的. 其优化了迭代式工作负载.

![](https://pic3.zhimg.com/80/v2-351cc2f582f83199f16e1a23fd22bc1a_1440w.jpg)

Spark 的主要特点还包括:

- (1)提供 Cache 机制来支持需要反复迭代计算或者多次数据共享,减少数据读取的 IO 开销;
- (2)提供了一套支持 DAG 图的分布式并行计算的编程框架,减少多次计算之间中间结果写到 Hdfs 的开销;
- (3)使用多线程池模型减少 Task 启动开稍, shuffle 过程中避免不必要的 sort 操作并减少磁盘 IO 操作。(Hadoop 的 Map 和 reduce 之间的 shuffle 需要 sort)

## Spark计算框架

伯克利大学将 Spark 的整个生态系统成为 伯克利数据分析栈(BDAS),在核心框架 Spark 的基础上,主要提供四个范畴的计算框架:


![](https://pic4.zhimg.com/80/v2-da06e65a91307133d6c5e3003cf8f40b_1440w.jpg)

- Spark SQL: 提供了类 SQL 的查询,返回 Spark\-DataFrame 的数据结构(类似 Hive)
- Spark Streaming: 流式计算,主要用于处理线上实时时序数据(类似 storm)
- MLlib: 提供机器学习的各种模型和调优
- GraphX: 提供基于图的算法,如 PageRank


## 系统架构

Spark遵循主从架构。它的集群由一个主服务器和多个从服务器组成。
Spark架构依赖于两个抽象：

* 弹性分布式数据集(RDD)
* 有向无环图(DAG)

![](https://www.yiibai.com/uploads/article/2019/05/17/101257_24026.png)

- 应用程序(Application): 基于Spark的用户程序，包含了一个Driver Program 和集群中多个的Executor；
- 驱动(Driver): 运行Application的main()函数并且创建SparkContext;
- 执行单元(Executor): 是为某Application运行在Worker Node上的一个进程，该进程负责运行Task，并且负责将数据存在内存或者磁盘上，每个Application都有各自独立的Executors;
- 集群管理程序(Cluster Manager): 在集群上获取资源的外部服务(例如：Local、Mesos或Yarn等集群管理系统)；
- 操作(Operation): 作用于RDD的各种操作分为Transformation和Action.

![](https://pic4.zhimg.com/80/v2-89b33390425ffb8a58238df59d945c3f_1440w.jpg)



>底层详细细节介绍:

```
　　使用spark-submit提交一个Spark作业之后，这个作业就会启动一个对应的Driver进程。根据你使用的部署模式（deploy-mode）不同，Driver进程可能在本地启动，也可能在集群中某个工作节点上启动。而Driver进程要做的第一件事情，就是向集群管理器申请运行Spark作业需要使用的资源，这里的资源指的就是Executor进程。
    YARN集群管理器会根据我们为Spark作业设置的资源参数，在各个工作节点上，启动一定数量的Executor进程，每个Executor进程都占有一定数量的内存和CPU core。
　在申请到了作业执行所需的资源之后，Driver进程就会开始调度和执行我们编写的作业代码。Driver进程会将我们编写的Spark作业代码分拆为多个stage，每个stage执行一部分代码片段，并为每个stage创建一批Task，然后将这些Task分配到各个Executor进程中执行。Task是最小的计算单元，负责执行一模一样的计算逻辑（也就是我们自己编写的某个代码片段），只是每个Task处理的数据不同而已。一个stage的所有Task都执行完毕之后，会在各个节点本地的磁盘文件中写入计算中间结果，然后Driver就会调度运行下一个stage。下一个stage的Task的输入数据就是上一个stage输出的中间结果。如此循环往复，直到将我们自己编写的代码逻辑全部执行完，并且计算完所有的数据，得到我们想要的结果为止。
　Spark是根据shuffle类算子来进行stage的划分。如果我们的代码中执行了某个shuffle类算子（比如reduceByKey、join等），那么就会在该算子处，划分出一个stage界限来。可以大致理解为，shuffle算子执行之前的代码会被划分为一个stage，shuffle算子执行以及之后的代码会被划分为下一个stage。因此一个stage刚开始执行的时候，它的每个Task可能都会从上一个stage的Task所在的节点，去通过网络传输拉取需要自己处理的所有key，然后对拉取到的所有相同的key使用我们自己编写的算子函数执行聚合操作（比如reduceByKey()算子接收的函数）。这个过程就是shuffle。
　当我们在代码中执行了cache/persist等持久化操作时，根据我们选择的持久化级别的不同，每个Task计算出来的数据也会保存到Executor进程的内存或者所在节点的磁盘文件中。
　因此Executor的内存主要分为三块：第一块是让Task执行我们自己编写的代码时使用，默认是占Executor总内存的20%；第二块是让Task通过shuffle过程拉取了上一个stage的Task的输出后，进行聚合等操作时使用，默认也是占Executor总内存的20%；第三块是让RDD持久化时使用，默认占Executor总内存的60%。
　Task的执行速度是跟每个Executor进程的CPU core数量有直接关系的。一个CPU core同一时间只能执行一个线程。而每个Executor进程上分配到的多个Task，都是以每个Task一条线程的方式，多线程并发运行的。如果CPU core数量比较充足，而且分配到的Task数量比较合理，那么通常来说，可以比较快速和高效地执行完这些Task线程。
```

　　在实际编程中,我们不需关心以上调度细节.只需使用 Spark 提供的指定语言的编程接口调用相应的 API 即可.
　　**在 Spark API 中**, 一个 应用(Application) 对应一个 SparkContext 的实例。一个 应用 可以用于单个 Job，或者分开的多个 Job 的 session，或者响应请求的长时间生存的服务器。与 MapReduce 不同的是，一个 应用 的进程（我们称之为 Executor)，会一直在集群上运行，即使当时没有 Job 在上面运行。
　　而调用一个Spark内部的 Action 会产生一个 Spark job 来完成它。 为了确定这些job实际的内容，Spark 检查 RDD 的DAG再计算出执行 plan 。这个 plan 以最远端的 RDD 为起点（最远端指的是对外没有依赖的 RDD 或者 数据已经缓存下来的 RDD），产生结果 RDD 的 Action 为结束 。并根据是否发生 shuffle 划分 DAG 的 stage.


## RDD

RDD(弹性分布式数据集)是Spark的核心抽象。它是一组元素，在集群的节点之间进行分区，以便我们可以对其执行各种并行操作。

### Transformation 和 Action

Spark 的设计思想中,为了减少网络及磁盘 IO 开销,需要设计出一种新的容错方式,于是才诞生了新的数据结构 RDD. RDD 是一种只读的数据块,可以从外部数据转换而来,你可以对RDD 进行函数操作(Operation),包括 Transformation 和 Action. 在这里只读表示当你对一个 RDD 进行了操作,那么结果将会是一个新的 RDD, 这种情况放在代码里,假设变换前后都是使用同一个变量表示这一 RDD,RDD 里面的数据并不是真实的数据,而是一些元数据信息,记录了该 RDD 是通过哪些 Transformation 得到的,在计算机中使用 lineage 来表示这种血缘结构,lineage 形成一个有向无环图 DAG, 整个计算过程中,将不需要将中间结果落地到 HDFS 进行容错,加入某个节点出错,则只需要通过 lineage 关系重新计算即可.

![](https://pic4.zhimg.com/80/v2-dc154708ba16034ed619bb9755fe27bb_1440w.jpg)

> Transformation 操作不是马上提交 Spark 集群执行的,Spark 在遇到 Transformation 操作时只会记录需要这样的操作,并不会去执行,需要等到有 Action 操作的时候才会真正启动计算过程进行计算.针对每个 Action,Spark 会生成一个 Job, 从数据的创建开始,经过 Transformation, 结尾是 Action 操作.这些操作对应形成一个有向无环图(DAG),形成 DAG 的先决条件是最后的函数操作是一个Action

### RDD 主要特点

- 1.它是在集群节点上的不可变的、已分区的集合对象;
- 2.通过并行转换的方式来创建(如 Map、 filter、join 等);
- 3.失败自动重建;
- 4.可以控制存储级别(内存、磁盘等)来进行重用;
- 5.必须是可序列化的;
- 6.是静态类型的(只读)。

### 创建RDD

有两种方法可以用来创建RDD：

* 并行化驱动程序中的现有数据 `parallelize`
* 引用外部存储系统中的数据集，例如：共享文件系统，HDFS，HBase或提供Hadoop InputFormat的数据源

## Spark SQL & DataFrame

Spark SQL是Spark用来处理结构化数据的一个模块，它提供了两个编程抽象分别叫做DataFrame和DataSet，它们用于作为分布式SQL查询引擎。
![](https://upload-images.jianshu.io/upload_images/468490-712bc1c76bb0f370.png?imageMogr2/auto-orient/strip|imageView2/2/w/432/format/webp)

### DataFrame

>DataFrame（表）= Schema（表结构） + Data（表数据）

DataFrame是组织成命名列的数据集。它在概念上等同于关系数据库中的表，但在底层具有更丰富的优化
DataFrame相比RDD多了数据的结构信息，即schema。RDD是分布式的对象的集合。DataFrame是分布式的Row对象的集合。DataFrame除了提供了比RDD更丰富的算子以外，更重要的特点是提升执行效率、减少数据读取以及执行计划的优化

创建DataFrame

* 方式1：使用case class定义表
* 方式2：使用SparkSession对象创建DataFrame  `spark.createDataFrame`
* 方式3：直接读取格式化的文件（json,csv

## shuffle 和 stage

**shuffle 是划分 DAG 中 stage 的标识,同时影响 Spark 执行速度的关键步骤**.
　　RDD 的 Transformation 函数中,又分为窄依赖(narrow dependency)和宽依赖(wide dependency)的操作.窄依赖跟宽依赖的区别是**是否发生 shuffle(洗牌) 操作**.宽依赖会发生 shuffle 操作. 窄依赖是子 RDD的各个分片(partition)不依赖于其他分片,能够独立计算得到结果,宽依赖指子 RDD 的各个分片会依赖于父RDD 的多个分片,所以会造成父 RDD 的各个分片在集群中重新分片, 看如下两个示例:

```scala
// Map: "cat" -> c, cat
val rdd1 = rdd.Map(x => (x.charAt(0), x))
// groupby same key and count
val rdd2 = rdd1.groupBy(x => x._1).
                Map(x => (x._1, x._2.toList.length))
```

　　第一个 Map 操作将 RDD 里的各个元素进行映射, RDD 的各个数据元素之间不存在依赖,可以在集群的各个内存中独立计算,也就是**并行化**,第二个 groupby 之后的 Map 操作,为了计算相同 key 下的元素个数,需要把相同 key 的元素聚集到同一个 partition 下,所以造成了数据在**内存中的重新分布**,即 shuffle 操作.**shuffle 操作是 spark 中最耗时的操作,应尽量避免不必要的 shuffle**.
　　宽依赖主要有两个过程: shuffle write 和 shuffle fetch. 类似 Hadoop 的 Map 和 Reduce 阶段.shuffle write 将 ShuffleMapTask 任务产生的中间结果缓存到内存中, shuffle fetch 获得 ShuffleMapTask 缓存的中间结果进行 ShuffleReduceTask 计算,**这个过程容易造成OutOfMemory**.

![](https://pic3.zhimg.com/v2-536754e21c138eea8e7aea37a7cd2afe_b.jpg)


## 性能优化

### 缓存

　　Spark中对于一个RDD执行多次算子(函数操作)的默认原理是这样的：每次你对一个RDD执行一个算子操作时，都会重新从源头处计算一遍，计算出那个RDD来，然后再对这个RDD执行你的算子操作。这种方式的性能是很差的。
　　**首先要认识到的是**, .Spark 本身就是一个基于内存的迭代式计算,**所以如果程序从头到尾只有一个 Action 操作且子 RDD 只依赖于一个父RDD 的话,就不需要使用 cache 这个机制**, RDD 会在内存中一直从头计算到尾,最后才根据你的 Action 操作返回一个值或者保存到相应的磁盘中.需要 cache 的是当存在多个 Action 操作或者依赖于多个 RDD 的时候, 可以在那之前缓存RDD.

### shuffle优化

- 当进行联合的规约操作时，避免使用 groupByKey
- 当输入和输入的类型不一致时，避免使用 reduceByKey
- 生成新列的时候,避免使用单独生成一列再 join 回来的方式,而是直接在数据上生成.
- 当需要对两个 RDD 使用 join 的时候,如果其中一个数据集特别小,小到能塞到每个 Executor 单独的内存中的时候,可以不使用 join, 使用 broadcast 操作将小 RDD 复制广播到每个 Executor 的内存里 join.

### 资源参数调优

![](https://pic4.zhimg.com/80/v2-3d2053cea9cdf52a700de377fe16d11f_1440w.jpg)