---
title: Azure CLI 示例 - 应用服务 | Azure
description: Azure CLI 示例 - 应用服务
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
origin.date: 12/12/2017
ms.date: 01/02/2018
ms.author: v-yiso
ms.custom: mvc
ms.openlocfilehash: 51d2e221b695ee704b19d91ec3884de0a9df44bc
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52651871"
---
# <a name="azure-cli-samples"></a>Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [创建 Web 应用并使用 FTP 部署文件](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2%2ftoc.json)| 创建 Azure Web 应用并使用 FTP 向其部署文件。 |
| [从 GitHub 创建 Web 应用并部署代码](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2ftoc.json)| 从公共 GitHub 存储库创建 Azure Web 应用并部署代码。 |
| [从 GitHub 使用连续部署创建 Web 应用](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2ftoc.json)| 从拥有的 GitHub 存储库通过连续发布创建 Azure Web 应用。 |
| [从本地 Git 存储库创建 Web 应用并部署代码](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2ftoc.json) | 从本地 Git 存储库创建 Azure Web 应用并配置代码推送。 |
| [创建 Web 应用并将代码部署到过渡环境](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2ftoc.json) | 使用用于暂存代码更改的部署槽创建 Azure Web 应用。 |
|**配置应用**||
| [将自定义域映射到 Web 应用](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2ftoc.json)| 创建 Azure Web 应用并将自定义域名映射到它。 |
| [将自定义 SSL 证书绑定到 Web 应用](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2ftoc.json)| 创建 Azure Web 应用并将自定义域名的 SSL 证书绑定到它。 |
|**缩放应用**||
| [手动缩放 Web 应用](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球 Web 应用](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2ftoc.json) | 在两个不同地理区域中创建两个 Azure Web 应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将 Web 应用连接到 SQL 数据库](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2ftoc.json)| 创建 Azure Web 应用和 SQL 数据库，并将数据库连接字符串添加到应用设置。 |
| [将 Web 应用连接到存储帐户](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2ftoc.json)| 创建 Azure Web 应用和存储帐户，并将存储连接字符串添加到应用设置。 |
| [将 Web 应用连接到 Redis 缓存](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用和 Redis 缓存，并将 Redis 连接详细信息添加到应用设置。 |
| [将 Web 应用连接到 Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用和 Cosmos DB，然后将 Cosmos DB 连接详细信息添加到应用设置。 |
|**备份和还原应用**||
| [备份 Web 应用](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用，并为其创建一次性备份。 |
| [为 Web 应用创建计划备份](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用，并为其创建计划备份。 |
| [从备份中还原 Web 应用](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2ftoc.json) | 从备份中还原 Azure Web 应用。 |
|**监视应用**||
| [使用 Web 服务器日志监视 Web 应用](./scripts/app-service-cli-monitor.md?toc=%2fcli%2ftoc.json) | 创建 Azure Web 应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |