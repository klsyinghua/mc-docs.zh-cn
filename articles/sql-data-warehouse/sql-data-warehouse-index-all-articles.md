---
title: "有关 SQL 数据仓库服务的所有主题 | Azure"
description: "位于 /azure/ 的有关 Azure SQL 数据仓库的所有主题的表格，包括标题和说明。"
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
wacn.date: 
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2c4ee90387d280f15b2f2ed656f7d4862ad80901
ms.openlocfilehash: cd16554bdb5e2f93a77a0b6c9b34ac86455afa9e
ms.lasthandoff: 04/28/2017

---

# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>有关 Azure SQL 数据仓库服务的所有主题

本主题列出的每个主题都可以直接应用于 Azure **SQL 数据仓库** 服务。 您可以使用 **Ctrl+F**来搜索此网页的关键字，以便查找当前感兴趣的主题。

## <a name="new"></a>新建
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 1 |[SQL 数据仓库备份](sql-data-warehouse-backups.md) |了解 SQL 数据仓库的内置数据库备份，该功能可以将 Azure SQL 数据仓库还原到某个还原点或另一地理区域。 |

## <a name="updated-articles-sql-data-warehouse"></a>更新的文章，SQL 数据仓库
本部分列出最近做了重大更新的文章。 每篇更新的文章都会显示大致的代码段，说明添加的 Markdown 文本。 这些文章是在 **2016-08-22** 到 **2016-10-05** 期间更新的。

