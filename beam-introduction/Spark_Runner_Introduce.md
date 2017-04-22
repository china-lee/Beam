# Spark Beam Runner (Spark-Runner)


## 简介

Spark-Runner允许用户在Apache Spark上执行使用Apache Beam API编写的数据管道。Spark Runner允许在Spark引擎之上执行批处理和流式计算数据管道。

## 概述

### 特征集
- ParDo
- GroupByKey
- Combine
- Windowing
- Flatten
- View
- Side inputs/outputs
- Encoding

## 容错

Spark runner提供的容错机制与Apache Spark一致

## 监控

Spark runner支持通过Beam Aggregators（实现了Spark的 Aggregators/聚合器）支持用户自定义的计数器。这个Aggregators/聚合器（由管道作者定义）和Spark内部的度量机制通过Spark的Metrics System/度量系统给出度量报告。



## Beam 模型支持

### 批处理

Spark runner通过Spark的弹性数据集提供对Beam批处理编程模型的完全支持。

### 流式计算

对Beam流式管道编程模型的完全支持正在开发中。你可以通过订阅邮件列表来跟踪进展。

### 问题追踪

参考Beam JIRA (runner-spark)


## 开始

要获取Spark Runner的最新版本，请首先克隆下面的Beam仓库： git clone https://github.com/apache/beam

然后切换到新创建的git目录，运行下面的Maven命令构建Apache Beam:

<code>
cd beam
mvn clean install -DskipTests
</code>


这样Apache Beam和Spark Runner就安装到了你的本地maven仓库中。

如果想在本地模式的Spark实例中，使用默认选项运行一个Beam管道，代码如下：

<code>
Pipeline p = <logic for pipeline creation >
PipelineResult result = p.run();
result.waitUntilFinish();
</code>


如果想要创建一个管道运行器，运行在一个自定义Spark master url的Spark集群中，可以这么做：

<code>
SparkPipelineOptions options = PipelineOptionsFactory.as(SparkPipelineOptions.class);
options.setSparkMaster("spark://host:port");
Pipeline p = <logic for pipeline creation >
PipelineResult result = p.run();
result.waitUntilFinish();
</code>

### 单词计数的例子

首先下载一个文本文档作为输入：

<code>
curl http://www.gutenberg.org/cache/epub/1128/pg1128.txt > /tmp/kinglear.txt
</code>

切换到Spark runner目录：

<code>
cd runners/spark
</code>

然后在Spark本地模式实例下运行单词计数例子：

<code>
mvn exec:exec -DmainClass=org.apache.beam.runners.spark.examples.WordCount \
      -Dinput=/tmp/kinglear.txt -Doutput=/tmp/out -Drunner=SparkRunner \
      -DsparkMaster=local
</code>

查看运行结果:

<code>
head /tmp/out-00000-of-00001
</code>

注意：使用mvn exec:exec运行例子，仅支持工作在Spark本地模式下。可以查看下面的章节，了解如何在集群模式下运行例子。


### 运行在 Spark 集群模式下

Spark Beam管道可以使用spark-submit命令运行在集群模式下。

TBD： 本地hdfs的支持正在开发中 (当前阻塞于 BEAM-59).






