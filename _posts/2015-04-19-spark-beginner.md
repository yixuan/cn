---
date: '2015-04-19 14:42:50'
layout: post
slug: spark-beginner-1
status: publish
title: 极简 Spark 入门笔记——安装和第一个回归程序
categories:
- 学习中
tags:
- Spark
- 程序
- 并行
- 回归
- 安装
- 配置
- 开源
- 大什么数据

---

现在的各种数据处理技术更新换代太快，新的名词和工具层出不穷，像是 Hadoop 和 Spark 这些，最近几年着实火了一把，但自己一直没精力和时间去尝试和学习。特别是听说这些工具配置起来比较复杂，就更懒得去折腾。在这一点上，果然是不如从前了。

然而绝知此事要躬行。即使将来不一定会花大功夫在这上面，但对它们有些基本的了解总是好的。听说 Spark 有一段时间了，但一直是只闻其名不见其实，今天就来简单记录一下初学 Spark 的若干点滴。

### Spark 是什么

按照 [Spark 官方的说法](http://spark.apache.org/)，Spark 是一个快速的集群运算平台，以及一系列处理大型数据集的工具包。用通俗的话说，Spark 与 R 一样是一套用于数据处理的软件和平台，但它最显著的特点就是处理大型数据（我就是不说大数据）的能力。

### 极简安装

Spark 本身面向的是大规模的分布式计算，但对学习和测试来说，利用单机的多核 CPU 就已经足够了，所以作为入门，我并没有打算去涉及多台计算机相连的情形。在这个基础上，第一件出乎我意料的事情就是，Spark 的安装和配置其实可以是**异常简单**的。

在网上出现的各种资料中，Spark 经常与 Hadoop 和 Scala 这两个名词一起出现。前者也是一个大型分布式计算的框架，诞生得比 Spark 更早；后者是 Spark 主要使用的一种编程语言。这就给我造成了一种印象，好像要使用 Spark 的话就得先安装配置好 Hadoop 和 Scala，而要安装它们又得有更多的软件依赖。但实际上，要在单机上使用 Spark，真正需要的只有下面两样：

1. 一个 Linux 操作系统
2. Java 开发环境 JDK

这两样可以说是大部分计算环境的标配，如果系统还没有安装 JDK，那么一般都可以用系统的包管理工具，比如 Fedora 下是

    sudo yum install java-1.8.0-openjdk

Ubuntu 下是

    sudo apt-get install openjdk-7-jdk

有了上面的开发环境，安装 Spark 就非常容易了，基本上只要下载预编译包，解压缩，然后添加系统路径即可。首先，到 [https://spark.apache.org/downloads.html](https://spark.apache.org/downloads.html) 选择最新的 Spark 版本和 Hadoop 版本（实际上我们暂时用不上 Hadoop，所以任何版本都行），然后下载压缩包。



<div align="center">
  <img src="http://i.imgur.com/2IbWNhI.png" />
</div>



完毕后，将其中的文件夹解压到某个特定的位置，比如，我将解压出的文件夹命名为 `spark`，并放在我的主文件夹 `/home/qyx` 里，这样我就可以执行

    /home/qyx/spark/bin/spark-shell

来运行 Spark 的终端了。为了避免每次打开 Spark 都要输入很长一串的路径，可以将 Spark 的 `bin` 目录加入到系统路径中，例如我在 `~/.bashrc` 文件中写入了

    export PATH=$PATH:/home/qyx/spark/bin

<!-- more -->

于是安装过程就这么愉快地结束了。

### Spark 终端

我觉得 Spark 非常亲民的一点是它提供了一个交互式的命令行终端，这样用户就可以快速地测试一些命令和语句，而无需每次都保存代码脚本然后调用执行，这对于 R 和 Python 用户来说是非常顺心的一件事。如果已经将 Spark 的 `bin` 目录加入到了系统路径，那么在系统命令行里输入 `spark-shell` 就可以进入 Spark 的交互式终端了。


<div align="center">
  <img src="http://i.imgur.com/WXuIyFf.png" />
</div>


如果出现了像图中 `scala>` 这样的提示符，就说明 Spark 安装成功。这里的 `scala` 指的是 Scala 编程语言。前面说了，Spark 主要使用 Scala 来进行开发，这意味着要最大程度地发挥 Spark 的性能，还需要再多学一门编程语言（Spark 还支持 Java 和 Python 的接口，但 Java 的语法没有 Scala 简洁，Python 的性能没有 Scala 的高）。虽然这需要花费一些额外的时间，但好在 Scala 的语法非常直观，基本上通过例子就可以模仿写出自己的程序来。

如果对 Scala 语言感兴趣，可以参考[这份教程](http://people.cis.ksu.edu/~schmidt/705a/Scala/scala_tutorial.pdf)来了解其基本的语法。但在这里我们将直接进入正题，用 Spark 来跑一个回归的例子。

### Spark 例子：回归模型

Spark 的数据分析功能包含在一个称为 MLlib 的组件当中，顾名思义，这是 Spark 的机器学习库，而回归是它支持的模型之一。为了演示例子，我们首先用 R 生成一组模拟的数据（是不是感觉怪怪的，主要是我还没用熟 Scala）：

{% highlight r %}
set.seed(123)
n = 1e6
p = 5
x = matrix(rnorm(n * p), n)
b = rnorm(p)
y = x %*% b + rnorm(n, 1, 3)
z = data.frame(y, x)
write.table(z, "reg.txt", sep = " ", row.names = FALSE, col.names = FALSE)
{% endhighlight %}

我们将数据保存为 `reg.txt` 文件，它共有一百万行，每一行有6个数，用空格分隔，其中第一个数代表因变量，其余的为自变量。

下面就是一段用 Scala 实现的 Spark 算回归的程序，其中包括了读取数据，拟合回归，计算回归系数，进行模型预测以及计算 $R^2$ 的过程。将这段程序复制到 Spark 的终端里，就可以迅速查看输出结果，体验 Spark 的基本功能了。

{% highlight scala linenos=table %}
import org.apache.spark.mllib.linalg.Vectors
import org.apache.spark.mllib.regression.LabeledPoint
import org.apache.spark.mllib.regression.LinearRegressionWithSGD
import org.apache.spark.mllib.stat.Statistics

val raw = sc.textFile("reg.txt")

val parsed = raw.map({ line =>
    val split = line.split(' ') // 字符串按空格切分
    val y = split(0).toDouble   // 第一个数转成Double型，是因变量
    val x = split.tail.map(_.toDouble) // 其余的转成自变量向量
    LabeledPoint(y, Vectors.dense(x))  // 把因变量和自变量打包
}).cache()

println(parsed.count())

val model = new LinearRegressionWithSGD()

model.optimizer.setNumIterations(100)
model.setIntercept(true)

val tstart = System.currentTimeMillis()
val res = model.run(parsed)
val tend = System.currentTimeMillis()

println("Model training time: " + (tend - tstart) / 1000.0 + " secs")

println(res.intercept)
println(res.weights)

val pred = res.predict(parsed.map(_.features))

val r = Statistics.corr(pred, parsed.map(_.label))
println("R-square = " + r * r)
{% endhighlight %}

下面我们来解释一下程序中每一部分的含义。开头1到4行的是一系列的 `import` 语句，目的是使用一些已经封装好的类，与 R 中的 `library()` 和 Python 的 `import` 语句类似。另外，相信不少读者立刻就能看出这是 Java 风格的导入语句。事实上，Scala 正是基于 Java 而开发的，因此其语法也大多脱胎于 Java。

第6行是读取数据，并将结果赋值给一个变量 `raw`。特别需要指出的是，这条语句实际上并没有开始读取文件，而只是建立了数据与程序之间的一种连接。这一点是与 R 中 `read.table()` 最大的不同。

第8到第13行其实包含了两条操作，第一是调用了 `raw` 对象的 `map()` 方法，之后连着的是 `cache()` 方法。`map()` 相当于 R 中的 `apply()`，意思是对读进来文件的每一行进行一次变换，然后将结果返回，组成一个新的向量。之所以需要这么做，是因为 Spark 读取文本文件时把每一行当作了一个字符串，因此我们需要从这个字符串中解析出我们需要的数据来。语句中被大括号包括的部分其实就是定义了这样一个变换函数，其输入是参数 `line`，代表传入函数的那个字符串，而输出是一个 `LabeledPoint` 对象，它是 MLlib 中定义的一个数据结构，用来代表一个因变量-自变量的配对观测。`map()` 方法返回的结果，就是一个长度为一百万，每个元素为 `LabeledPoint` 类型的向量。

接下来的 `cache()` 方法是 Spark 非常独到的一个操作，它是为了告诉 Spark，只要内存足够（限额可以通过 Spark 中的配置文件设置），就将数据放到内存里，以加快后续程序的运算速度。如果内存放不下，就依然保存到硬盘中。这样的好处在于，一方面避免了 R 把所有对象都往内存放的操作，另一方面避免了 Hadoop 这种重度依赖硬盘，以至于效率底下的情形。

而有趣的是，执行完这一句后，数据其实还没有进行真正的读取。这是因为 Spark 采用了一种“延迟运行”的机制，意思是数据只有在真正用到的地方才开始运算，其理念就是，“**只要老师不检查作业，我就暂时不写**”。基于这个原因，数据只有到了下面 `parsed.count()` 这句需要计算样本量时才真正开始进行读取和变换。

接下来的第17到26行就是真正拟合回归模型的时候了。MLlib 里拟合回归采用的是随机梯度下降法（SGD），选用这种算法的原因，一是因为它是一种迭代算法，可以通过设置迭代次数来随时终止计算（当然同时会损失一部分精度），这对于大规模的数据是非常关键的，因为通常在这些场合下我们不要求结果非常精确，但却希望计算能在规定的时间里完成。而另一个原因就在于这种算法可以比较容易地进行并行，扩展性较好。

第17行中，我们先建立模型对象，然后在19、20行设置最大迭代次数以及告诉模型应该包括截距项。22和24行插入了两句获取时间的函数，是为了评估模型训练（23行）花费的时间。

在第31行中，我们用拟合出的模型对训练集本身进行了预测。`parsed.map(_.features)` 的目的是取出训练集中的自变量部分，而 `predict()` 方法返回的结果就是因变量的预测值向量。

最后的第33行，我们利用 MLlib 为我们封装好的 `corr()` 函数计算了预测值与真实值之间的相关系数（`parsed.map(_.label)` 与 `parsed.map(_.features)` 相对，是取出训练集中的因变量），将它平方一下，就是模型的 $R^2$ 值了。

### 整体印象

总体感觉，Spark 还是挺好上手的，唯一麻烦的地方大概就是要对 Scala 语言有所了解。不过好在 Spark 也提供了 Python 和 Java 的接口，所以如果只是想尝尝鲜，也完全可以用其他的语言来加以操作。另外一个好消息是，从2015年4月起，Spark 官方已经开始提供 R 语言的接口，大约在2015年夏季发布 Spark 1.4 版本时，R 用户就可以使用原生的 Spark 接口了。

### 学习资源

下面几个链接我觉得对于 Spark 入门是非常有帮助的：

1. [伯克利的 Spark 迷你课程](http://ampcamp.berkeley.edu/big-data-mini-course/index.html)
2. [Spark 官方快速入门教程](https://spark.apache.org/docs/latest/quick-start.html)
3. [MLlib 官方文档](https://spark.apache.org/docs/latest/mllib-guide.html)



