---
title: 针对 Hadoop、Spark、Kafka、HBase 或 R Server 的群集设置 - Azure HDInsight
description: 通过浏览器、Azure CLI、Azure PowerShell、REST 或 SDK 为 HDInsight 设置 Hadoop、Kafka、Spark、HBase、R Server 或 Storm 群集。
services: storage
author: WenJason
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 01/14/2019
ms.author: v-jay
ms.openlocfilehash: cf83fd5ac274e5f029acdac7e081d75cec096dce
ms.sourcegitcommit: 5eff40f2a66e71da3f8966289ab0161b059d0263
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192972"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>快速入门：在 HDInsight 中设置群集

在此快速入门中，了解如何使用 Hadoop、Spark、Kafka、交互式查询、HBase、R Server 或 Storm 在 HDInsight 中设置和配置群集。 还将了解如何自定义群集、将它们加入到域并附加到启用了 [Azure Data Lake Storage Gen2 预览版](data-lake-storage-introduction.md)的存储帐户。

Hadoop 群集由用于对任务进行分布式处理的多个虚拟机（节点）组成。 Azure HDInsight 对各个节点的安装和配置的实现细节进行处理，因此你只需提供常规配置信息。

> [!IMPORTANT]
>HDInsight 群集计费在创建群集之后便会开始，删除群集后才会停止。 HDInsight 群集按分钟收费，因此不再需要使用群集时，应将其删除。 了解如何[删除群集](../../hdinsight/hdinsight-delete-cluster.md)。

具有 Data Lake Storage Gen2 功能的存储帐户在此快速入门中用作数据层。 借助其分层命名空间服务和 [Hadoop 驱动程序](data-lake-storage-abfs-driver.md)，Data Lake Storage Gen2 针对分布式处理和分析进行优化。 即使在删除了 HDInsight 群集后，存储在已启用 Data Lake Storage Gen2 的存储帐户中的数据仍会存在。

## <a name="cluster-setup-methods"></a>群集设置方法

下表显示了可用于设置 HDInsight 群集的不同方法。

