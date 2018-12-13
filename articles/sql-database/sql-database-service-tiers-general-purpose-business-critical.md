---
title: Azure SQL 数据库 - 常规用途和业务关键 | Microsoft Docs
description: 本文讨论 vCore 购买模型中的常规用途和业务关键服务层。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sashan, moslake
manager: digimobile
origin.date: 10/15/2018
ms.date: 12/03/2018
ms.openlocfilehash: 9e2677765e5daf2fba4c89757655f94728d9ee21
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675272"
---
# <a name="general-purpose-and-business-critical-service-tiers"></a>“常规用途”和“业务关键”服务层

本文讨论基于 vCore 购买模型中的常规用途和业务关键服务层的存储和备份注意事项。

## <a name="data-and-log-storage"></a>数据和日志存储

请注意以下几点：

- 分配的存储由数据文件 (MDF) 和日志文件 (LDF) 使用。
- 每个单一数据库计算大小支持一个最大数据库大小，默认最大大小为 32 GB。
- 配置所需的单一数据库大小（MDF 大小）时，系统会自动额外添加 30% 的存储来支持 LDF
- 可以选择介于 10 GB 与受支持最大值之间的任何单一数据库大小
  - 对于标准存储，可以按 10 GB 的增量增大或减小大小
  - 对于高级存储，可以按 250 GB 的增量增大或减小大小
- 在“常规用途”服务层中，`tempdb` 使用附加的 SSD，此存储成本包含在 vCore 价格中。
- 在“业务关键”服务层中，`tempdb` 与 MDF 和 LDF 文件共享附加的 SSD，tempDB 存储成本已包含在 vCore 价格中。

> [!IMPORTANT]
> 需要支付分配给 MDF 和 LDF 的总存储费用。

若要监视 MDF 和 LDF 的当前总大小，请使用 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储，以支持 SQL 数据库的时间点还原 (PITR) 和[长期保留 (LTR)](sql-database-long-term-retention.md) 功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。

- **PITR**：自动将各个数据库备份复制到 [RA-GRS 存储](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 创建新备份时，存储大小动态递增。  存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 存储消耗量取决于数据库变化率和保留期。 可单独为每个数据库配置 7 到 35 天的保留期。 提供与 1 倍数据库大小相等的最小存储量，不收取额外费用。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：SQL 数据库提供相应的选项用于将完整备份的长期保留期配置为最多 10 年。 如果启用了 LTR 策略，则这些备份将自动存储在 RA-GRS 存储中，但你可以控制复制备份的频率。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 此配置将定义要为 LTR 备份使用多少存储。 可以使用 LTR 定价计算器来估算 LTR 存储成本。 有关详细信息，请参阅 [长期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解适用于常规用途和业务关键服务层中的单一数据库的特定计算大小和存储大小选项，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- 若要详细了解适用于常规用途和业务关键服务层中的弹性池的特定计算大小和存储大小选项，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。