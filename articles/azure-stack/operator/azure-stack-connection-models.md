---
title: Azure Stack 集成系统连接模型 | Microsoft Docs
description: 确定 Azure Stack 集成系统的连接模型和其他部署规划决策。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/02/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: e21b8d05cca4be4bb19a4dd8c838ba21603d9089
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75335778"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack 集成系统连接模型
如果你有兴趣购买 Azure Stack 集成系统，则需要了解[几个数据中心集成注意事项](azure-stack-datacenter-integration.md)，以便 Azure Stack 部署确定系统将如何适应数据中心。 此外，还需要确定如何将 Azure Stack 集成到混合云环境。 本文概述了这些主要决策，包括 Azure 连接模型、标识存储选项和计费模型选项。

如果你决定购买一个集成系统，原始设备制造商 (OEM) 硬件供应商可帮助更详细地指导你完成规划过程。 OEM 硬件供应商还会执行实际部署。

## <a name="choose-an-azure-stack-deployment-connection-model"></a>选择 Azure Stack 部署连接模型
可以选择在连接到 Internet（和 Azure）时还是断开连接时部署 Azure Stack。 若要从 Azure Stack（包括 Azure Stack 和 Azure 之间的混合方案）获得最大效益，请在连接到 Azure 时进行部署。 此选择定义了哪些选项可用于标识存储（Azure Active Directory 或 Active Directory 联合身份验证服务）和计费模型（基于“提前支付”计费或基于容量计费），如以下图和表中所总结：

![Azure Stack 部署和计费方案](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> 这是关键决策点！ 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 以后，除非重新部署整个系统，否则将无法更改此设置。  


|选项|已连接到 Azure|已与 Azure 断开连接|
|-----|:-----:|:-----:|
|Azure AD|![支持](media/azure-stack-connection-models/check.png)| |
|AD FS|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|基于使用的计费|![支持](media/azure-stack-connection-models/check.png)| |
|基于容量的计费|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|许可| 企业协议或云解决方案提供商 | 企业协议 |
|修补和更新|可以直接从 Internet 将更新包下载到 Azure Stack |  必须<br><br>还需要可移动媒体<br> 和独立的连接设备 |
| 注册 | 自动 | 必须<br><br>还需要可移动媒体<br> 和独立的连接设备 |

选定 Azure Stack 部署要使用的 Azure 连接模型后，必须对标识存储和计费方法做出其他与连接相关的决策。

## <a name="next-steps"></a>后续步骤

[已连接 Azure 的 Azure Stack 部署决策](azure-stack-connected-deployment.md)

[与 Azure 断开连接的 Azure Stack 部署决策](azure-stack-disconnected-deployment.md)
