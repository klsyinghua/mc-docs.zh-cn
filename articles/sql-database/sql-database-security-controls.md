---
title: Azure SQL 数据库的安全控制
description: 用于评估 Azure SQL 数据库的安全控制的清单
services: sql-database
author: WenJason
manager: digimobile
ms.service: load-balancer
ms.topic: conceptual
origin.date: 09/04/2019
ms.date: 09/30/2019
ms.author: v-jay
ms.openlocfilehash: bbb340c6d736b13e8dc63afdd93fa4d559631c70
ms.sourcegitcommit: 5c3d7acb4bae02c370f6ba4d9096b68ecdd520dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263064"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL 数据库的安全控制

本文介绍 Azure SQL 数据库中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL 数据库同时包含[单一数据库](sql-database-single-index.yml)和[托管实例](sql-database-managed-instance.md)。 除非另有说明，否则以下条目适用于这两种产品/服务。

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于[单一数据库](sql-database-single-index.yml)。 |
| Azure 虚拟网络注入支持| 是 | 仅适用于[托管实例](sql-database-managed-instance.md)。 |
| 网络隔离和防火墙支持| 是 | 数据库级别和服务器级别的防火墙。 网络隔离仅适用于[托管实例](sql-database-managed-instance.md)。 |
| 强制隧道支持| 是 | 通过 [ExpressRoute](../expressroute/index.yml) VPN [托管的实例](sql-database-managed-instance.md)。 |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释|
|---|---|--|
| Azure 监视支持，如 Log Analytics 或 Application Insights| 是 | SecureSphere 是 Imperva 的 SIEM 解决方案，也通过 [Azure 存储](/storage/)集成（通过 [SQL 审核](sql-database-auditing.md)）获得支持。 |
| 控制平面和管理平面日志记录和审核| 是 | 仅对某些事件为“是” |
| 数据平面日志记录和审核 | 是 | 通过 [SQL 审核](sql-database-auditing.md) |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 身份验证| 是 | Azure Active Directory (Azure AD) |
| 授权| 是 | 无 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 称为“使用中加密”，详见 [Always Encrypted](sql-database-always-encrypted.md) 一文中的说明。 服务端加密使用[透明数据加密](transparent-data-encryption-azure-sql.md)。|
| 传输中加密：<ul><li>Azure ExpressRoute 加密</li><li>虚拟网络中的加密</li><li>虚拟网络之间的加密</ul>| 是 | 使用 HTTPS。 |
| 加密密钥处理，如 CMK 或 BYOK| 是 | 提供服务托管和客户托管密钥处理。 后者通过 [Azure Key Vault](../key-vault/index.yml) 提供。 |
| Azure 数据服务提供的列级加密| 是 | 通过 [Always Encrypted](sql-database-always-encrypted.md) 进行。 |
| 加密的 API 调用| 是 | 使用 HTTPS/SSL。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 配置管理支持（例如，配置的版本控制）| 否  | 无 |

## <a name="additional-security-controls-for-sql-database"></a>SQL 数据库的其他安全控制

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅 [SQL 漏洞评估服务可帮助你识别数据库漏洞](sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](sql-database-data-discovery-and-classification.md)。 |
| 检测：威胁检测 | 是 | 请参阅 [Azure SQL 数据库的高级威胁防护](sql-database-threat-detection-overview.md)。 |