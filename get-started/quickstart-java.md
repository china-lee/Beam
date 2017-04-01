# Apache Beam Java SDK快速入门

此Quickstart将引导您执行您的第一个Beam管道，运行使用Beam的Java SDK编写的WordCount，选择合适的 Runner。

设置您的开发环境
获取WordCount代码
运行WordCount
检查结果
下一步

## 设置您的开发环境

下载并安装<a href=http://www.oracle.com/technetwork/java/javase/downloads/index.html>Java Development Kit（JDK） 1.7</a>或更高版本。验证<a href="https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/envvars001.html">JAVA_HOME</a>环境变量是否已设置并指向您的JDK安装。

按照Maven的特定操作系统的<a href="http://maven.apache.org/install.html">安装指南</a>，下载并安装<a href="http://maven.apache.org/download.cgi">Apache Maven</a>。


## 获取WordCount代码

获取WordCount管道副本的最简单方法是使用以下命令来生成一个简单的Maven项目，该项目包含Beam的WordCount示例，并针对最近的Beam版本进行构建：

<code>
	$ mvn archetype:generate \
      -DarchetypeRepository=https://repository.apache.org/content/groups/snapshots \
      -DarchetypeGroupId=org.apache.beam \
      -DarchetypeArtifactId=beam-sdks-java-maven-archetypes-examples \
      -DarchetypeVersion=LATEST \
      -DgroupId=org.example \
      -DartifactId=word-count-beam \
      -Dversion="0.1" \
      -Dpackage=org.apache.beam.examples \
      -DinteractiveMode=false
</code>

这将创建一个目录word-count-beam，其中包含一个简单pom.xml的一系列示例管道，用于计算文本文件中的单词。

<code>
$ cd word-count-beam/

$ ls
pom.xml	src

$ ls src/main/java/org/apache/beam/examples/
DebuggingWordCount.java	WindowedWordCount.java	common
MinimalWordCount.java	WordCount.java
</code>

有关这些示例中使用的Beam概念的详细介绍，请参阅<a href="https://beam.apache.org/get-started/wordcount-example">WordCount</a>示例演练。在这里，我们将专注于执行WordCount.java。


## 运行 WordCount

单个Beam Pipeline可以运行在多个Beam Runner，包括ApexRunner，FlinkRunner，SparkRunner或DataflowRunner。该DirectRunner是通用Runner，因为你的机器上本地运行，不需要特定的设置。

选择您要使用的Runner后：
1.确保你已经完成了任何一个跑步者的设置。
2.建立你的命令行：
	1).使用--runner=<runner>（默认为DirectRunner）指定特定的运行程序
	2).添加任何特定于赛跑者的选项
	3).选择输入文件和输出位置可以在选定的跑步者上访问。（例如，如果在外部集群上运行管道，则无法访问本地文件。）

3.运行您的第一个WordCount管道。


<code>
$ mvn compile exec:java -Dexec.mainClass=org.apache.beam.examples.WordCount \
     -Dexec.args="--inputFile=pom.xml --output=counts" -Pdirect-runner
</code>

## 检查结果

管道完成后，您可以查看输出。您会注意到可能有多个输出文件作为前缀count。这些文件的确切数量由运行者决定，使其灵活地执行高效的分布式执行。


<code>
$ ls counts*
</code>

当您查看文件的内容时，您会看到它们包含唯一的单词和每个单词的出现次数。文件中元素的顺序可能会有所不同，因为光束模型通常不保证排序，同样允许跑步者优化效率。

<code>
$ more counts*
api: 9
bundled: 1
old: 4
Apache: 2
The: 1
limitations: 1
Foundation: 1
...	
</code>

## 下一步

在WordCount示例演练中了解有关这些WordCount示例的更多信息。</t>
<a href="https://beam.apache.org/documentation/resources">相关演示文稿</a> </t>
加入Beam 用户邮件列表

















