---
title: Azure Database for PostgreSQL - 单一服务器中的监视
description: 本文介绍了 Azure Database for PostgreSQL - 单一服务器中的监视功能。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 06/19/2019
ms.date: 08/05/2019
ms.openlocfilehash: 76d2250690f441f1369c503a940b9b51685b232a
ms.sourcegitcommit: 193f49f19c361ac6f49c59045c34da5797ed60ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68732369"
---
# <a name="monitor-azure-database-for-postgresql---single-server"></a>监视 Azure Database for PostgreSQL - 单一服务器
监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for PostgreSQL 提供各种监视选项，帮助用户深入了解服务器的行为。

## <a name="metrics"></a>指标
Azure Database for PostgreSQL 提供了各种指标来帮助用户深入了解为 PostgreSQL 服务器提供支持的资源的行为。 每项指标以一分钟为频率发出，历史记录长达 30 天。 可针对指标配置警报。 有关分步指南，请参阅[如何设置警报](howto-alert-on-metric.md)。 其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>指标列表
这些指标适用于 Azure Database for PostgreSQL：

|指标|指标显示名称|计价单位|说明|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|io_consumption_percent|IO 百分比|百分比|使用的 IO 百分比。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
|storage_limit|存储限制|字节|此服务器的最大存储。|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|所用服务器日志存储占服务器最大服务器日志存储的百分比。|
|serverlog_storage_usage|服务器日志已用的存储量|字节|使用的服务器日志存储量。|
|serverlog_storage_limit|服务器存储空间上限|字节|此服务器的最大服务器日志存储。|
|active_connections|活动连接数|计数|服务器的活动连接数。|
|connections_failed|失败的连接数|计数|服务器的失败连接数。|
|network_bytes_egress|网络传出|字节|跨活动连接的网络传出。|
|network_bytes_ingress|网络传入|字节|跨活动连接的网络传入。|
|backup_storage_used|使用的备份存储|字节|已使用的备份存储量。|
|pg_replica_log_delay_in_bytes|副本的最大滞后时间|字节|主服务器与滞后时间最长的副本之间的滞后时间（以字节为单位）。 此指标仅适用于主服务器。|
|pg_replica_log_delay_in_seconds|副本滞后时间|秒|自上次重放事务以来所经历的时间。 此指标仅适用于副本服务器。|

## <a name="server-logs"></a>服务器日志
可以在服务器上启用日志记录。 这些日志也可通过 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)、事件中心和存储帐户中的 Azure 诊断日志获得。 若要了解有关日志记录的详细信息，请访问[服务器日志](concepts-server-logs.md)页。

## <a name="query-store"></a>查询存储
[查询存储](concepts-query-store.md)可以随着时间的推移跟踪查询性能，包括查询运行时统计信息和等待事件。 此功能将查询运行时性能信息保留在 query_store 架构下名为 azure_sys 的一个系统数据库中  。 你可以通过各种配置旋钮控制数据的收集和存储。

## <a name="next-steps"></a>后续步骤
- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-alert-on-metric.md)。
- 若要深入了解如何使用 Azure 门户、REST API 或 CLI 访问和导出指标，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
