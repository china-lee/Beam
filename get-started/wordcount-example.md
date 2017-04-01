# Apache Beam WordCount示例

WordCount示例演示如何设置可以读取文本的处理流水线，将文本行标记为单个单词，并对这些单词执行频次计数。Beam SDK包含一系列这四个相继更详细的WordCount示例，彼此建立。所有例子的输入文本都是一套莎士比亚的文本。

每个WordCount示例在Beam编程模型中引入了不同的概念。首先了解最小的WordCount，最简单的例子。一旦您对建立管道的基本原则感到满意，请继续了解其他示例中的更多概念。

最小的WordCount演示了构建管道的基本原理。
WordCount引入了一些更常见的创建可重用和可维护管道的最佳做法。
调试WordCount引入了日志记录和调试实践。
Windowed WordCount演示了如何使用Beam的编程模型来处理有界数据集和无界数据集。


## MinimalWordCount

最小的WordCount演示了一个简单的流水线，可以从文本文件中读取，应用变换来标记和计数单词，并将数据写入输出文本文件。此示例硬编码其输入和输出文件的位置，并且不执行任何错误检查; 它只是为了向您展示创建Beam pipeline的“主干应”。这种缺乏参数化使得这种特定的管道在不同的Runner之间比标准的Beam Pipeline更不可移植。在后面的例子中，我们将参数化管道的输入和输出源并显示其他最佳实践。

要运行此示例，请按照“ Beam 示例”中的说明进行操作。要查看完整的代码，请参阅MinimalWordCount。

关键概念：

创建管道
将变换应用于管道
阅读输入（在本示例中：阅读文本文件）
应用ParDo转换
应用SDK提供的转换（在本例中为Count）
写输出（在本例中：写入文本文件）
运行管道

以下部分将详细介绍这些概念以及Minimal WordCount管道中相关代码的摘录。

## 创建管道

创建Beam 管道的第一步是创建一个PipelineOptions object。该对象允许我们为管道(Pipeline)设置各种选项，例如将执行我们的管道的管道运行程序和所选运行程序所需的任何特定于运行程序的配置。在这个例子中，我们以编程方式设置这些选项，但通常使用命令行参数进行设置PipelineOptions。

您可以指定用于执行管道的运行程序，例如DataflowRunner或SparkRunner。如果省略指定一个运行程序，如本示例所示，您的管道将在本地执行DirectRunner。在下一节中，我们将指定管道的运​​行。

<code>
PipelineOptions options = PipelineOptionsFactory.create();

    // In order to run your pipeline, you need to make following runner specific changes:
    //
    // CHANGE 1/3: Select a Beam runner, such as DataflowRunner or FlinkRunner.
    // CHANGE 2/3: Specify runner-required options.
    // For DataflowRunner, set project and temp location as follows:
    //   DataflowPipelineOptions dataflowOptions = options.as(DataflowPipelineOptions.class);
    //   dataflowOptions.setRunner(DataflowRunner.class);
    //   dataflowOptions.setProject("SET_YOUR_PROJECT_ID_HERE");
    //   dataflowOptions.setTempLocation("gs://SET_YOUR_BUCKET_NAME_HERE/AND_TEMP_DIRECTORY");
    // For FlinkRunner, set the runner as follows. See {@code FlinkPipelineOptions}
    // for more details.
    //   options.setRunner(FlinkRunner.class);
</code>

下一步是使用我们刚刚构建的选项创建一个管道对象。Pipeline对象建立与该特定流水线相关联的要执行的转换图。

<code>
Pipeline p = Pipeline.create(options);
</code>


## 应用管道转换

最小的WordCount管道包含几个转换，以将数据读入流水线，操纵或以其他方式转换数据，并写出结果。每个变换表示管道中的操作。

每个变换都需要某种输入（数据或其他），并产生一些输出数据。输入和输出数据由SDK类表示PCollection。PCollection是一个特殊的类，由Beam SDK提供，您可以使用它来表示几乎任何大小的数据集，包括无限数据集。


<a href="https://beam.apache.org/images/wordcount-pipeline.png">图1：流水线数据流</a>

最小WordCount管道包含五个变换：

1.文本文件Read变换应用于管道对象本身，并产生一个PCollection输出。输出PCollection中的每个元素表示输入文件中的一行文本。这个例子恰好是使用存储在可公开访问的Google云端存储桶（“gs：//”）中的输入数据。

<code>
p.apply(TextIO.Read.from("gs://apache-beam-samples/shakespeare/*"))
</code>

2.ParDo 变换调用DoFn在每个元素上调用一个匿名类, 标记文本到单个单词（在线作为匿名类中定义）。该变换的输入是PCollection由前一个TextIO.Read变换生成的文本行。的ParDo变换输出一个新的PCollection，其中每个元素表示的文本的单词。

<code>
	.apply("ExtractWords", ParDo.of(new DoFn<String, String>() {
    @ProcessElement
    public void processElement(ProcessContext c) {
        for (String word : c.element().split("[^a-zA-Z']+")) {
            if (!word.isEmpty()) {
                c.output(word);
            }
        }
    }
}))
</code>

3.SDK提供的Count变换是一个通用的变换，它采用PCollection任何类型，并返回一个PCollection键/值对。每个键代表输入集合中的唯一元素，每个值表示输入集合中键出现的次数。

在这个流水线中，输入Count是PCollection由先前生成的单个词ParDo，输出是PCollection键/值对，其中每个键表示文本中的唯一字，相关联的值是每个单词的发生次数。

<code>
.apply(Count.<String>perElement())
</code>

4.下一个变换将唯一字和发生计数的每个键/值对格式格式化成适合写入输出文件的可打印字符串。

Map变换是封装简单的更高级别的复合变换ParDo; 对于输入PCollection中的每个元素，Map变换应用一个函数产生一个输出元素。

<code>
	.apply("FormatResults", MapElements.via(new SimpleFunction<KV<String, Long>, String>() {
    @Override
    public String apply(KV<String, Long> input) {
        return input.getKey() + ": " + input.getValue();
    }
}))
</code>

5.文本文件写入变换。此变换将PCollection格式化的字符串作为输入，并将每个元素写入输出文本文件。输入中的每个元素PCollection表示结果输出文件中的一行文本。

<code>
.apply(TextIO.Write.to("wordcounts"));

</code>

请注意，该Write变换产生类型的微不足道的结果值PDone，在这种情况下，该值将被忽略。

## 运行管道

通过调用run方法来运行管道，该方法将您的管道发送到您创建管道时指定的管道运行程序执行。

<code>
p.run().waitUntilFinish();
</code>

请注意，该run方法是异步的。为了阻止执行，请运行您的管道来添加该waitUntilFinish方法。






























