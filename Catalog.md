# Spark编程指南
对应md：rdd-programming-guide.md

[对应网址](https://spark.apache.org/docs/2.3.0/rdd-programming-guide.html)

包含内容：
* 概述
* Spark 依赖
* 初始化 Spark
    - 使用 Shell
* 弹性分布式数据集 (RDDs)
    - 并行集合
    - 外部 Datasets（数据集）
    - RDD 操作
        - 基础
        - 传递 Functions（函数）给 Spark
        - 理解闭包

        - 与 Key-Value Pairs 一起使用
        - Transformations（转换）
         - Actions（动作）
         - Shuffle 操作
             - Background（幕后）
              - 性能影响
    - RDD Persistence（持久化）
        - 如何选择存储级别 ?
        - 删除数据
* 共享变量
    - 广播变量
    - Accumulators（累加器）
* 部署应用到集群中
* 从 Java / Scala 启动 Spark jobs
* 单元测试
* 快速链接

# Spark Streaming

对应md:treaming-programming-guide.md

[对应网址](https://spark.apache.org/docs/2.3.0/streaming-programming-guide.html)

包含内容：
* 概述
* 一个入门示例
* 基础概念
    - 依赖
    - 初始化 StreamingContext
    - Discretized Streams (DStreams)（离散化流）
    - Input DStreams 和 Receivers（接收器）
    - DStreams 上的 Transformations（转换）
    - DStreams 上的输出操作
    - DataFrame 和 SQL 操作
    - MLlib 操作
    - 缓存 / 持久性
    - Checkpointing
    - Accumulators, Broadcast 变量, 和 Checkpoint
    - 应用程序部署
    - Monitoring Applications （监控应用程序）
* Performance Tuning （性能调优）
    - Reducing the Batch Processing Times （减少批处理时间）
    - Setting the Right Batch Interval （设置正确的批次间隔）
    - Memory Tuning （内存调优）
* Fault-tolerance Semantics （容错语义）
* 快速链接


# DataFrame DataSet and SQL

对应md：sql-programming-guide.md

[对应网址](https://spark.apache.org/docs/2.3.0/sql-programming-guide.html)

包含内容：
* Overview
    - SQL
    - Datasets and DataFrames
* 开始入门
    - 起始点: SparkSession
    - 创建 DataFrames
    - 无类型的Dataset操作 (aka DataFrame 操作)
    - Running SQL Queries Programmatically
    - 全局临时视图
    - 创建Datasets
    - RDD的互操作性
        - 使用反射推断Schema
        - 以编程的方式指定Schema
    - Aggregations
        - Untyped User-Defined Aggregate Functions
        - Type-Safe User-Defined Aggregate Functions
* Data Sources （数据源）
    - Generic Load/Save Functions （通用 加载/保存 功能）
        - Manually Specifying Options （手动指定选项）
        - Run SQL on files directly （直接在文件上运行 SQL）
        - Save Modes （保存模式）
        - Saving to Persistent Tables （保存到持久表）
        - Bucketing, Sorting and Partitioning （分桶, 排序和分区）
    - Parquet Files
        - Loading Data Programmatically （以编程的方式加载数据）
        - Partition Discovery （分区发现）
        - Schema Merging （模式合并）
        - Hive metastore Parquet table conversion （Hive metastore Parquet table 转换）
            - Hive/Parquet Schema Reconciliation
            - Metadata Refreshing （元数据刷新）
        - Configuration （配置）
    - JSON Datasets （JSON 数据集）
    - Hive 表
        - 指定 Hive 表的存储格式
        - 与不同版本的 Hive Metastore 进行交互
    - JDBC 连接其它数据库
    - 故障排除
* 性能调优
    - 在内存中缓存数据
    - 其他配置选项
* 分布式 SQL 引擎
    - 运行 Thrift JDBC/ODBC 服务器
    - 运行 Spark SQL CLI
* 迁移指南
    - 从 Spark SQL 2.1 升级到 2.2
    - 从 Spark SQL 2.0 升级到 2.1
    - 从 Spark SQL 1.6 升级到 2.0
    - 从 Spark SQL 1.5 升级到 1.6
    - 从 Spark SQL 1.4 升级到 1.5
    - 从 Spark SQL 1.3 升级到 1.4
        - DataFrame data reader/writer interface
        - DataFrame.groupBy 保留 grouping columns（分组的列）
        - DataFrame.withColumn 上的行为更改
    - 从 Spark SQL 1.0-1.2 升级到 1.3
        - 重命名 DataFrame 的 SchemaRDD
        - Java 和 Scala APIs 的统一
        - 隔离隐式转换和删除 dsl 包（仅Scala）
        - 针对 DataType 删除在 org.apache.spark.sql 包中的一些类型别名（仅限于 Scala）
        - UDF 注册迁移到 sqlContext.udf 中 (Java & Scala)
        - Python DataTypes 不再是 Singletons（单例的）
    - 与 Apache Hive 的兼容
        - 在现有的 Hive Warehouses 中部署
        - 所支持的 Hive 特性
        - 未支持的 Hive 函数
* 参考
    - 数据类型
    - NaN Semantics


# Structured Streaming
对应md:structured-streaming-programming-guide.md

[对应网址](https://spark.apache.org/docs/2.3.0/structured-streaming-programming-guide.html)

包含内容：
* 概述
* 快速示例
* Programming Model （编程模型）
    - 基本概念
    - 处理 Event-time 和延迟数据
    - 容错语义
* API 使用 Datasets 和 DataFrames
    - 创建 streaming DataFrames 和 streaming Datasets
        - Input Sources （输入源）
        - streaming DataFrames/Datasets 的模式接口和分区
    - streaming DataFrames/Datasets 上的操作
        - 基础操作 - Selection, Projection, Aggregation
        - Window Operations on Event Time （事件时间窗口操作）
        - 处理 Late Data （迟到数据）和 Watermarking （水印）
        - Join 操作
        - Streaming Deduplication （Streaming 去重）
        - Arbitrary Stateful Operations （任意有状态的操作）
        - 不支持的操作
    - 开始 Streaming Queries
        - Output Modes （输出模式）
        - Output Sinks （输出接收器）
        - 使用 Foreach
    - 管理 Streaming Queries
    - 监控 Streaming Queries
        - Interactive APIs
        - Asynchronous API
    - Recovering from Failures with Checkpointing （从检查点恢复故障）
* 从这里去哪儿


# GraphX

对应md：graphx-programming-guide.md
[对应网址](https://spark.apache.org/docs/2.3.0/graphx-programming-guide.html)

包含内容：
* 概述
* 入门
* 属性 Graph
    - 示例属性 Graph
* Graph 运算符
    - 运算符的汇总表
    - Property 运算符
    - Structural 运算符
    - Join 运算符
    - 邻域聚合
        - 聚合消息 (aggregateMessages)
        - Map Reduce Triplets Transition Guide (Legacy)
        - 计算级别信息
        - 收集相邻点
    - Caching and Uncaching
* Pregel API
* Graph 建造者
* Vertex and Edge RDDs
    - VertexRDDs
    - EdgeRDDs
* 优化表示
* Graph 算法
    - PageRank
    - 连接组件
    - Triangle 计数
* 示例

# Spark Configuration

对应md：configuration.md

[对应网址](https://spark.apache.org/docs/2.3.0/configuration.html)

包含内容：
* Spark 属性
    - 动态加载 Spark 属性
    - 查看 Spark 属性
    - 可用属性
        - 应用程序属性
        - 运行环境
        - Shuffle Behavior （Shuffle 行为）
        - Spark UI
        - Compression and Serialization （压缩和序列化）
        - Memory Management （内存管理）
        - Execution Behavior （执行行为）
        - Networking （网络）
        - Scheduling （调度）
        - Dynamic Allocation （动态分配）
        - Security （安全）
        - TLS / SSL
        - Spark SQL
        - Spark Streaming
        - SparkR
        - GraphX
        - Deploy （部署）
        - Cluster Managers （集群管理器）
            - YARN
            - Mesos
            - Standalone Mode
* Environment Variables （环境变量）
* Configuring Logging （配置 Logging）
* Overriding configuration directory （覆盖配置目录）
* Inheriting Hadoop Cluster Configuration （继承 Hadoop 集群配置）

# Monitoring and Instrumentation
对应md：monitoring.md

[对应网址](https://spark.apache.org/docs/2.3.0/monitoring.html)

包含内容
* web界面
* 事后查看
* 环境变量
* Spark配置选项
* REST API
* Metrics



# Tuning Spark
对应md：tuning.md

[对应网址](https://spark.apache.org/docs/2.3.0/tuning.html)

包含内容：
* 数据序列化
* 内存调优
    - 内存管理概述
    - 确定内存消耗
    - 调整数据结构
    - 序列化 RDD 存储
    - 垃圾收集调整
* 其他注意事项
    - 并行度水平
    - 减少任务的内存使用
    - 广播大的变量
    - 数据本地化
* 概要

# Job Scheduling
对应md：job-scheduling.md

[对应网址](https://spark.apache.org/docs/2.3.0/job-scheduling.html)

包含内容：
* 概述
* 跨应用调度
    - 动态资源分配
        - 配置和部署
        - 资源分配策略
            - 请求策略
            - 移除策略
        - 优雅的关闭Executor(执行器)
* 应用内调度
    - 公平调度资源池
    - 资源池默认行为
    - 配置资源池属性


# Spark R

# ML  MLlib

# Yarn