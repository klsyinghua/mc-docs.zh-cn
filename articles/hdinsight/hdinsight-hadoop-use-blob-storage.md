---
title: 从与 HDFS 兼容的 Azure 存储查询数据 - Azure HDInsight | Azure
description: 了解如何从 Azure 存储和 Azure Data Lake Storage 查询数据，以存储分析结果。
services: hdinsight,storage
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.topic: get-started-article
ms.devlang: na
origin.date: 11/01/2019
ms.date: 12/23/2019
ms.author: v-yiso
ms.openlocfilehash: 81bcfefb8e20b250aecb1003eaebe470becded1c
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75336231"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>将 Azure 存储与 Azure HDInsight 群集配合使用

若要分析 HDInsight 群集中的数据，可将数据存储在 [Azure 存储](../storage/common/storage-introduction.md)和/或 [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md) 中。 使用这两个存储选项都可以安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。

Apache Hadoop 支持默认文件系统的概念。 默认文件系统意指默认方案和授权。 它还可用于解析相对路径。 在 HDInsight 群集创建过程中，可以指定 Azure 存储中的 Blob 容器作为默认文件系统，或者借助 HDInsight 3.6，可以选择 Azure 存储或 Azure Data Lake Storage Gen 2 作为默认文件系统（有少数例外）。 

本文介绍 Azure 存储如何与 HDInsight 群集配合使用。 若要深入了解如何创建 HDInsight 群集，请参阅[在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

Azure 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。 HDInsight 可将 Azure 存储中的 Blob 容器用作群集的默认文件系统。 通过 Hadoop 分布式的文件系统 (HDFS) 界面，可以针对作为 Blob 存储的结构化或非结构化数据直接运行 HDInsight 中的整套组件。

> [!IMPORTANT]  
> 存储帐户类型 **BlobStorage** 仅可用作 HDInsight 群集的辅助存储器。

| 存储帐户类型 | 支持的服务 | 支持的性能层 | 支持的访问层 |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2（常规用途 v2）  | Blob     | 标准                    | 热、冷、存档\*   |
| 存储（常规用途 v1）   | Blob     | 标准                    | 不适用                    |
| BlobStorage                    | Blob     | 标准                    | 热、冷、存档\*   |

建议不要使用默认 blob 容器来存储业务数据。 最佳做法是每次使用之后删除默认 Blob 容器以降低存储成本。 默认容器包含应用程序日志和系统日志。 请确保在删除该容器之前检索日志。

不支持将单个 blob 容器共享为多个群集的默认文件系统。

> [!NOTE]  
> 存档访问层是一个离线层，具有几小时的检索延迟，不建议与 HDInsight 一起使用。 有关详细信息，请参阅[存档访问层](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="access-files-from-the-cluster"></a>从群集访问文件

可以通过多种方法从 HDInsight 群集访问 Data Lake Storage 中的文件。 URI 方案提供了使用 *wasb:* 前缀的未加密访问和使用 *wasbs* 的 SSL 加密访问。 建议尽量使用 *wasbs* ，即使在访问位于同一 Azure 区域内的数据时也是如此。

* **使用完全限定的名称**。 使用此方法时，需提供要访问的文件的完整路径。

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **使用缩短的路径格式**。 使用此方法时，需将群集根的路径替换为：

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **使用相对路径**。 使用此方法时，仅需提供要访问的文件的相对路径。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>数据访问示例

示例基于到群集的头节点的 [ssh 连接](./hdinsight-hadoop-linux-use-ssh-unix.md)。 示例使用所有三个 URI 方案。 将 `CONTAINERNAME` 和 `STORAGEACCOUNT` 替换为相关值

#### <a name="a-few-hdfs-commands"></a>一些 hdfs 命令

1. 在本地存储上创建一个简单的文件。

    ```bash
    touch testFile.txt
    ```

1. 在群集存储上创建目录。

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 将数据从本地存储复制到群集存储。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 列出群集存储上的目录内容。

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]
> 在 HDInsight 外部使用 Blob 时，大多数实用程序无法识别 WASB 格式，应改用基本路径格式，如 `example/jars/hadoop-mapreduce-examples.jar`。

#### <a name="creating-a-hive-table"></a>创建 Hive 表

但为了便于说明，显示了三个文件位置。 实际执行时，仅使用 `LOCATION` 条目之一。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-abmari"></a>从 Abmari 标识存储路径

* 若要标识已配置的默认存储的完整路径，请导航到

    “HDFS”   >   “配置”，然后在筛选器输入框中输入 `fs.defaultFS`。

* 若要检查是否已将 wasb 存储配置为辅助存储器，请导航到

    “HDFS”   >   “配置”，然后在筛选器输入框中输入 `blob.core.windows.net`。

## <a name="blob-containers"></a>Blob 容器

若要使用 Blob，请先创建 [Azure 存储帐户](../storage/common/storage-create-storage-account.md)。 在此过程中，可指定在其中创建存储帐户的 Azure 区域。 群集和存储帐户必须位于同一区域。 Hive 元存储 SQL Server 数据库和 Apache Oozie 元存储 SQL Server 数据库也必须位于同一区域。

无论所创建的每个 Blob 位于何处，它都属于 Azure 存储帐户中的某个容器。 此容器可以是在 HDInsight 外部创建的现有的 Blob，也可以是为 HDInsight 群集创建的容器。

默认的 Blob 容器存储群集特定的信息，如作业历史记录和日志。 请不要多个 HDInsight 群集之间共享默认的 Blob 容器。 这可能会损坏作业历史记录。 建议对每个群集使用不同的容器，并将共享数据放入在所有相关群集的部署中指定的链接存储帐户，而不是放入默认存储帐户。 有关配置链接存储帐户的详细信息，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。 但是，在删除原始的 HDInsight 群集后，可以重用默认存储容器。 对于 HBase 群集，实际上可以通过使用已删除的 HBase 群集使用的默认 Blob 容器创建新的 HBase 群集，从而保留 HBase 表架构和数据。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>与 Azure 存储交互

Microsoft 提供以下用于操作 Azure 存储的工具：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>使用其他存储帐户

创建 HDInsight 群集时，可以指定要与其关联的 Azure 存储帐户。 除了此存储帐户外，在创建过程中或群集创建完成后，还可以从同一 Azure 订阅或不同 Azure 订阅添加其他存储帐户。 有关添加其他存储帐户的说明，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。

## <a name="next-steps"></a>后续步骤
本文介绍如何将与 HDFS 兼容的 Azure 存储和 HDInsight 配合使用。 这样可以生成可缩放的长期存档数据采集解决方案，并使用 HDInsight 来解锁所存储结构化和非结构化数据内的信息。

有关详细信息，请参阅：

* [Azure HDInsight 入门][hdinsight-get-started]
* [将数据上传到 HDInsight][hdinsight-upload-data]
* [将 Apache Hive 和 HDInsight 配合使用][hdinsight-use-hive]
* [将 Apache Pig 和 HDInsight 配合使用][hdinsight-use-pig]
* [使用 Azure 存储共享访问签名来限制使用 HDInsight 访问数据][hdinsight-use-sas]
* [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](hdinsight-hadoop-use-data-lake-storage-gen2.md)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png
<!--Update_Description: wording update, update storage link and change 'wasbs' into 'wasb'-->