| 群集创建方法 | Web 浏览器 | 命令行 | REST API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure 门户](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure CLI（版本 1.0）](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager 模板](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>快速创建：基本群集设置

本文逐步讲解如何通过 [Azure 门户](https://portal.azure.cn)进行设置：在门户中使用“快速创建”或“自定义”选项创建 HDInsight 群集。

![hdinsight 创建选项 自定义 快速创建](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

遵照屏幕上的说明执行基本的群集设置。 下面提供了各项设置的详细信息：

* [资源组名称](#resource-group-name)
* [群集类型和配置](#cluster-types) 
* [群集登录名和 SSH 用户名](#cluster-login-and-ssh-user-name)
* [位置](#location)

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 3.3 停用](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="resource-group-name"></a>资源组名称

可以借助 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 以组（称为 Azure 资源组）的形式处理应用程序中的资源。 可以通过单个协调的操作来部署、更新、监视或删除应用程序的所有资源。

## <a name="cluster-types"></a>群集类型和配置

Azure HDInsight 目前提供以下群集类型，每种类型都具有一组用于提供特定功能的组件。

> [!IMPORTANT]
> HDInsight 群集以多种类型提供，每种类型适用于单个工作负荷或技术。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。 如果解决方案需要分布在多种 HDInsight 群集类型上的技术，可以使用 [Azure 虚拟网络 ](/virtual-network)连接所需的群集类型。

| 群集类型 | 功能 |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Batch 查询和存储数据的分析 |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |大量无架构 NoSQL 数据的处理 |
| [交互式查询](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |更快的交互式 Hive 查询的内存中缓存 |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | 分布式流式处理平台，可用于构建实时流数据管道和应用程序 |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |内存中处理、交互式查询、微批流处理 |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |实时事件处理 |

### <a name="hdinsight-version"></a>HDInsight 版本

选择此群集的 HDInsight 版本。 有关详细信息，请参阅[支持的 HDInsight 版本](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)。

## <a name="cluster-login-and-ssh-user-name"></a>群集登录名和 SSH 用户名

使用 HDInsight 群集时，可以在群集创建期间配置两个用户帐户：

* HTTP 用户：默认的用户名为 *admin*。它使用 Azure 门户上的基本配置。 有时称为“群集用户”。
* SSH 用户（Linux 群集）：用来通过 SSH 连接到群集。 有关详细信息，请参阅 [将 SSH 与 HDInsight 配合使用](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="location"></a>群集和存储的位置（区域）

无需显式指定群集位置；群集位于默认存储所在的位置。 有关受支持区域的列表，请单击 [HDInsight 定价](https://www.azure.cn/zh-cn/pricing/details/hdinsight/)中的“区域”下拉列表。

## <a name="storage-endpoints-for-clusters"></a>群集的存储终结点

Hadoop 的本地安装对群集上的存储使用 Hadoop 分布式文件系统 (HDFS)，而在云中，需使用已连接到群集的存储终结点。 HDInsight 群集使用 [Data Lake Storage Gen2](data-lake-storage-abfs-driver.md) 或 [Azure 存储中的 Blob](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)。 使用 Azure 存储或 Data Lake Storage 意味着可以安全地删除用于计算的 HDInsight 群集，同时仍保留数据。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他位置使用其他存储帐户。

配置期间，为默认存储终结点指定 Data Lake Storage。 默认存储包含应用程序日志和系统日志。 （可选）可以指定群集可访问的其他已启用 Data Lake Storage Gen2 的链接存储帐户。 HDInsight 群集和相关的存储帐户必须在同一个 Azure 位置。

![群集存储设置：与 HDFS 兼容的存储终结点](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage2.png)

> [!IMPORTANT]
> 确保“禁用 Data Lake Store 访问”。 此设置指的是旧版 Data Lake Store 功能，为了 Data Lake Storage 功能正常运行，需要禁用此设置。

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>可选元存储

可创建可选的 Hive 或 Oozie 元存储。 但是，并非所有群集类型都支持元存储，并且 Azure SQL 数据仓库与元存储不兼容。

有关详细信息，请参阅[在 Azure HDInsight 中使用外部元数据存储](../../hdinsight/hdinsight-use-external-metadata-stores.md)。

> [!IMPORTANT]
> 创建自定义元存储时，请不要在数据库名称中使用破折号、连字符或空格。 否则可能导致群集创建过程失败。

### <a name="use-hiveoozie-metastore"></a>Hive 元存储

如果希望在删除 HDInsight 群集后保留 Hive 表，请使用自定义元存储。 这样，便可以将该元存储附加到另一个 HDInsight 群集。

为一个 HDInsight 群集版本创建的 HDInsight 元存储不能在不同的 HDInsight 群集版本之间共享。 有关 HDInsight 版本的列表，请参阅[支持的 HDInsight 版本](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)。

### <a name="oozie-metastore"></a>Oozie 元存储

若要提高使用 Oozie 时的性能，请使用自定义元存储。 删除群集后，元存储也可提供对 Oozie 作业数据的访问权限。

> [!IMPORTANT]
> 无法重用自定义 Oozie 元存储。 若要使用自定义 Oozie 元存储，必须在创建 HDInsight 群集时提供一个空的 Azure SQL 数据库。

## <a name="configure-cluster-size"></a>配置群集大小

只要群集存在，就会产生节点使用费。 创建群集后便开始计费，删除群集后停止计费。 无法取消分配群集或暂停群集。

### <a name="number-of-nodes-for-each-cluster-type"></a>每个群集类型的节点数

每个群集类型有自身的节点数目、节点术语和默认的 VM 大小。 下表中的括号内列出了每个节点类型的节点数目。

| 类型 | Nodes | 图示 |
| --- | --- | --- |
| Hadoop |头节点 (2)，数据节点 (1+) |![HDInsight Hadoop 群集节点](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |头服务器 (2)，区域服务器 (1+)，主控/ZooKeeper 节点 (3) |![HDInsight HBase 群集节点](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus 节点 (2)，监督程序服务器 (1+)，ZooKeeper 节点 (3) |![HDInsight Storm 群集节点](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |头节点 (2)，辅助角色节点 (1+)，ZooKeeper 节点 (3)（对于 A1 ZooKeeper VM 大小免费） |![HDInsight Spark 群集节点](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

有关详细信息，请参阅“HDInsight 中的 Hadoop 组件和版本是什么？”中的[群集的默认节点配置和虚拟机大小](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

HDInsight 群集的成本取决于节点数和节点的虚拟机大小。

不同群集类型具有不同的节点类型、节点数和节点大小：
* Hadoop 群集类型的默认配置：
    * 两个头节点  
    * 四个数据节点
* Storm 群集类型的默认配置：
    * 两个 Nimbus 节点
    * 三个 ZooKeeper 节点
    * 四个监督器节点

如果你只是想要试用 HDInsight，我们建议使用一个数据节点。 有关 HDInsight 定价的详细信息，请参阅 [HDInsight 定价](https://www.azure.cn/zh-cn/pricing/details/hdinsight/)。

使用 Azure 门户配置群集时，可通过“节点定价层”边栏选项卡查看节点大小。 在门户中，还可以查看不同节点大小的相关费用。

![HDInsight VM 节点大小](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>虚拟机大小

部署群集时，请根据要部署的解决方案选择计算资源。 以下 VM 用于 HDInsight 群集：

* A 系列和 D1-4 系列 VM：[常规用途 Linux VM 大小](/virtual-machines/linux/sizes-general)
* D11-14 系列 VM：[内存优化 Linux VM 大小](/virtual-machines/linux/sizes-memory)

使用不同的 SDK 或使用 Azure PowerShell 创建群集时，若要确定应该使用哪个值来指定 VM 大小，请参阅[用于 HDInsight 群集的 VM 大小](../../cloud-services/cloud-services-sizes-specs.md#size-tables)。 请使用此链接本章的“大小”列中的值。

> [!IMPORTANT]
> 如果需要在群集中使用 32 个以上的辅助角色节点，则必须选择至少具有 8 个核心和 14 GB RAM 的头节点大小。

有关详细信息，请参阅[虚拟机的大小](../../virtual-machines/windows/sizes.md)。 有关不同大小的定价信息，请参阅 [HDInsight 定价](https://azure.cn/pricing/details/hdinsight)。

## <a name="custom-cluster-setup"></a>自定义群集设置

“自定义群集设置”是在“快速创建”设置的基础之上实现的，其中添加了以下选项：

- [HDInsight 应用程序](#hdinsight-applications)
- [群集大小](#cluster-size)
- 高级设置
  - [脚本操作](#customize-clusters-using-script-action)
  - [虚拟网络](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>在群集上安装 HDInsight 应用程序

HDInsight 应用程序是用户可以在基于 Linux 的 HDInsight 群集上安装的应用程序。 可以使用 Microsoft、第三方提供的或你自己开发的应用程序。 有关详细信息，请参阅[在 Azure HDInsight 上安装第三方 Hadoop 应用程序](../../hdinsight/hdinsight-apps-install-applications.md)。

大多数 HDInsight 应用程序安装在空边缘节点上。  空边缘节点是安装并配置了与头节点中相同的客户端工具的 Linux 虚拟机。 可以使用该边缘节点来访问群集、测试客户端应用程序和托管客户端应用程序。 有关详细信息，请参阅[在 HDInsight 中使用空边缘节点](../../hdinsight/hdinsight-apps-use-edge-node.md)。

## <a name="advanced-settings-script-actions"></a>高级设置：脚本操作

可以在创建期间通过使用脚本安装其他组件或自定义群集配置。 此类脚本可通过 **脚本操作**调用，脚本操作是一种配置选项，可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md)。

某些本机 Java 组件（如 Mahout 和 Cascading）可以在群集上作为 Java 存档 (JAR) 文件运行。 可以通过 Hadoop 作业提交机制将这些 JAR 文件分发到 Azure 存储或 Azure Data Lake Storage，并提交到 HDInsight 群集。 有关详细信息，请参阅[以编程方式提交 Hadoop 作业](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md)。

> [!NOTE]
> 如果在将 JAR 文件部署到 HDInsight 群集或调用 HDInsight 群集上的 JAR 文件时遇到问题，请联系 [Azure 支持](https://www.azure.cn/zh-cn/support/contact/)。
>
> HDInsight 不支持级联，因此不符合 Azure 技术支持的条件。 有关支持的组件的列表，请参阅 [HDInsight 提供的群集版本有哪些新功能？](../../hdinsight/hdinsight-component-versioning.md)。

在创建过程中，有时需要配置以下配置文件：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

有关详细信息，请参阅 [使用 Bootstrap 自定义 HDInsight 群集 ](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md)。

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>高级设置：使用虚拟网络扩展群集

如果解决方案需要分布在多种 HDInsight 群集类型上的技术，可以使用 [Azure 虚拟网络 ](/virtual-network)连接所需的群集类型。 此配置允许群集以及部署到群集的任何代码直接相互通信。

有关将 Azure 虚拟网络与 HDInsight 配合使用的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md)。

有关在一个 Azure 虚拟网络中使用两种群集类型的示例，请参阅 [结合使用 Spark Structured Streaming 和 Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md)。 有关将 HDInsight 与虚拟网络配合使用的详细信息（包括虚拟网络的特定配置要求），请参阅 [Extend HDInsight capabilities by using Azure Virtual Network](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md)（使用 Azure 虚拟网络扩展 HDInsight 功能）。

## <a name="troubleshoot-access-control-issues"></a>排查访问控制问题

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>后续步骤

- [适用于 Azure Data Lake Storage Gen2 的 ABFS Hadoop 文件系统驱动程序](data-lake-storage-abfs-driver.md)
- [教程：使用 Apache Hive on Azure HDInsight 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md)
- [HDInsight、Hadoop 生态系统和 Hadoop 群集是什么？](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [开始在 HDInsight 中使用 Hadoop](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [使用 Windows 电脑在 HDInsight 上的 Hadoop 中工作](../../hdinsight/hdinsight-hadoop-windows-tools.md)