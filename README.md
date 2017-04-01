# Beam

Apache Beam是用于定义批处理和流数据并行处理流水线的统一模型，以及一组用于构建管道的特定于语言的SDK，用于在分布式处理后端执行它们的Runners，包括Apache Apex，Apache Flink，Apache Spark和Google Cloud Dataflow。官方网站为 https://beam.apache.org

## 概述

Beam提供了一种通用方法来表示并行数据处理流水线，并支持三类用户，每个用户具有相对不同的背景和需求。
1. 最终用户：使用现有的SDK编写流水线，在现有的运行程序上运行。这些用户想要专注于编写他们的应用程序逻辑，和其他工作。
2. SDK作者：开发针对特定用户社区（Java，Python，Scala，Go，R，图形等）的Beam SDK。这些用户是编程极客，并且更喜欢屏蔽各种Runner及其实现的所有细节。
3. Runner 写手：拥有分布式处理的执行环境，并希望支持针对Beam模型编写的程序。屏蔽多个SDK的细节。

## Beam模型

Beam背后的模式是从许多内部Google数据处理项目中演变而成，包括MapReduce，FlumeJava和Millwheel。这个模型最初被称为"数据流模型"。

要了解更多关于Beam模型（尽管仍然以Dataflow的原始名称），请参见O'Reilly的网站上的World Beyond Batch: ：Streaming 101和Streaming 102帖子以及VLDB 2015文章。

Beam编程模型中的关键概念如下：

PCollection：表示数据的集合，其大小可以是有界的或无界的。
PTransform：表示将输入PCollections转换为输出PCollections的计算。
Pipeline：管理准备执行的PTransforms和PCollections的有向非循环图。
PipelineRunner：指定管道应在哪里和如何执行。


## SDK

Beam支持多种语言特定的SDK，用于针对Beam模型编写管道。
目前，该存储库包含Java和Python的SDK。

有新的SDK或DSL的想法？见 <a href="https://issues.apache.org/jira/browse/BEAM/component/12328909/">JIRA</a>。

## Runner

Beam支持通过PipelineRunners在多个分布式处理后端执行程序。目前，有以下PipelineRunners可用：

将DirectRunner运行在本地计算机上的管道。
将DataflowRunner管道提交到<a href="http://cloud.google.com/dataflow/
">Google Cloud Dataflow</a>。
该FlinkRunner运行在Apache Flink集群上的管道。代码已经从<a href="https://github.com/dataArtisans/flink-dataflow">dataArtisans / flink-dataflow</a>中捐赠，现在是Beam的一部分。

该SparkRunner运行在Apache星火集群上的管道。代码已经从<a href="https://github.com/cloudera/spark-dataflow">cloudera / spark-dataflow</a>中捐赠，现在是Beam的一部分。

创建新的 Runner？参见 <a href="https://issues.apache.org/jira/browse/BEAM/component/12328916/">JIRA</a>

##入门

请参阅我们网站上提供的<a href="http://beam.apache.org/get-started/quickstart/">快速入门</a>。

如果您希望从源代码中构建并安装整个项目，则可能需要在系统中安装一些其他工具。在基于Debian的分发中：

<code>
sudo apt-get install \
    openjdk-8-jdk \
    maven \
    python-setuptools \
    python-pip   
</code>

然后请使用标准mvn clean install命令。

## Spark Runner

参见<a href="https://github.com/apache/beam/tree/master/runners/spark">Spark Runner </a>


## 联系我们

参与Apache Beam：

订阅或邮件的user@beam.apache.org列表。
订阅或邮件的dev@beam.apache.org列表。
报告JIRA的问题。
我们也有一个贡献者的指南。


















