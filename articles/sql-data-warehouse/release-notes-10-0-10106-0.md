---
title: 发行说明
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
origin.date: 11/12/2019
ms.date: 01/20/2020
author: WenJason
ms.author: v-jay
ms.reviewer: jrasnick
manager: digimobile
ms.custom: seo-lt-2019
ms.openlocfilehash: e2b3f1611cc62db7610bb5e676b679d64bd2d5e0
ms.sourcegitcommit: 6e47d840eb0ac773067723254e60dd318272d73e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75964890"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 数据仓库发行说明

本文汇总了 [Azure SQL 数据仓库](sql-data-warehouse-overview-what-is.md) (Azure SQL DW) 的最新版本中的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。

## <a name="check-your-azure-sql-data-warehouse-version"></a>检查 Azure SQL 数据仓库的版本

我们已在所有区域中推出新的功能用于检查实例中部署的版本，以及最新 Azure SQL 数据仓库可用功能的发行说明。 若要检查 Azure SQL 数据仓库的版本，请通过 SQL Server Management Studio (SSMS) 连接到数据仓库，然后运行 `SELECT @@VERSION;` 返回 Azure SQL 数据仓库的当前版本。

示例输出：

![SQL 数据仓库版本](./media/release-notes/t47-1-version.png)

使用标识的版本确认已将哪个版本应用到 Azure SQL 数据仓库。

