---
title: 在 Azure HDInsight 上将自定义 Maven 包与 Spark 中的 Jupyter 配合使用 | Azure
description: 逐步说明如何配置 HDInsight Spark 群集随附的 Jupyter notebook，以使用自定义 Maven 包。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/22/2019
ms.date: 12/23/2019
ms.author: v-yiso
ms.openlocfilehash: ab0954e3a9c1d1c47053d1beeb89acc01feca38f
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75336164"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter 笔记本配合使用
> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)
>
>

了解如何在 HDInsight 上的 Apache Spark 群集中配置 [Jupyter Notebook](https://jupyter.org/)，以使用未现成包含在群集中的、由社区贡献的外部 Apache **maven** 包。

可以在 [Maven 存储库](https://search.maven.org/) 中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如， [Spark 包](https://spark-packages.org/)中提供了社区贡献包的完整列表。

本文介绍如何将 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包与 Jupyter 笔记本配合使用。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

* 熟悉 Jupyter Notebook 和 Spark on HDInsight 的结合使用。 有关详细信息，请参阅[使用 Apache Spark on HDInsight 加载数据并运行查询](./apache-spark-load-data-run-query.md)。

* 群集主存储的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 对于 Azure 存储，此值为 `wasb://`；对于 Azure Data Lake Storage Gen2，此值为 `abfs://`。 如果为 Azure 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 将是 `wasbs://` 或 `abfss://`。另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

## <a name="use-external-packages-with-jupyter-notebooks"></a>将外部包与 Jupyter 笔记本配合使用

1. 导航至 `https://CLUSTERNAME.azurehdinsight.cn/jupyter`，其中 `CLUSTERNAME` 是 Spark 群集的名称。

1. 创建新的笔记本。 选择“新建”  ，然后选择“Spark”  。

    ![创建新的 Spark Jupyter 笔记本](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "创建新的 Jupyter 笔记本")

1. 随即创建新笔记本，并以 Untitled.pynb 名称打开。 选择顶部的笔记本名称，并输入一个友好名称。

    ![提供笔记本的名称](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "提供笔记本的名称")

1. 我们将使用 `%%configure` magic 将笔记本配置为使用外部包。 在使用外部包的笔记本中，确保在第一个代码单元中调用 `%%configure` magic。 这可以确保将内核配置为在启动会话之前使用该包。

    >[!IMPORTANT]  
    >如果忘记了在第一个单元中配置内核，可以结合 `-f` 参数使用 `%%configure`，但这会重新启动会话，导致所有进度都会丢失。

    | HDInsight 版本 | 命令 |
    |-------------------|---------|
    | 对于 HDInsight 3.5 和 HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |对于 HDInsight 3.3 和 HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. 上述代码片段需要 Maven 中心存储库中用于外部包的 maven 坐标。 在此代码片段中， `com.databricks:spark-csv_2.11:1.5.0` 是 **spark-csv** 包的 maven 坐标。 下面说明了如何构造包的坐标。

    a. 在 Maven 存储库中找出该包。 在本文中，我们使用 [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv)。

    b. 从存储库中收集 **GroupId**、**ArtifactId** 和 **Version** 的值。 确保收集的值与群集相匹配。 本示例中，我们将使用 Scala 2.11 和 Spark 1.5.0 包，但可能需要选择群集中相应的 Scala 或 Spark 版本的不同版本。 通过在 Spark Jupyter 内核或 Spark 提交上运行 `scala.util.Properties.versionString`，可以找出群集上的 Scala 版本。 通过在 Jupyter 笔记本上运行 `sc.version`，可以找出群集上的 Spark 版本。
   
    ![将外部包与 Jupyter 笔记本配合使用](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "将外部包与 Jupyter 笔记本配合使用")
   
    c. 串连这三个值并以冒号分隔 ( **:** )。

        com.databricks:spark-csv_2.11:1.5.0

1. 结合 `%%configure` magic 运行代码单元。 这会将基础 Livy 会话配置为使用提供的包。 现在，可以在笔记本的后续单元中使用该包，如下所示。

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    对于 HDInsight 3.4 及更低版本，应使用以下代码片段。

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. 然后，可以运行代码片段（如下所示）以查看上一步骤创建的数据帧中的数据。

        df.show()

        df.select("Time").count()

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展

* [在 HDInsight Linux 上的 Apache Spark 群集中将外部 python 包与 Jupyter 笔记本配合使用](apache-spark-python-package-installation.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