| &nbsp; | 文章 | 更新的文本、代码片段 | 更新时间 |
| ---:|:--- |:--- |:--- |
| 2 |[将数据从 Azure Blob 存储载入 SQL 数据仓库 (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- 若要跟踪字节和文件 SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[SQL 数据仓库还原](sql-data-warehouse-restore-database-overview.md) |** 可否还原已暂停的数据仓库？** 若要还原已暂停的数据仓库，首先需要将其重新联机。 数据仓库重新联机后，有 7 天的恢复点可供选择。 ** 还原到异地冗余区域** 如果使用异地冗余存储，则可将数据仓库还原到另一地理区域的配对数据中心。 从上次的每日备份还原数据仓库。 ** 还原时间线** 可以将数据库还原到过去 7 天的任何还原点。 快照 4 到 8 小时启动一次，可供使用 7 天。 快照超过 7 天将过期，其还原点不再可用。 ** 还原成本** 已还原的数据仓库的存储费用按 Azure 高级存储费率计算。 如果暂停还原的数据仓库，则存储费用按 Azure 高级存储费率计算。 暂停的优点是不会产生费用 |2016-09-29 |

## <a name="get-started"></a>入门
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 4 |[对 Azure SQL 数据仓库进行身份验证](sql-data-warehouse-authentication.md) |对 Azure SQL 数据仓库进行的 Azure Active Directory (AAD) 和 SQL Server 身份验证。 |
| 5 |[Azure SQL 数据仓库最佳实践](sql-data-warehouse-best-practices.md) |开发 Azure SQL 数据仓库解决方案时应了解的建议和最佳实践。 这些内容可帮助你取得成功。 |
| 6 |[Azure SQL 数据仓库的驱动程序](sql-data-warehouse-connection-strings.md) |SQL 数据仓库的连接字符串和驱动程序 |
| 7 |[连接到 Azure SQL 数据仓库](sql-data-warehouse-connect-overview.md) |如何查找连接到 Azure SQL 数据仓库所需的服务器名称和连接字符串 |
| 8 |[查询 Azure SQL 数据仓库 (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |使用 sqlcmd 命令行实用工具查询 Azure SQL 数据仓库。 |
| 9 |[使用 Transact-SQL (TSQL) 创建 SQL 数据仓库数据库](sql-data-warehouse-get-started-create-database-tsql.md) |了解如何使用 TSQL 创建 Azure SQL 数据仓库 |
| 10 |[在 SQL 数据仓库中使用 PolyBase 加载数据](sql-data-warehouse-get-started-load-with-polybase.md) |了解什么是 PolyBase，以及如何将其用于数据仓库方案。 |
| 11 |[创建 Azure SQL 数据仓库](sql-data-warehouse-get-started-provision.md) |了解如何在 Azure 门户预览中创建 Azure SQL 数据仓库 |
| 12 |[使用 PowerShell 创建 SQL 数据仓库](sql-data-warehouse-get-started-provision-powershell.md) |使用 PowerShell 创建 SQL 数据仓库 |
| 13 |[查询 Azure SQL 数据仓库 (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |使用 Visual Studio 查询 SQL 数据仓库。 |

## <a name="develop"></a>开发
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 14 |[优化 SQL 数据仓库的事务](sql-data-warehouse-develop-best-practices-transactions.md) |在 Azure SQL 数据仓库中编写有效事务更新的最佳做法指南 |
| 15 |[SQL 数据仓库中的并发性和工作负荷管理](sql-data-warehouse-develop-concurrency.md) |在开发解决方案之前，了解 SQL 数据仓库中的并发性和工作负荷管理。 |
| 16 |[SQL 数据仓库中的 Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md) |有关在开发解决方案时使用 Azure SQL 数据仓库中的 create table as select (CTAS) 语句编写代码的技巧。 |
| 17 |[SQL 数据仓库中的动态 SQL](sql-data-warehouse-develop-dynamic-sql.md) |有关在开发解决方案时使用 Azure SQL 数据仓库中的动态 SQL 的技巧。 |
| 18 |[SQL 数据仓库中的 Group By 选项](sql-data-warehouse-develop-group-by-options.md) |有关在开发解决方案时实现 Azure SQL 数据仓库中的 Group By 选项的技巧。 |
| 19 |[在 SQL 数据仓库中使用标签检测查询](sql-data-warehouse-develop-label.md) |有关在开发解决方案时于 Azure SQL 数据仓库中使用标签检测查询的技巧。 |
| 20 |[SQL 数据仓库中的循环](sql-data-warehouse-develop-loops.md) |有关在开发解决方案时使用 Azure SQL 数据仓库中的 Transact-SQL 循环和替换游标的技巧。 |
| 21 |[SQL 数据仓库中的存储过程](sql-data-warehouse-develop-stored-procedures.md) |有关在开发解决方案时实现 Azure SQL 数据仓库中的存储过程的技巧。 |
| 22 |[SQL 数据仓库中的事务](sql-data-warehouse-develop-transactions.md) |有关在开发解决方案时实现 Azure SQL 数据仓库中的事务的技巧。 |
| 23 |[SQL 数据仓库中用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md) |有关在开发解决方案时使用 Azure SQL 数据仓库中的 Transact-SQL 架构的技巧。 |
| 24 |[在 SQL 数据仓库中分配变量](sql-data-warehouse-develop-variable-assignment.md) |有关在开发解决方案时于 Azure SQL 数据仓库中分配 Transact-SQL 变量的技巧。 |
| 25 |[SQL 数据仓库中的视图](sql-data-warehouse-develop-views.md) |有关在开发解决方案时使用 Azure SQL 数据仓库中的 Transact-SQL 视图的技巧。 |
| 26 |[SQL 数据仓库的设计决策和编程技术](sql-data-warehouse-overview-develop.md) |SQL 数据仓库的开发概念、设计决策、建议和编程技术。 |

## <a name="manage"></a>管理
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 27 |[管理 Azure SQL 数据仓库中的计算能力（概述）](sql-data-warehouse-manage-compute-overview.md) |Azure SQL 数据仓库中的性能横向扩展功能。 通过调整 DWU 数目进行横向扩展，或者通过暂停和恢复计算资源来节省成本。 |
| 28 |[管理 Azure SQL 数据仓库中的计算能力（Azure 门户预览）](sql-data-warehouse-manage-compute-portal.md) |用于管理计算能力的 Azure 预览门户任务。 通过调整 DWU 缩放计算资源。 或者，暂停和恢复计算资源来节省成本。 |
| 29 |[管理 Azure SQL 数据仓库中的计算能力 (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |用于管理计算能力的 PowerShell 任务。 通过调整 DWU 缩放计算资源。 或者，暂停和恢复计算资源来节省成本。 |
| 30 |[管理 Azure SQL 数据仓库中的计算能力 (REST)](sql-data-warehouse-manage-compute-rest-api.md) |用于管理计算能力的 PowerShell 任务。 通过调整 DWU 缩放计算资源。 或者，暂停和恢复计算资源来节省成本。 |
| 31 |[管理 Azure SQL 数据仓库中的计算能力 (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |可通过调整 DWU 来提高性能的 Transact-SQL (T-SQL) 任务。 通过在非高峰期缩减性能来节省成本。 |
| 32 |[使用 DMV 监视工作负荷](sql-data-warehouse-manage-monitor.md) |了解如何使用 DMV 监视工作负荷。 |
| 33 |[在 Azure SQL 数据仓库中管理数据库](sql-data-warehouse-overview-manage.md) |管理 SQL 数据仓库数据库的概述。 包括管理工具、DWU 和向外扩展性能，对查询性能进行故障排除，建立良好的安全策略，以及从数据损坏或区域中断还原数据库。 |
| 34 |[在 Azure SQL 数据仓库中监视用户查询](sql-data-warehouse-overview-manage-user-queries.md) |在 Azure SQL 数据仓库中监视用户查询时的注意事项、最佳实践和任务的概述 |
| 35 |[SQL 数据仓库还原](sql-data-warehouse-restore-database-overview.md) |在 Azure SQL 数据仓库中恢复数据库时的数据库还原选项概述。 |
| 36 |[还原 Azure SQL 数据仓库（门户）](sql-data-warehouse-restore-database-portal.md) |用于还原 SQL 数据仓库的 Azure 门户预览任务。 |
| 37 |[还原 Azure SQL 数据仓库 (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |用于还原 SQL 数据仓库的 PowerShell 任务。 |
| 38 |[还原 Azure SQL 数据仓库 (REST API)](sql-data-warehouse-restore-database-rest-api.md) |用于还原 SQL 数据仓库的 REST API 任务。 |

## <a name="tables-and-indexes"></a>表和索引
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 39 |[SQL 数据仓库中表的数据类型](sql-data-warehouse-tables-data-types.md) |Azure SQL 数据仓库表的数据类型入门。 |
| 40 |[在 SQL 数据仓库中分布表](sql-data-warehouse-tables-distribute.md) |开始在 Azure SQL 数据仓库中分布表。 |
| 41 |[为 SQL 数据仓库中的表编制索引](sql-data-warehouse-tables-index.md) |Azure SQL 数据仓库中的表索引入门。 |
| 42 |[概述 SQL 数据仓库中的表](sql-data-warehouse-tables-overview.md) |Azure SQL 数据仓库表入门。 |
| 43 |[对 SQL 数据仓库中的表进行分区](sql-data-warehouse-tables-partition.md) |Azure SQL 数据仓库中的表分区入门。 |
| 44 |[管理 SQL 数据仓库中表的统计信息](sql-data-warehouse-tables-statistics.md) |Azure SQL 数据仓库中表的统计信息入门。 |
| 45 |[SQL 数据仓库中的临时表](sql-data-warehouse-tables-temporary.md) |开始使用 Azure SQL 数据仓库中的临时表。 |

## <a name="integrate"></a>集成
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 46 |[将 Azure 流分析与 SQL 数据仓库配合使用](sql-data-warehouse-integrate-azure-stream-analytics.md) |有关在开发解决方案时将 Azure 流分析与 Azure SQL 数据仓库配合使用的技巧。 |
| 47 |[在 SQL 数据仓库中利用其他服务](sql-data-warehouse-overview-integrate.md) |用于集成 SQL 数据仓库的工具以及提供相应解决方案的合作伙伴。 |

## <a name="load"></a>加载
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 48 |[将数据从 Azure Blob 存储载入 SQL 数据仓库 (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |了解如何使用 PolyBase 将数据从 Azure Blob 存储载入 SQL 数据仓库。 将公共数据中的一些表载入 Contoso 零售数据仓库架构。 |
| 49 |[将数据从 SQL Server 载入 Azure SQL 数据仓库 (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |使用 bcp 将数据从 SQL Server 导出到平面文件，使用 AZCopy 将数据导入 Azure Blob 存储，使用 PolyBase 将数据引入 Azure SQL 数据仓库。 |
| 50 |[将数据从 SQL Server 载入 Azure SQL 数据仓库（平面文件）](sql-data-warehouse-load-from-sql-server-with-bcp.md) |对于少量的数据，可以使用 bcp 将数据从 SQL Server 导出到平面文件，然后将数据直接导入 Azure SQL 数据仓库。 |
| 51 |[将数据从 SQL Server 载入 Azure SQL 数据仓库 (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |演示如何创建 SQL Server Integration Services (SSIS) 包，以便将数据从各种数据源移动到 SQL 数据仓库。 |
| 52 |[在 SQL 数据仓库中使用 PolyBase 加载数据](sql-data-warehouse-load-from-sql-server-with-polybase.md) |使用 bcp 将数据从 SQL Server 导出到平面文件，使用 AZCopy 将数据导入 Azure Blob 存储，使用 PolyBase 将数据引入 Azure SQL 数据仓库。 |
| 53 |[在 SQL 数据仓库中使用 PolyBase 的指南](sql-data-warehouse-load-polybase-guide.md) |有关在 SQL 数据仓库方案中使用 PolyBase 的指导原则和建议。 |
| 54 |[将示例数据载入 SQL 数据仓库](sql-data-warehouse-load-sample-databases.md) |将示例数据载入 SQL 数据仓库 |
| 55 |[使用 bcp 加载数据](sql-data-warehouse-load-with-bcp.md) |了解什么是 bcp，以及如何将它用于数据仓库方案。 |
| 56 |[将数据载入 Azure SQL 数据仓库](sql-data-warehouse-overview-load.md) |了解将数据载入 SQL 数据仓库的常见方案。 这些常见方案包括使用 PolyBase、Azure Blob 存储、平面文件以及磁盘寄送。 也可使用第三方工具。 |

## <a name="migrate"></a>迁移
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 57 |[将 SQL 代码迁移到 SQL 数据仓库](sql-data-warehouse-migrate-code.md) |有关在开发解决方案时将 SQL 代码迁移到 Azure SQL 数据仓库的技巧。 |
| 58 |[迁移数据](sql-data-warehouse-migrate-data.md) |有关在开发解决方案时将数据迁移到 Azure SQL 数据仓库的技巧。 |
| 59 |[数据仓库迁移实用程序（预览版）](sql-data-warehouse-migrate-migration-utility.md) |迁移到 SQL 数据仓库。 |
| 60 |[将架构迁移到 SQL 数据仓库](sql-data-warehouse-migrate-schema.md) |有关在开发解决方案时将架构迁移到 Azure SQL 数据仓库的技巧。 |
| 61 |[将解决方案迁移到 SQL 数据仓库](sql-data-warehouse-overview-migrate.md) |有关将解决方案转移到 Azure SQL 数据仓库平台的迁移指南。 |


## <a name="reference"></a>引用
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 62 |[SQL 数据仓库参考主题](sql-data-warehouse-overview-reference.md) |SQL 数据仓库的参考内容链接。 |
| 63 |[适用于 SQL 数据仓库的 PowerShell cmdlet 和 REST API](sql-data-warehouse-reference-powershell-cmdlets.md) |了解 Azure SQL 数据仓库的最常用 PowerShell cmdlet，包括如何暂停和恢复数据库。 |
| 64 |[语言元素](sql-data-warehouse-reference-tsql-language-elements.md) |用于 SQL 数据仓库的 Transact-SQL 语言元素的参考内容链接列表。 |
| 65 |[Transact-SQL 主题](sql-data-warehouse-reference-tsql-statements.md) |SQL 数据仓库使用的 Transact-SQL 主题参考内容的链接。 |
| 66 |[系统视图](sql-data-warehouse-reference-tsql-system-views.md) |SQL 数据仓库的系统视图内容链接。 |

## <a name="security"></a>“安全”
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 67 |[SQL 数据仓库中的透明数据加密 (TDE) 入门](sql-data-warehouse-encryption-tde.md) |SQL 数据仓库中的透明数据加密 (TDE) |
| 68 |[透明数据加密 (TDE) 入门](sql-data-warehouse-encryption-tde-tsql.md) |SQL 数据仓库 (T-SQL) 中的透明数据加密 (TDE) |
| 69 |[保护 SQL 数据仓库中的数据库](sql-data-warehouse-overview-manage-security.md) |有关在开发解决方案时保护 Azure SQL 数据仓库中的数据库的技巧。 |

## <a name="miscellaneous"></a>其他
| &nbsp; | 标题 | 说明 |
| ---:|:--- |:--- |
| 70 |[安装适用于 SQL 数据仓库的 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md) |安装适用于 Azure SQL 数据仓库的 Visual Studio 和 SQL Server 开发工具 (SSDT) |
| 71 |[迁移到高级存储的详细信息](sql-data-warehouse-migrate-to-premium-storage.md) |将 SQL 数据仓库迁移到高级存储的说明 |
| 72 |[SQL 数据仓库容量限制](sql-data-warehouse-service-capacity-limits.md) |SQL 数据仓库的连接、数据库、表和查询的最大值。 |
| 73 |[排查 Azure SQL 数据仓库问题](sql-data-warehouse-troubleshoot.md) |排查 Azure SQL 数据仓库问题。 |