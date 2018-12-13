---
title: 将 MySQL 数据库用作 Azure Stack 上的 PaaS | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 MySQL 资源提供程序，并提供 MySQL 数据库即服务。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 06/21/2018
ms.date: 07/20/2018
ms.author: v-junlch
ms.reviewer: jeffgo
ms.openlocfilehash: 8eafaa3a8e56f69c76a777738aadc17997cf8bd2
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52647396"
---
# <a name="use-mysql-databases-on-azure-stack"></a>在 Azure Stack 上使用 MySQL 数据库

你可以部署 MySQL 资源提供程序 API 来使用 Azure Stack 中部署的 MySQL 数据库。 有关资源提供程序 API 的详细信息，请参阅 [Azure Pack MySQL 资源提供程序 REST API 参考](https://msdn.microsoft.com/library/dn528442.aspx)。

MySQL 数据库常用于网站上并且支持许多网站平台。 例如，可以使用 Web 应用平台即服务 (PaaS) 附加产品创建 WordPress 网站。

部署资源提供程序后，可以：

- 使用 Azure 资源管理器部署模板创建 MySQL 服务器和数据库。
- 提供 MySQL 数据库即服务。  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 资源提供程序适配器体系结构

资源提供程序具有以下组件：

- **MySQL 资源提供程序适配器虚拟机 (VM)**，这是运行提供程序服务的 Windows Server VM。
- **资源提供程序**，它处理请求并访问数据库资源。
- **托管 MySQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。 你可以自己创建 MySQL 实例，或提供对外部 MySQL 实例的访问权限。 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)中有一个示例模板，可以用来：

  - 创建 MySQL 服务器。
  - 从 Azure 市场下载并部署 MySQL 服务器。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 必须通过租户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>所需的特权

系统帐户必须拥有以下特权：

- **数据库：** 创建、删除
- **登录名：** 创建、设置、删除、授予、吊销  

## <a name="next-steps"></a>后续步骤

[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!-- Update_Description: wording update -->