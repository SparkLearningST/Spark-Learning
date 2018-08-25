# 跨应用调度
## 动态资源分配
Spark提供了一种机制根据你的负载量动态地调整程序所占有的资源 。这意味着，你的应用会在不需要资源的时候把资源返回给集群，需要时再向集群重新申请资源。这一特性在多个应用共享Spark集群资源的情况下特别有用.

这个特性默认是禁止的，但是在所有的粗粒度集群管理器上都是可用的,如standalone mode, YARN mode, and Mesos coarse-grained mode.

### 配置和部署
要使用这一特性有两个要求。首先，必须设置spark.dynamicAllocation.enabled为true。其次，你必须在每个节点上启动external shuffle service,也就是将spark.shuffle.service.enabled设为true。external shuffle service（外部shuffle服务） 的目的是允许executor从移除的时候，能够保留这些executor输出的shuffle文件(本文后续有更新的描述). 启用external shuffle service 的方式根据集群管理器的不同而有所不同：

在 standalone 模式,只需要简单地设置spark.shuffle.service.enabled为true即可。

在Mesos模式,需要在各个节点上运行$SPARK_HOME/sbin/start-mesos-shuffle-service.sh 并设置 spark.shuffle.service.enabled为true即可. 例如, 你可以在Marathon来启用这一功能。

在YARN模式下，需要按以下步骤在各个NodeManager上启动： [here](https://spark.apache.org/docs/2.3.1/running-on-yarn.html#configuring-the-external-shuffle-service).

所有其它的配置都是可选的，在spark.dynamicAllocation.和spark.shuffle.service.这两个命明空间下有更加详细的介绍 configurations page.

### 资源分配策略
总体上来说,Spark程序应该放弃那些不再需要的节点，当需要的时候再重新申请。但是没有一个固定的方法,可以预测一个excutor在后续的时间是否马上会被分配去执行任务，或者一个新分配的executor实际上是空闲的，所以我们需要一个试探性的方法，来决定是否申请或是移除一个executor。

#### 请求策略
一个启用了动态分配的Spark应用当他有等待任务需要调度的时候，允许去申请额外的executor。在这种情况下，必定意味着现在已有的executor已经不足以同时执行所有已提交但是未完成的任务。

Spark以一种循环的方式来请求executor。实际申请的请求会被触发是在有任务等待了spark.dynamicAllocation.schedulerBacklogTimeout秒后首次触发，其后如果等待队列中仍有挂起的任务，则每过spark.dynamicAllocation.sustainedSchedulerBacklogTimeout秒后触发一次申请。另外，每一轮申请的executor的数量以指数形式增长。例如：一个Spark应用可能在首轮申请1个，后续的轮次申请个数可能是2个、4个、8个….。

申请数量呈指数增长主要有两方面的考虑。其一：一个程序在一开始申请资源应该谨慎，要考虑到只需要增加一小部分executor就可以完成任务的情况，和TCP慢启动有些类似。其二：是确保那些确实需要很多资源的任务可以及时获取更多的资源。

#### 移除策略
移除executor的策略就简单得多了。Spark应用会在某个executor空闲超过 spark.dynamicAllocation.executorIdleTimeout秒后将其删除，在大多数情况下，executor的移除条件和申请条件都是互斥的，也就是说，executor在有等待执行任务挂起时，不应该空闲。

### 优雅的关闭Executors
在动态分配模式之前，一个spark executor退出原因可能是执行失败或是相关Spark应用已经退出。这两种情况下，这个executor的所有相关状态都已经不再需要，这个exeecutor可以丢弃掉。但是在动态分配的情况下，executor希望被移除的时候可能这个Spark应用仍然是在运行期,这时候，如果Spark应用尝试去访问该executor存储的状态，就必须重算这一部分数据。因此，Spark需要一种机制，能够优雅的关闭执行器，同时在移除这个executor之前还保留其状态数据。

这种需求对于shuffle操作尤其重要。在shuffle过程中，Spark executor首先将 map 输出写到本地磁盘，同时executor本身又是一个文件服务器以提供给其他executor来获取shuffle的数据。一旦有某些任务执行时间过长，动态分配有可能在整个shuffle结束前移除一个executor，而这些被移除的executor对应的数据将会被重新计算，但这些重算其实是不必要的。

要解决这一问题，就需要用到 external shuffle service ，该服务在 Spark 1.2 引入。该服务在每个节点上都会启动一个不依赖于任何 Spark 应用或executor的独立进程。一旦该服务启用，Spark executor不再从各个executor上获取 shuffle 文件，转而从这个 service 获取。这意味着，任何executor的shuffle输出可以持续地被提供即使超出了这个executor的生命周期。

除了shuffle文件之外，executor也会在磁盘或者内存中缓存数据。一旦executor被移除，其缓存数据将无法访问，为了缓和这种情况，有缓存数据的executor不会被移除，你可以配置这种行为通过spark.dynamicAllocation.cachedExecutorIdleTimeout。在未来的版本中，这个缓存的数据可以通过堆外存储保存，这与通过外部shuffle服务保存shuffle文件的方式类似。