## <a name="october-2019"></a>2019 年 10 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Copy（预览版）**|我们很高兴地宣布，现已推出简单灵活 COPY 语句的公共预览版用于数据引入。 现在，只需一条语句就能无缝引入数据并获得更高的灵活性，且无需分配高特权用户。 有关详细信息，请参阅 [COPY 命令文档](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)。|
|**工作负载隔离（预览版）**|为了在客户使其数据仓库大众化时为客户提供支持，我们宣布推出用于智能工作负载管理的新功能。 新的[工作负载隔离](/sql-data-warehouse/sql-data-warehouse-workload-isolation)功能使你能够管理异类工作负载的执行，同时提供灵活性以及对数据仓库资源的控制。 这会提高执行可预测性，并增强满足预定义 SLA 的能力。 </br>除了工作负载隔离外，现在还有其他选项可用于[工作负载分类](/sql-data-warehouse/sql-data-warehouse-workload-classification)。  除了登录分类以外，[创建工作负载分类器](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)语法还提供根据查询标签、会话上下文和时间对请求进行分类的功能。|
|**具体化视图（正式版）**|具体化视图会保留从视图定义查询返回的数据，并在基础表中的数据更改时自动更新。 它提高了复杂查询（通常是使用联接和聚合的查询）的性能，同时提供了简单的维护操作。 有关详细信息，请参阅[使用具体化视图优化性能](/sql-data-warehouse/performance-tuning-materialized-views)。  安装 [SQL Server Management Studio 18.4 或更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)可以编写具体化视图的脚本。|
|**动态数据掩码（正式版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。 有关详细信息，请参阅 [SQL 数据库动态数据掩码](/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**读取提交的快照隔离（正式版）**|可以使用 ALTER DATABSE 来为用户数据库启用或禁用快照隔离。 若要避免对当前工作负荷产生影响，可以在数据库维护期间设置此选项，或等到该数据库没有其他活动的连接时设置此选项。 有关详细信息，请参阅 [ALTER DATABASE SET 选项](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**有序聚集列存储索引（正式版）**|若要存储和有效查询大量数据，列存储至关重要。 排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。   有关详细信息，请参阅[使用有序聚集列存储索引优化性能](/sql-data-warehouse/performance-tuning-ordered-cci)。|
|**结果集缓存（正式版）**|启用结果集缓存后，Azure SQL 数据仓库将在用户数据库中自动缓存查询结果，供重复使用。 这样，后续的查询执行就能直接从持久性缓存中获取结果，因此无需重新计算。 结果集缓存提高了查询性能，并减少了计算资源的用量。 此外，使用缓存结果集的查询不会占用任何并发槽，因此不会计入现有的并发限制。 出于安全考虑，如果访问方用户的数据访问权限与创建缓存结果的用户相同，则访问方用户只能访问缓存的结果。 有关详细信息，请参阅[使用结果集缓存优化性能](/sql-data-warehouse/performance-tuning-result-set-caching)。 适用于 10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据发现和分类（正式版）**|[数据发现和分类](/sql-database/sql-database-data-discovery-and-classification?toc=/sql-data-warehouse/toc.json)功能现已推出正式版。 此功能提供高级功能用于**发现、分类、标记和保护**数据库中的敏感数据。|
|**Azure 顾问一键式集成**|现在，SQL 数据仓库直接在概述边栏选项卡中与 Azure 顾问建议集成，并提供一键式体验。 现在可以在概述边栏选项卡中发现建议，而无需导航到 Azure 顾问边栏选项卡。 在[此处](sql-data-warehouse-concept-recommendations.md)详细了解建议。|
|**读取提交的快照隔离（预览版）**|可以使用 ALTER DATABSE 来为用户数据库启用或禁用快照隔离。  若要避免对当前工作负荷产生影响，可以在数据库维护期间设置此选项，或等到该数据库没有其他活动的连接时设置此选项。 有关详细信息，请参阅 [ALTER DATABASE SET 选项](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest) T-SQL 支持现已在 SQL 数据仓库中推出，使客户能够将会话的执行上下文设置为指定的用户。|
|**更多 T-SQL 支持**|SQL 数据仓库的 T-SQL 语言外围应用已扩展，现在支持： </br> - [FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [sys.user_token (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服务改进 | 详细信息 |
| --- | --- |
|**具体化视图（预览版）**|具体化视图会保留从视图定义查询返回的数据，并在基础表中的数据更改时自动更新。 它提高了复杂查询（通常是使用联接和聚合的查询）的性能，同时提供了简单的维护操作。 有关详细信息，请参阅： </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [Azure SQL 数据仓库中支持的 T-SQL 语句](/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**更多 T-SQL 支持**|SQL 数据仓库的 T-SQL 语言外围应用已扩展，现在支持： </br> - [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|添加了 DBCC 命令以管理之前公布的结果集缓存。 有关详细信息，请参阅： </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 另请参阅 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 中新的 result_set_cache 列，该列显示执行的查询何时使用结果集缓存。|
|**排序聚集列存储索引（预览版）**|新列 column_store_order_ordinal 已添加到 [sys.index_columns](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest)，以标识已排序聚集列存储索引中列的顺序。|

## <a name="may-2019"></a>2019 年 5 月

| 服务改进 | 详细信息 |
| --- | --- |
|**动态数据掩码（预览版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。 有关详细信息，请参阅 [SQL 数据库动态数据掩码](/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**工作负荷重要性现已推出正式版**|工作负荷管理分类和重要性可以影响查询的运行顺序。 有关工作负荷重要性的详细信息，请参阅文档中的[分类](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概述文章。 另请查看[创建工作负荷分类器](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)文档。|
|**更多 T-SQL 支持**|SQL 数据仓库的 T-SQL 语言外围应用已扩展，现在支持： </br> - [TRIM](https://docs.microsoft.com/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 函数**|现在，业务分析师可以使用 Azure 数据仓库中的以下新 JSON 函数，以他们熟悉的 T-SQL 语言查询和操作采用 JSON 数据格式的文档：</br> - [ISJSON](https://docs.microsoft.com/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](https://docs.microsoft.com/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](https://docs.microsoft.com/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](https://docs.microsoft.com/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|使用结果集缓存可以立即获得查询响应，同时减少业务分析师和报告用户获得见解所需的时间。 有关详细信息，请参阅：</br> - [ALTER DATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 选项 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [SET RESULT SET CACHING (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 语句 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql)</br> - [sys.databases (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**排序聚集列存储索引（预览版）**|若要存储和有效查询大量数据，列存储至关重要。 对于每个表，列存储会将传入的数据划分为行组，行组的每个列构成了磁盘上的一个段。  排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。   有关详细信息，请参阅：</br> -  [CREATE TABLE（Azure SQL 数据仓库）](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)|

## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据发现和分类**|现已推出适用于 Azure SQL 数据仓库的数据发现和分类公共预览版。 若要保护客户的敏感数据和隐私，此功能至关重要。 随着企业和客户数据资产的不断增长，发现、分类和保护数据将越来越复杂。 Azure SQL 数据仓库原生引入的数据发现和分类功能有助于轻松保护数据。 此功能的总体优点包括：<br/>&bull; &nbsp; 满足数据隐私标准与合规性要求。<br/>&bull; &nbsp; 限制对包含高度敏感数据的数据仓库的访问并增强其安全性。<br/>&bull; &nbsp; 监视对敏感数据的异常访问并发出警报。<br/>&bull; &nbsp; 在 Azure 门户上的中心仪表板中可视化敏感数据。 </br></br>数据发现和分类适用于所有 Azure 区域中的 Azure SQL 数据仓库，它是包括漏洞评估和威胁检测在内的高级数据安全的一部分。 有关数据发现和分类的详细信息，请参阅[此博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和[联机文档](/sql-database/sql-database-data-discovery-and-classification)。|
|**GROUP BY ROLLUP**|现在，ROLLUP 是 Azure 数据仓库支持的一个 GROUP BY 选项。   GROUP BY ROLLUP 可为每个列表达式组合创建一个组。 GROUP BY 还可将结果“汇总”为小计和总计。 GROUP BY 函数的处理顺序为从右到左，减少了创建组和聚合所依据的列表达式数目。  列顺序会影响 ROLLUP 的输出，并可能会影响结果集中的行数。<br/><br/>有关 GROUP BY ROLLUP 的详细信息，请参阅 [GROUP BY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**改进了已用 DWU 和 CPU 门户指标的准确性**|SQL 数据仓库明显提高了 Azure 门户中的指标准确性。  此版本包含 CPU 和已用 DWU 指标定义的修复，可正确反映所有计算节点中的工作负荷。 在推出此项修复之前，指标值的报告程度不足。 在 Azure 门户中，已用 DWU 和 CPU 指标预期会增大。 |
|**行级别安全性**|早在 2017 年 11 月，我们就引入了行级别安全性功能。 我们现已将此项支持扩展到外部表。 此外，我们在内联表值函数（内联 TVF）中添加了调用非确定性函数来定义安全筛选器谓词的支持。 借助此项新增功能，可以在安全筛选器谓词中指定 IS_ROLEMEMBER()、USER_NAME() 等函数。 有关详细信息，请参阅[行级别安全性文档](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)中的示例。|
|**更多 T-SQL 支持**|SQL 数据仓库的 T-SQL 语言外围应用已扩展，现在包含对 [STRING_SPLIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) 的支持。
|**查询优化器增强** |查询优化是任何数据库的关键组成部分。 在如何执行查询方面做出最佳选择可以得到明显的改进。  在分布式环境中执行复杂的分析查询时，执行的操作数目非常关键。 生成更好的质量计划可以增强查询性能。 这些计划可以尽量减少高开销的数据传输操作和冗余计算，例如重复的子查询。 有关详细信息，请参阅此 Azure SQL 数据仓库[博客文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**返回结果时的排序依据优化**|在此版本中，SELECT…ORDER BY 的性能已得到大幅提升。   现在，所有计算节点会将其结果发送到单个计算节点。 此节点会合并结果并将其排序，然后将其返回给用户。  当查询结果集包含大量的行时，通过单个计算节点进行合并可以显著提升性能。 以前，查询执行引擎会在每个计算节点上将结果排序。 然后将结果流式传输到控制节点。 然后，控制节点将合并结果。|
|**PartitionMove 和 BroadcastMove 的数据移动增强**|在 Azure SQL 数据仓库 Gen2 中，ShuffleMove 类型的数据移动步骤使用即时数据移动技术。  有关详细信息，请参阅[性能增强博客文章](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 以相同的即时数据移动技术为后盾。 使用这些类型的数据移动步骤的用户查询可以提升运行性能。 无需更改代码便可利用这些性能改进。|
|**值得注意的 Bug**|Azure SQL 数据仓库不正确版本 - `SELECT @@VERSION` 可能返回不正确的版本 10.0.9999.0。 当前版本的正确版本是 10.0.10106.0。 此 bug 已报告，正在评审。
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|无 | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**虚拟网络服务终结点正式版**|此版本包括公开发布的虚拟网络 (VNet) 服务终结点，适用于所有 Azure 区域中的 Azure SQL 数据仓库。 使用 VNet 服务终结点，可从虚拟网络中的给定子网或子网集隔离与逻辑服务器的连接。 从 VNet 到 Azure SQL 数据仓库的流量始终保留在 Azure 主干网络内。 这种直接路由将优先于通过虚拟设备或本地访问 Internet 流量的任何特定路由。 通过服务终结点访问虚拟网络无需额外付费。 [Azure SQL 数据仓库](https://azure.cn/pricing/details/sql-data-warehouse/)的当前定价模型按原样应用。<br/><br/>在此版本中，我们还允许使用 [Azure Blob 文件系统](/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驱动程序通过 PolyBase 连接到 [Azure Data Lake Storage Gen2](/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 为 Azure 存储提供了分析数据的整个生命周期所需的所有特性。<br/><br/>使用 Polybase 时，也可将数据从以安全方式连接到 VNet 的 Azure 存储导入到 Azure SQL 数据仓库中。 同样，也可通过 Polybase 将数据从 Azure SQL 数据仓库导出到以安全方式连接到 VNet 的 Azure 存储。<br/><br/>有关 Azure SQL 数据仓库中 VNet 服务终结点的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文档](/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/sql-data-warehouse/toc.json)。|
|**自动性能监视（预览版）**|[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)现已发布适用于 Azure SQL 数据仓库的预览版。 查询存储旨在帮助你进行查询性能故障诊断，可以通过跟踪查询、查询计划、运行时统计信息和查询历史记录来监视数据仓库的活动和性能。 查询存储是一组内部存储和动态管理视图 (DMV)，用于执行以下操作：<br/><br/>&bull; &nbsp; 确定和优化排名靠前的资源占用查询<br/>&bull; &nbsp; 识别并改进计划外的工作负荷<br/>&bull; &nbsp; 按统计信息、索引或系统大小（DWU 设置）方面的变化评估查询性能以及对计划的影响<br/>&bull; &nbsp; 查看所有已执行查询的完整查询文本<br/><br/>查询存储包含三个实际的存储：<br/>&bull; &nbsp; 一个计划存储，用于持久保存执行计划信息<br/>&bull; &nbsp; 一个运行时统计信息存储，用于持久保存执行统计信息<br/>&bull; &nbsp; 一个等待统计信息存储，用于持久保存等待统计信息<br/><br/>SQL 数据仓库自动管理这些存储，并提供过去七天存储的查询，不限数量且不额外收费。 启用查询存储很简单，只需运行 ALTER DATABASE T-SQL 语句即可： <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------若要详细了解 Azure SQL 数据仓库中的查询存储，请参阅[使用查询存储监视性能](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)一文和查询存储 DMV（例如 [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)）。 这是宣告发布情况的[博客文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)。|
|**列存储后台合并**|默认情况下，Azure SQL 数据仓库 (Azure SQL DW) 使用称为[行组](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微分区以列格式存储数据。 有时候，由于构建索引或加载数据时的内存约束，行组可能会被压缩为小于最佳大小 100 万行。 行组还可能会由于删除而产生碎片。 小的或碎片化的行组会导致较高的内存消耗以及低效的查询执行。 使用此版本的 Azure SQL DW 时，列存储后台维护任务会将较小的压缩行组进行合并以创建较大的行组，从而更好地利用内存并提高查询执行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**行级别安全性正式版**|Azure SQL 数据仓库 (SQL DW) 现在支持行级别安全性 (RLS)，增加了保护敏感数据的强大功能。 通过引入 RLS，可以实现安全策略以控制对表中行的访问（例如哪些人员可以访问哪些行）。 RLS 支持这种精确的访问控制，而无需重新设计数据仓库。 由于访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，因此 RLS 简化了整体安全模型。 RLS 还消除了引入视图以筛选出用于访问控制管理的行的需求。 我们为所有客户提供此企业级安全功能，而用户无需额外付费。|
|**高级顾问**|其他数据仓库建议和指标使 Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单。 Azure 顾问还提供其他高级性能建议供你参考，包括：<br/><br/>1.自适应缓存 - 在应进行缩放以优化缓存利用率时通知用户。<br/>2.表分配 - 确定何时复制表，以减少数据移动，提高工作负载性能。<br/>3.Tempdb - 了解何时缩放和配置资源类，以减少 tempdb 争用。<br/><br/>数据仓库指标与 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) 深度集成，包括增强的可自定义监视图表，以在概览边栏选项卡中实现近实时指标。 监视使用情况或验证和应用数据仓库建议时，无需离开数据仓库概览边栏选项卡即可访问 Azure Monitor 指标。 此外，推出了新的指标（例如 tempdb 和自适应缓存使用率）作为性能建议的补充。|
|**使用集成顾问进行高级优化**|通过其他数据仓库建议和指标，以及重新设计的提供 Azure Advisor 和 Azure Monitor 的集成体验的门户概述边栏选项卡，Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单了。|
|**加速的数据库恢复 (ADR)**|Azure SQL 数据仓库加速的数据库恢复 (ADR) 现在处于公共预览状态。 ADR 是一项新的 SQL Server 引擎功能，通过完全从头开始重新设计当前的恢复过程，极大地提高了数据库可用性（尤其是在存在长时间运行事务的情况下）。 ADR 的主要优点是快速且一致的数据库恢复和即时事务回滚。|
|**Azure Monitor 诊断日志**|SQL 数据仓库 (SQL DW) 现可通过直接与 Azure Monitor 诊断日志集成来增强对分析工作负载的见解。 这项新功能使开发者可分析长时段内的工作负载行为，并针对查询优化或容量管理作出明智决策。 我们现在通过 [Azure Monitor 诊断日志](/azure-monitor/platform/data-platform?toc=%2Fazure-monitor%2Ftoc.json#logs)引入了外部日志记录过程，这些日志提供了有关数据仓库工作负载的其他见解。 只需单击一下按钮，即可使用 [Log Analytics](/azure-monitor/log-query/log-query-overview) 为历史查询性能故障排除功能配置诊断日志。 Azure Monitor 诊断日志支持通过将日志保存到存储帐户以进行审核来自定义保留期，将日志流式传输到事件中心以获得近实时遥测见解，以及通过日志查询使用 Log Analytics 来分析日志。 诊断日志由数据仓库的遥测视图组成，等效于 SQL 数据仓库最常用的性能故障排除 DMV。 对于此初始版本，我们为以下系统动态管理视图启用了视图：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**列存储内存管理**|随着压缩列存储行组数量的增加，管理这些行组的内部列段元数据所需的内存也会增加。  因此，针对某些列存储动态管理视图 (DMV) 执行的查询和查询性能可能会降级。  此版本中的改进旨在优化这些情况下的内部元数据大小，从而改善此类查询的体验并提高此类查询的性能。|
|**Azure Data Lake Storage Gen2 集成 (GA)**|Azure SQL 数据仓库 (SQL DW) 现在已与 Azure Data Lake Storage Gen2 进行了本机集成。 客户现在使用外部表将数据从 ABFS 加载到 SQL DW。 此功能使客户能够与 Data Lake Storage Gen2 中的 data lake 集成。|
|**值得注意的 Bug**|在 DW2000 等数据仓库上的小资源类中执行到 Parquet 的 CETAS 时发生失败 - 此项修复可正确识别 Create External Table As to Parquet 代码路径中的 null 引用。<br/><br/>在某些 CTAS 操作中可能会丢失标识列值 - CTAS 到另一个表后，可能不会保留标识列的值。 已在[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中报告。<br/><br/>在某些情况下，当查询仍在运行时终止会话会发生内部失败 - 如果在查询仍在运行时终止会话，此项修复会触发 InvalidOperationException。<br/><br/>（部署于 2018 年 11 月）尝试使用 Polybase 从 ADLS (Gen1) 加载多个小文件时，客户体验的性能不佳。 在 AAD 安全令牌验证期间，系统性能出现瓶颈。 通过启用安全令牌的缓存来缓解性能问题。 |
| | |

## <a name="next-steps"></a>后续步骤

- [创建 SQL 数据仓库](./create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息

- [Azure 词汇表](../azure-glossary-cloud-terminology.md)
