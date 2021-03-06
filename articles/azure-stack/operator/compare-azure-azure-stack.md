---
title: Azure Stack 和 Azure 的比较 | Microsoft Docs
description: 了解 Microsoft 如何在一个 Azure 生态系统中提供 Azure 和 Azure Stack 系列的服务
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
origin.date: 05/03/2019
ms.date: 01/13/2020
ms.author: v-jay
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 9db9d9badd77c74002aa6599b4857d2e3178d0d5
ms.sourcegitcommit: 166549d64bbe28b28819d6046c93ee041f1d3bd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737736"
---
# <a name="differences-between-azure-azure-stack-and-azure-stack-hci"></a>Azure、Azure Stack 与 Azure Stack HCI 之间的差异

Microsoft 在一个 Azure 生态系统中提供 Azure 和 Azure Stack 系列的服务。 无论企业使用 Azure 还是本地资源，都可以结合 Azure 资源管理器使用相同的应用程序模型、自助门户和 API 来提供基于云的功能。

本文介绍 Azure、Azure Stack 和 Azure Stack HCI 的功能，并提供常见方案的建议，以帮助你做出最佳的选择来为组织交付基于 Microsoft 云的服务。

![Azure 生态系统概述](./media/compare-azure-azure-stack/azure-family.png)

## <a name="azure"></a>Azure

Azure 包括一系列不断扩增的云服务，可帮助组织解决业务难题。 它允许你自由使用偏好的工具和框架，在大规模全球网络中构建、管理和部署应用程序。

Azure 提供 100 多个可在 4 个区域使用的服务。 有关 Azure 服务的最新列表，请参阅[*各区域可用的产品*](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=all)。 Azure 中提供的服务按类别列出，同时会指出是否已推出正式版，或者以预览版提供。

有关全球 Azure 服务的详细信息，请参阅 [Azure 入门](https://docs.azure.cn/#pivot=get-started&panel=get-started1)。

## <a name="azure-stack"></a>Azure Stack

Azure Stack 是 Azure 的扩展，它将云计算的敏捷性和创新引入到了本地环境。 可以使用部署在本地的 Azure Stack 来提供 Azure 一致性服务，这些服务可以连接到 Internet（和 Azure），也可以位于未连接 Internet 的离线环境中。 Azure Stack 使用的底层技术与 Stack 相同，包括基础结构即服务 (IaaS)、软件即服务 (SaaS) 和可选平台即服务 (PaaS) 功能的核心组件，其中包括：

- 适用于 Windows 和 Linux 的 Azure VM
- Azure Web 应用和 Functions
- Azure Key Vault
- Azure Resource Manager
- Azure 市场
- 容器
- 管理工具（计划、套餐、RBAC 等）

Azure Stack 的 PaaS 功能是可选的，因为 Azure Stack 不是由 Microsoft 操作，而是由客户操作。 这意味着，如果你已准备好从最终用户抽离底层基础结构和流程，则可以向最终用户提供任何 PaaS 服务。 但是，Azure Stack 包含多个可选的 PaaS 服务提供程序，包括应用服务、SQL 数据库和 MySQL 数据库。 这些产品以资源提供程序的形式交付，因此已经是多租户性质、会通过标准 Azure Stack 更新不断更新、显示在 Azure Stack 门户中，并与 Azure Stack 妥善集成。

除了上述资源提供程序以外，还有其他 PaaS 服务可供使用，经测试它们可以用作 IaaS 中运行的[基于 Azure 资源管理器模板的解决方案](https://github.com/Azure/AzureStack-QuickStart-Templates)，但是，Azure Stack 操作员可将其作为 PaaS 服务提供给用户。这些服务包括：

- Service Fabric
- 以太坊区块链
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Azure Stack 的示例用例：

- 财务建模
- 临床诊断和理赔数据
- IoT 设备分析
- 零售分类优化
- 供应链优化
- 工业 IoT
- 预见性维护
- 智能城市
- 公民参与

有关 Azure Stack 的详细信息，请参阅[什么是 Azure Stack](azure-stack-overview.md)。

## <a name="azure-stack-hci"></a>Azure Stack HCI

使用 [Azure Stack HCI](azure-stack-hci-overview.md) 解决方案可以通过超融合基础结构 (HCI) 解决方案在本地运行虚拟机并轻松连接到 Azure。 根据法规或技术要求，在本地使用一致的 Azure 服务来构建和运行云应用程序。 除了在本地运行虚拟化的应用程序以外，Azure Stack HCI 还可让你更换与整合过时的服务器基础结构，并使用 Windows 管理中心连接到 Azure 云服务。

Azure Stack HCI 提供经过验证的，采用 Hyper-V、存储空间直通和 Windows Server 2019 软件定义的数据中心 (SDDC) 技术的 HCI 解决方案。 Windows 管理中心用于对 Azure 服务进行管理和集成式访问，例如：

- Azure 备份
- Azure Site Recovery
- Azure Monitor 和更新

有关可与 Azure Stack HCI 连接的 Azure 服务的更新列表，请参阅[将 Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/azure-hybrid-services/index)。

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的示例用例
- 远程或分支机构系统
- 数据中心整合
- 虚拟桌面基础结构
- 业务关键型基础结构
- 低成本存储
- 云中的高可用性和灾难恢复
- SQL Server 等企业应用

请访问 [Azure Stack HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci/)，以查看 Microsoft 合作伙伴目前提供的 70 多种 Azure Stack HCI 解决方案。

## <a name="next-steps"></a>后续步骤

[Azure Stack 管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack 管理门户](azure-stack-manage-portals.md)
