---
title: 当 Azure 存储容器包含 Azure HDInsight 中的许多文件时，Apache Spark 作业运行速度缓慢
description: 当 Azure 存储容器包含 Azure HDInsight 中的许多文件时，Apache Spark 作业运行速度缓慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 07/29/2019
ms.date: 09/23/2019
ms.openlocfilehash: 112aaca453f12bb644b975c3c7e4ebb161ccdd89
ms.sourcegitcommit: 43f569aaac795027c2aa583036619ffb8b11b0b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921332"
---
# <a name="scenario-apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>方案：当 Azure 存储容器包含 Azure HDInsight 中的许多文件时，Apache Spark 作业运行速度缓慢

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

运行 HDInsight 群集时，如果存在多个文件/子文件夹，写入 Azure 存储容器的 Apache Spark 作业将很缓慢。 例如，写入新容器只需 20 秒，而写入包含 20 万个文件的容器却需要大约 2 分钟。

## <a name="cause"></a>原因

这是一个已知的 Spark 问题。 速度变慢的原因在于执行 Spark 作业期间的 `ListBlob` 和 `GetBlobProperties` 操作。

为了跟踪分区，Spark 必须维护一个 `FileStatusCache`，其中包含有关目录结构的信息。 使用此缓存，Spark 可以分析路径并识别可用分区。 跟踪分区的好处是，Spark 只在读取数据时才处理必要的文件。 为了将此信息保持最新状态，当你写入新数据时，Spark 必须列出目录下的所有文件，并更新此缓存。

在 Spark 1.6 中，每次更新目录时，都会：(1) 清除缓存；(2) 以递归方式列出所有文件；(3) 更新整个缓存。 这会导致出现许多的列出操作。

在 Spark 2.1 中，尽管不需要在每次写入后更新缓存，但 Spark 会检查现有分区列是否与当前写入请求中的建议分区列匹配，因此它也会在每次写入时导致发生列出操作。

在 Spark 2.2 中，使用追加模式写入数据时，此性能问题应可解决。

## <a name="resolution"></a>解决方法

创建分区数据集时必须使用分区方案来更新 `FileStatusCache`，该方案用于限制 Spark 必须列出的文件数。

对于 N% 100 == 0（100 只是一个示例）的每第 N 个微批，请将现有数据移到可由 Spark 加载的另一个目录。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。