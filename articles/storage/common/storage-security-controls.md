---
title: Azure 存储的安全控制
description: 用于评估 Azure 存储的安全控制的清单
services: storage
documentationcenter: ''
author: WenJason
manager: digimobile
ms.service: storage
ms.topic: conceptual
origin.date: 09/04/2019
ms.date: 09/30/2019
ms.author: v-jay
ms.openlocfilehash: b54776bb23644ccd47d591c5b1fb2af5144ec673
ms.sourcegitcommit: 0d07175c0b83219a3dbae4d413f8e012b6e604ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306862"
---
# <a name="security-controls-for-azure-storage"></a>Azure 存储的安全控制

本文介绍 Azure 存储中内置的安全控制。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 |  |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 是 | 请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](storage-service-encryption-customer-managed-keys.md?toc=%2fstorage%2fblobs%2ftoc.json)。|
| 列级加密（Azure 数据服务）| 不适用 |  |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 支持标准的 HTTPS/TLS 机制。  用户也可以先加密数据，然后再将其传输到服务。 |
| 加密的 API 调用| 是 |  |

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 不适用 |  |
| 网络隔离和防火墙支持| 是 | |
| 强制隧道支持| 不适用 |  |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | Azure Monitor 指标现已发布，日志开始推出预览版 |
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志，Azure Monitor 日志记录开始推出预览版  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 身份验证| 是 | Azure Active Directory、共享密钥、共享访问令牌。 |
| 授权| 是 | 支持通过 RBAC、POSIX ACL 和 SAS 令牌进行授权 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 Azure 资源管理器 API 进行资源提供程序版本控制 |
