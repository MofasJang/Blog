---
title: Spark基本架构和原理
copyright: false
date: 2021-10-28 14:35:21
tags: Spark
categories:
- 大数据
- Spark
---
{% note info %}
参考资料：[Spark基本概念快速入门](https://www.jianshu.com/p/e41b18a7e202)，[Spark基础知识（一）---常用组建和RDD](https://zhuanlan.zhihu.com/p/95622176)，[Spark(一): 基本架构及原理](https://www.cnblogs.com/cxxjohnson/p/8909578.html)，[Spark 入门和使用](https://blog.csdn.net/c391183914/article/details/78672555#12-spark%E7%89%B9%E7%82%B9)
{% endnote %}

　　目前，Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含Spark SQL、Spark Streaming、GraphX、MLib、SparkR等子项目，Spark是基于内存计算的大数据并行计算框架。除了**扩展了广泛使用的 MapReduce 计算模型**，而且高效地支持更多计算模式，包括**交互式查询和流处理**。
<!-- more -->

　　**适用场景**：Spark 适用于各种各样原先需要多种不同的分布式平台的场景，包括**批处理、迭代算法、交互式查询、流处理**。通过在一个统一的框架下支持这些不同的计算，Spark 使我们可以简单而低耗地把各种处理流程整合在一起。而这样的组合，在实际的数据分析 过程中是很有意义的。不仅如此，Spark 的这种特性还大大减轻了原先需要对各种平台分 别管理的负担。

### 一、基本架构

![](http://pic.panjiangtao.cn/202110271945_212.png)

#### 1. Spark Core核心和弹性分布式资料集（RDD）

　　Spark Core是整个项目的基础，提供了分布式任务调度，调度和基本的I／O功能，**包含任务调度、内存管理、错误恢复、与存储系统交互**等模块。而其基础的程序抽象则称为弹性分布式资料集（RDD），是一个可以**并行操作、有容错机制的资料集合**。 Spark Core定义RDD的API、操作以及这两者上的动作。RDD可以透过引用外部存储系统的资料集创建（例如：共享文件系统、HDFS、HBase或其他 Hadoop 资料格式的数据源）。或者是通过在现有RDD的转换而创建（比如：map、filter、reduce、join等等）。

#### 2. Spark SQL

　　Spark SQL在Spark核心上带出一种名为**SchemaRDD**的资料抽象化概念，提供结构化和半结构化资料相关的支持。Spark SQL提供了领域特定语言，可使用**Scala、Java或Python**来操纵SchemaRDD。它还支持使用使用命令行界面和**ODBC／JDBC**服务器操作SQL语言。在Spark 1.3版本，SchemaRDD被重命名为DataFrame。

#### 3. Spark Streaming

　　Spark Streaming充分利用Spark核心的快速调度能力来运行**流分析**。它截取小批量的资料并对之运行RDD转换。这种设计使流分析可在同一个引擎内使用同一组为批量分析编写而撰写的应用程序代码。

#### 4. MLlib

　　MLlib是Spark上的**分布式机器学习框架**。Spark分布式存储器式的架构比Hadoop磁盘式的Apache Mahout快上10倍，扩展性甚至比Vowpal Wabbit要好。MLlib可使用许多常见的机器学习和统计算法，简化大规模机器学习时间，其中包括：

* 汇总统计、相关性、分层抽样、假设检定、随机数据生成
* 分类与回归：支持向量机、回归、线性回归、逻辑回归、决策树、朴素贝叶斯
* 协同过滤：ALS
* 分群：k-平均算法
* 维度约减：奇异值分解（SVD），主成分分析（PCA）
* 特征提取和转换：TF-IDF、Word2Vec、StandardScaler
* 最优化：随机梯度下降法（SGD）、L-BFGS

#### 5. GraphX

　　GraphX是Spark上的**分布式图形处理框架**。它提供了一组API，可用于表达图表计算并可以模拟Pregel抽象化。GraphX还对这种抽象化提供了优化运行。

#### 6. 集群管理器

　　Spark 设计为可以高效地在一个计算节点到数千个计算节点之间**伸缩计算**。为了实现这样的要求，同时获得最大灵活性，Spark 支持在各种集群管理器(cluster manager)上运行，包括 **Hadoop YARN、Apache Mesos**，以及 Spark 自带的一个简易调度器，叫作**独立调度器**。

1. Standalone : spark原生的资源管理，由Master负责资源的分配
2. Apache Mesos:与hadoop MR兼容性良好的一种资源调度框架
3. Hadoop Yarn: 主要是指Yarn中的ResourceManager



### 二、基本概念

#### 1. Spark集群

　　一组计算机的集合，每个计算机节点作为独立的计算资源，又可以虚拟出多个具备计算能力的虚拟机，这些虚拟机是集群中的计算单元。Spark的核心模块专注于调度和管理虚拟机之上分布式计算任务的执行，集群中的计算资源则交给Cluster Manager这个角色来管理，Cluster Manager可以为自带的Standalone、或第三方的Yarn和Mesos。
　　Cluster Manager一般采用Master-Slave结构。以Yarn为例，部署ResourceManager服务的节点为**Master**，负责集群中所有计算资源的统一管理和分配；部署NodeManager服务的节点为**Slave**，负责在当前节点创建一个或多个具备独立计算能力的JVM（Java虚拟机）实例，在Spark中，这些节点也叫做**Worker**。
　　另外还有一个**Client**节点的概念，是指用户提交Spark Application时所在的节点。

#### 2. Application

　　用户**自己写的Spark应用程序**，批处理作业的集合。Application的main方法为应用程序的入口，用户通过Spark的API，定义了RDD和对RDD的操作。

#### 3. SparkContext

　　Spark最重要的API，**用户逻辑与Spark集群主要的交互接口**，它会和Cluster Master交互，包括向它申请计算资源等。

#### 4. Driver和Executor
![](http://pic.panjiangtao.cn/202110281440_941.png)

Spark在执行每个Application的过程中会启动**Driver和Executor**两种JVM进程：

- Driver进程为主控进程，负责执行用户Application中的main方法，提交Job，并将Job转化为Task，在各个Executor进程间协调Task的调度。
- 运行在Worker上的Executor进程负责执行Task，并将结果返回给Driver，同时为需要缓存的RDD提供存储功能。
- Cluster Manager：在standalone模式中即为Master主节点，控制整个集群，监控worker。在YARN模式中为资源管理器。
- Worker节点：从节点，负责控制计算节点，启动Executor或者Driver。



#### 5. RDD

　　**RDD**（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据抽象，它代表一个**不可变、可分区、里面的元素可并行计算**的集合。RDD具有数据流模型的特点：**自动容错、位置感知性调度和可伸缩性**。RDD允许用户在执行多个查询时显式地将工作集缓存在内存中，后续的查询能够重用工作集，这极大地提升了查询速度。RDD的原理和组成如下图：

![](http://pic.panjiangtao.cn/202110271956_606.png)

（1）**一组分区（Partition**），即数据集的基本组成单位。对于RDD来说，每个分片都会被一个计算任务处理，并决定并行计算的粒度。用户可以在创建RDD时**指定RDD的分片个数**，如果没有指定，那么就会采用默认值。默认值就是程序所分配到的CPU Core的数目。<u>图中指数据集中的每句话经过第二个函数被分割为单词（由第一个RDD到第二个RDD完成分割）</u>。

（2）一个计算每个分区的函数。Spark中RDD的计算是以分片为单位的，**每个RDD都会实现compute函数以达到这个目的**。compute函数会对迭代器进行复合，不需要保存每次计算的结果。<u>图中表示为上方红色的处理函数</u>。Spark中的计算可以简单抽象为对RDD的**创建、转换和返回操作结果**的过程：

* 创建
  通过**加载外部**物理存储（如HDFS）中的**数据集**，或Application中**定义的对象集合**（如List）来创建。RDD在创建后不可被改变，只可以对其执行下面两种操作。

- 转换（Transformation）
  对已有的RDD中的数据执行计算进行转换，而**产生新的RDD**，在这个过程中有时会产生中间RDD。Spark对于Transformation采用惰性计算机制，遇到Transformation时并不会立即计算结果，而是要等遇到Action时一起执行。
- 行动（Action）
  对已有的RDD中的数据执行计算产生结果，**将结果返回Driver程序**或写入到外部物理存储。在Action过程中同样有可能生成中间RDD。

（3）RDD之间的依赖关系(Dependency)。**RDD的每次转换都会生成一个新的RDD，所以RDD之间就会形成类似于流水线一样的前后依赖关系**。在部分分区数据丢失时，Spark可以通过这个依赖关系重新计算丢失的分区数据，而不是对RDD的所有分区进行重新计算。<u>图中每个RDD经过函数的计算产生新的RDD</u>。

对RDD的Transformation或Action操作，让RDD产生了**父子依赖关系**（事实上，Transformation或Action操作生成的中间RDD也存在依赖关系），这种依赖分为宽依赖和窄依赖两种：

- NarrowDependency (窄依赖)
  parent RDD中的**每个Partition最多被**child RDD中的**一个Partition使用**。让RDD产生窄依赖的操作可以称为窄依赖操作，如map、union。
- WideDependency (或ShuffleDependency，宽依赖）
  parent RDD中的每个Partition被child RDD中的**多个Partition使用**，这时会依据Record的key进行**数据重组**，这个过程即为**Shuffle**（洗牌）。让RDD产生宽依赖的操作可以称为宽依赖操作，如reduceByKey, groupByKey。

（4）一个**Partitioner**，即RDD的分区函数。当前Spark中实现了**两种类型的分区函数，一个是基于哈希的HashPartitioner，另外一个是基于范围的RangePartitioner。**只有对于key-value的RDD，才会有Partitioner，非key-value的RDD的Parititioner的值是None。Partitioner函数不但决定了RDD本身的分区数量，也决定了parent RDD Shuffle输出时的分片区。<u>图中第二个函数就为分区函数</u>。

（5）一个列表，**存储存取每个Partition的优先位置**（preferred location）。对于一个HDFS文件来说，这个列表保存的就是每个Partition所在的块的位置。按照“移动数据不如移动计算”的理念，Spark在进行任务调度的时候，会尽可能地将计算任务分配到其所要处理数据块的存储位置。



#### 5.1 RDD的两个方法

* **Persist**

  　　通过RDD的`persist`方法，可以将RDD的分区数据**持久化在内存或硬盘中**，通过`cache`方法则是**缓存到内存**。这里的persist和cache是一样的机制，只不过cache是使用默认的`MEMORY_ONLY`的存储级别对RDD进行persist，故“缓存”也就是一种“持久化”。
   前面提到，只有触发了一个Action之后，Spark才会提交Job进行真正的计算。所以**RDD只有经过一次Action之后，才能将RDD持久化**，然后在Job间共享，即如果两个Job用到了相同的RDD，那么可以在第一个Job中对这个RDD进行缓存，在第二个Job中就避免了RDD的重新计算。持久化机制使需要访问重复数据的Application运行地更快，是能够**提升Spark运算速度**的一个重要功能。

* **Checkpoint**

  　　调用RDD的`checkpoint`方法，可以将RDD保存到**外部存储中，如硬盘或HDFS**。Spark引入checkpoint机制，是因为持久化的RDD的数据有可能丢失或被替换，checkpoint可以在这时候发挥作用，避免重新计算。
  　　创建checkpoint是在当前Job完成后，由另外一个专门的Job完成：也就是说需要checkpoint的RDD会被计算两次。因此，在使用rdd.checkpoint()的时候，建议加上rdd.cache(),这样第二次运行的Job久不用再去计算该RDD了。

  　　一个Job在开始处理RDD的Partition时，或者更准确点说，在Executor中运行的任务在获取Partition数据时，会**先判断是否被持久化**，在没有命中时**再判断是否保存了checkpoint**，如果没有读取到则会重新计算该Partition。



#### 6. Job

　　在一个Application中，**以Action为划分边界的Spark批处理作业**。前面提到，Spark采用惰性机制，对RDD的创建和转换并不会立即执行，只有在**遇到第一个Action时才会生成一个Job**，然后统一调度执行。一个Job包含N个Transformation和1个Action。

#### 7. Stage

　　一个Job中，**以Shuffle为边界划分出的不同阶段**。每个阶段包含一组可以被串行执行的窄依赖或宽依赖操作：用户提交的计算任务是一个由RDD构成的DAG(有向无环图)，如果RDD在转换的时候需要做Shuffle，那么这个Shuffle的过程就将这个DAG分为了不同的阶段（即Stage）。由于Shuffle的存在，不同的Stage是不能并行计算的，因为后面Stage的计算需要前面Stage的Shuffle的结果。
　　在对Job中的所有操作**划分Stage时，一般会按照倒序进行**，即从Action开始，遇到窄依赖操作，则划分到同一个执行阶段，遇到宽依赖操作，则划分一个新的执行阶段，且新的阶段为之前阶段的parent，然后依次类推递归执行。child Stage需要等待所有的parent Stage执行完之后才可以执行，这时Stage之间根据依赖关系构成了一个大粒度的DAG。
　　在一个Stage内，所有的操作以**串行的Pipeline的方式**，由一组Task完成计算。

* DAGScheduler: 根据Job**构建基于Stage的DAG**（Directed Acyclic Graph有向无环图)，并**提交Stage给TASkScheduler**。 其划分Stage的依据是RDD之间的依赖的关系找出开销最小的调度方法，如下图

  ![](http://pic.panjiangtao.cn/202110272106_289.png)

#### 8. Task

　　对一个Stage之内的RDD进行串行操作的计算任务。**每个Stage由一组并发的Task组成**（即TaskSet），这些Task的**执行逻辑完全相同**，只是**作用于不同的Partition**。一个Stage的总Task的个数由Stage中最后的一个RDD的Partition的个数决定。

　　Spark Driver会根据数据所在的位置分配计算任务，即把所有Task根据其Partition所在的位置分配给相应的Executor，以尽量减少数据的网络传输（这也就是所谓的移动数据不如移动计算）。一个Executor内同一时刻可以并行执行的Task数由`总CPU数／每个Task占用的CPU数`决定，即`spark.executor.cores / spark.task.cpus`。

　　Task分为ShuffleMapTask和ResultTask两种，位于最后一个Stage的Task为ResultTask，其他阶段的属于ShuffleMapTask。

- TaskSedulter: 将TaskSET提交给worker运行，**每个Executor运行什么Task**就是在此处分配的. TaskScheduler维护所有TaskSet，当Executor向Driver发生心跳时，TaskScheduler会根据资源剩余情况分配相应的Task。另外TaskScheduler还维护着所有Task的运行标签，重试失败的Task。下图展示了TaskScheduler的作用

  ![](http://pic.panjiangtao.cn/202110272104_734.png)

#### 上述概念间的层次关系

Job=多个stage，Stage=多个同种task

![](http://pic.panjiangtao.cn/202110272107_230.png)

### 三、Spark基本运行流程

![](http://pic.panjiangtao.cn/202110281417_711.png)

1. 构建Spark Application的运行环境，**启动SparkContext**
2. **SparkContext**向**资源管理器**（可以是Standalone，Mesos，Yarn）**申请运行Executor资源**，并启动StandaloneExecutorbackend，
3. **Executor**向**SparkContext申请Task**
4. SparkContext将**应用程序分发给Executor**
5. SparkContext**构建成DAG图**，将**DAG图分解成Stage**、将**Taskset发送给Task Scheduler**，最后由Task Scheduler**将Task发送给Executor运行**
6. **Task**在Executor上**运行**，运行完释放所有资源

#### Spark运行特点：

1. **每个Application获取专属的executor进程**，该进程在Application期间一直驻留，并以多线程方式运行Task。这种Application隔离机制是有优势的，无论是从调度角度看（每个Driver调度他自己的任务），还是从运行角度看（来自不同Application的Task运行在不同JVM中），当然这样意味着Spark Application不能跨应用程序共享数据，除非将数据写入外部存储系统
2. Spark**与资源管理器无关**，只要能够获取executor进程，并能保持相互通信就可以了
3. 提交SparkContext的**Client应该靠近Worker节点**（运行Executor的节点），最好是在同一个Rack里，因为Spark Application运行过程中SparkContext和Executor之间有大量的信息交换
4. Task采用了**数据本地性和推测执行**的优化机制

### 四、运行模式

- 部署在单机上时，既可以用本地模式运行，也可以用伪分布模式运行，而当以分布式集群的方式部署时，也有众多的运行模式可供选择，这取决于集群的实际情况，底层的资源调度即可以依赖外部资源调度框架，也可以使用Spark内建的**Standalone**模式。
- 对于外部资源调度框架的支持，目前的实现包括相对稳定的**Mesos**模式，以及**hadoop YARN**模式
- **本地模式**：常用于本地开发测试的local cluster模式

#### 1. standalone: 独立集群运行模式

##### （1）结构关系：

![](http://pic.panjiangtao.cn/202110281050_53.png)

- Standalone模式使用Spark**自带的资源调度框架**
- 采用Master/Slaves的典型架构，选用ZooKeeper来实现Master的HA（Highly Available，双机集群系统）
- 该模式主要的节点有Client节点、Master节点和Worker节点。其中**Driver既可以运行在Master节点上中，也可以运行在本地Client端**。当用spark-shell交互式工具提交Spark的Job时，Driver在Master节点上运行；当使用spark-submit工具提交Job或者在Eclips、IDEA等开发平台上使用”new SparkConf.setManager(“spark://master:7077”)”方式运行Spark任务时，Driver是运行在本地Client端上的

##### （2）运行过程

![](http://pic.panjiangtao.cn/202110281058_435.png)

1. SparkContext连接到Master，向**Master注册并申请资源**（CPU Core 和Memory）
2. Master根据SparkContext的资源申请要求和Worker心跳周期内报告的信息决定在哪个Worker上分配资源，然后**在该Worker上获取资源**，然后启动StandaloneExecutorBackend；
3. StandaloneExecutorBackend向**SparkContext注册**；
4. SparkContext将Applicaiton代码发送给StandaloneExecutorBackend；并且SparkContext解析Applicaiton代码，**构建DAG图**，并提交给DAG Scheduler分解成Stage（当碰到Action操作时，就会催生Job；每个Job中含有1个或多个Stage，Stage一般在获取外部数据和shuffle之前产生），然后以Stage（或者称为TaskSet）提交给Task Scheduler，Task Scheduler负责**将Task分配到相应的Worker**，最后提交给StandaloneExecutorBackend执行；
5. StandaloneExecutorBackend会建立Executor线程池，开始执行Task，并向SparkContext**报告Task状态**，直至Task完成
6. 所有Task完成后，SparkContext向Master**注销，释放资源**

#### 2. hadoop YARN模式

hadoop YARN模式根据Driver在集群中的位置分为两种模式：一种是YARN-Client模式，另一种是YARN-Cluster模式.

##### (1)Yarn框架流程

　　任何框架与*YARN*的结合，都必须遵循*YARN*的开发模式。在分析*Spark on YARN*的实现细节之前，有必要先分析一下*YARN*框架的一些基本原理。Yarn框架的基本流程如下:

![](http://pic.panjiangtao.cn/202110281120_36.png)

　　其中，ResourceManager负责将集群的**资源分配**给各个应用使用，而**资源分配和调度的基本单位是Container**，其中封装了机器资源，如内存、CPU、磁盘和网络等，每个任务会被分配一个Container，该任务只能在该Container中执行，并使用该Container封装的资源。**NodeManager**是一个个的计算节点，主要**负责启动Application所需的Container，监控资源**（内存、CPU、磁盘和网络等）的使用情况并将之汇报给ResourceManager。ResourceManager与NodeManagers共同组成整个数据计算框架，ApplicationMaster与具体的Application相关，主要负责同ResourceManager协商以获取合适的Container，并跟踪这些Container的状态和监控其进度。

##### (2)YARN-Client模式

　　Driver在客户端本地运行，这种模式可以使得Spark Application和客户端进行交互，因为Driver在客户端，所以可以通过webUI访问Driver的状态，默认是http://hadoop1:4040访问，而YARN通过http:// hadoop1:8088访问。工作流程：

![](http://pic.panjiangtao.cn/202110281115_159.png)

1. Spark Yarn Client向YARN的ResourceManager**申请启动Application Master**。同时在**SparkContent初始化**中将创建DAGScheduler和TASKScheduler等，由于我们选择的是Yarn-Client模式，程序会选择YarnClientClusterScheduler和YarnClientSchedulerBackend
2. ResourceManager收到请求后，在集群中选择一个NodeManager，**为该应用程序分配第一个Container**，要求它在这个Container中**启动应用程序的ApplicationMaster**，与YARN-Cluster区别的是在该ApplicationMaster不运行SparkContext，只与SparkContext进行联系进行资源的分派
3. Client中的SparkContext初始化完毕后，与ApplicationMaster建立通讯，向ResourceManager注册，根据任务信息向ResourceManager**申请资源（Container）**
4. 一旦ApplicationMaster申请到资源（也就是Container）后，便与对应的NodeManager通信，要求它在获得的Container中**启动CoarseGrainedExecutorBackend**，CoarseGrainedExecutorBackend启动后会向Client中的SparkContext**注册并申请Task**
5. client中的SparkContext**分配Task给CoarseGrainedExecutorBackend执行**，CoarseGrainedExecutorBackend**运行Task并向Driver汇报运行的状态和进度**，以让Client随时掌握各个任务的运行状态，从而可以在任务失败时重新启动任务
6. 应用程序运行完成后，Client的SparkContext向ResourceManager申请**注销并关闭自己**

##### (3)YARN-Cluster模式

　　在YARN-Cluster模式中，当用户向YARN中提交一个应用程序后，YARN将分两个阶段运行该应用程序：第一个阶段是把Spark的Driver作为一个ApplicationMaster在YARN集群中先启动；第二个阶段是由ApplicationMaster创建应用程序，然后为它向ResourceManager申请资源，并启动Executor来运行Task，同时监控它的整个运行过程，直到运行完成。

![](http://pic.panjiangtao.cn/202110281140_69.png)

1. Spark Yarn Client向YARN中**提交应用程序**，包括ApplicationMaster程序、启动ApplicationMaster的命令、需要在Executor中运行的程序等
2. ResourceManager收到请求后，在集群中选择一个NodeManager，为该应用程序分配第一个Container，要求它在这个Container中**启动应用程序的ApplicationMaster**，其中ApplicationMaster进行SparkContext等的初始化
3. ApplicationMaster向ResourceManager注册，这样用户可以直接通过ResourceManage查看应用程序的运行状态，然后它将采用轮询的方式通过RPC协议**为各个任务申请资源（也就是Container）**，并监控它们的运行状态直到运行结束
4. 一旦ApplicationMaster申请到资源后，便与对应的NodeManager通信，要求它在获得的Container中**启动CoarseGrainedExecutorBackend**，CoarseGrainedExecutorBackend启动后会向ApplicationMaster中的SparkContext**注册并申请Task**。这一点和Standalone模式一样，只不过SparkContext在Spark Application中初始化时，使用CoarseGrainedSchedulerBackend配合YarnClusterScheduler**进行任务的调度**，其中YarnClusterScheduler只是对TaskSchedulerImpl的一个简单包装，增加了对Executor的等待逻辑等
5. ApplicationMaster中的SparkContext分配Task给CoarseGrainedExecutorBackend执行，CoarseGrainedExecutorBackend运行Task并向ApplicationMaster汇报运行的状态和进度，以让**ApplicationMaster随时掌握各个任务的运行状态**，从而可以在任务失败时重新启动任务
6. 应用程序运行完成后，**ApplicationMaster**向ResourceManager**申请注销并关闭自己**。

#####  (4)SYARN-Client模式和YARN-Cluster模式区别

　　理解YARN-Client和YARN-Cluster深层次的区别之前先清楚一个概念：Application Master。在YARN中，每个Application实例都有一个ApplicationMaster进程，它是Application启动的第一个容器。它负责和ResourceManager打交道并请求资源，获取资源之后告诉NodeManager为其启动Container。从深层次的含义讲YARN-Cluster和YARN-Client模式的区别其实就是ApplicationMaster进程的区别。

* YARN-Cluster模式下，**Driver**运行在AM(Application Master)中，它负责**向YARN申请资源**，并监督作业的运行状况。**当用户提交了作业之后，就可以关掉Client**，作业会继续在YARN上运行，因而YARN-Cluster模式不适合运行交互类型的作业；
* YARN-Client模式下，Application Master仅仅向YARN请求Executor，**Client会和请求的Container通信来调度他们工作**，也就是说Client不能离开。



#### 3.Mesos模式

　　这是很多公司采用的模式，官方推荐这种模式（当然，原因之一是血缘关系）。正是由于Spark开发之初就考虑到支持Mesos，因此，目前而言，Spark运行在Mesos上会比运行在YARN上更加灵活，更加自然。目前在Spark On Mesos环境中，用户可选择两种调度模式之一运行自己的应用程序（可参考Andrew Xia的“Mesos Scheduling Mode on Spark”）：

![](http://pic.panjiangtao.cn/202110281428_718.png)

##### 1. 粗粒度模式（Coarse-grained Mode）

​        每个应用程序的运行环境由一个Dirver和若干个Executor组成，其中，每个Executor占用若干资源，内部可运行多个Task（对应多少个“slot”）。应用程序的各个任务正式运行之前，需要将运行环境中的资源全部申请好，且运行过程中要一直占用这些资源，即使不用，最后程序运行结束后，回收这些资源。举个例子，比如你提交应用程序时，指定使用5个executor运行你的应用程序，每个executor占用5GB内存和5个CPU，每个executor内部设置了5个slot，则Mesos需要先为executor分配资源并启动它们，之后开始调度任务。另外，在程序运行过程中，mesos的master和slave并不知道executor内部各个task的运行情况，executor直接将任务状态通过内部的通信机制汇报给Driver，从一定程度上可以认为，每个应用程序利用mesos搭建了一个虚拟集群自己使用。

##### 2. 细粒度模式（Fine-grained Mode）

​        鉴于粗粒度模式会造成大量资源浪费，Spark On Mesos还提供了另外一种调度模式：细粒度模式，这种模式类似于现在的云计算，思想是按需分配。与粗粒度模式一样，应用程序启动时，先会启动executor，但每个executor占用资源仅仅是自己运行所需的资源，不需要考虑将来要运行的任务，之后，mesos会为每个executor动态分配资源，每分配一些，便可以运行一个新任务，单个Task运行完之后可以马上释放对应的资源。每个Task会汇报状态给Mesos slave和Mesos Master，便于更加细粒度管理和容错，这种调度模式类似于MapReduce调度模式，每个Task完全独立，优点是便于资源控制和隔离，但缺点也很明显，短作业运行延迟大。

