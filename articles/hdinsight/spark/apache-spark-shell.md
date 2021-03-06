---
title: 在 Azure HDInsight 中使用交互式 Spark Shell
description: 交互式 Spark Shell 提供了一个读取执行打印进程，用于一次运行一个 Spark 命令并查看结果。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 12/12/2019
ms.date: 01/13/2020
ms.author: v-yiso
ms.openlocfilehash: bc394808dfdb0040819edf491d04390e068baab5
ms.sourcegitcommit: 6fb55092f9e99cf7b27324c61f5fab7f579c37dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75631090"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>从 Spark Shell 运行 Apache Spark

交互式 [Apache Spark](https://spark.apache.org/) Shell 提供了一个 REPL（读取执行打印循环）环境，用于一次运行一个 Spark 命令并查看结果。 此进程可用于开发和调试。 Spark 为其每个支持的语言（Scala、Python 和 R）提供了一个 shell。

## <a name="run-an-apache-spark-shell"></a>运行 Apache Spark Shell

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 编辑以下命令（将 CLUSTERNAME 替换为群集的名称），然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.cn
    ```

1. Spark 为 Scala (spark-shell) 和 Python (pyspark) 提供 shell。 在 SSH 会话中，输入以下命令之一：

    ```bash
    spark-shell
    pyspark
    ```

现在可以用适当的语言输入 Spark 命令。

1. 一些基本的示例命令：

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 和 SparkContext 实例

默认情况下，运行 Spark Shell 时，将自动实例化 SparkSession 和 SparkContext 的实例。

若要访问 SparkSession 实例，请输入 `spark`。 若要访问 SparkContext 实例，请输入 `sc`。

## <a name="important-shell-parameters"></a>重要 shell 参数

Spark Shell 命令（`spark-shell` 或 `pyspark`）支持多个命令行参数。 若要查看参数的完整列表，请使用开关 `--help` 启动 Spark Shell。 上述某些参数可能仅适用于 Spark Shell 包装的 `spark-submit`。

| 开关 | description | 示例 |
| --- | --- | --- |
| --master MASTER_URL | 指定主 URL。 在 HDInsight 中，此值始终为 `yarn`。 | `--master yarn`|
| --jars JAR_LIST | 要包括在驱动程序和执行程序类路径中的本地 jar 的逗号分隔列表。 在 HDInsight 中，此列表包含 Azure 存储或 Data Lake Storage 的默认文件系统路径。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | 要包括在驱动程序和执行程序类路径中的 maven jar 坐标的逗号分隔列表。 依次搜索本地 maven 存储库、maven 中心存储库、使用 `--repositories` 指定的任何其他远程存储库。 坐标的格式为 *groupId*:*artifactId*:*version*。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | 仅适用于 Python，要在 PYTHONPATH 上放置的 .zip、.egg 或 .py 文件的逗号分隔列表。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure HDInsight 上的 Apache Spark 简介](apache-spark-overview.md)。
- 若要使用 Spark 群集和 SparkSQL，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
- 若要编写使用 Spark 处理流数据的应用程序，请参阅[什么是 Apache Spark 结构化流式处理？](apache-spark-streaming-overview.md)。

