---
title: 在 Azure Stack 中创建套餐 | Microsoft Docs
description: 作为云管理员，了解如何在 Azure Stack 中为用户创建套餐。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 09/12/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: efemmano
ms.openlocfilehash: 1e3540c828c1d29e3026ba3460db492843bba4eb
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52656385"
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中创建套餐

[套餐](azure-stack-key-features.md)是提供者提供给用户购买或订阅的一个或多个计划的组合。 本文档介绍如何创建包含[创建的计划](azure-stack-create-plan.md)的套餐。 订阅方可以使用此套餐设置虚拟机。

1. 登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)，选择“+ 创建资源” > “租户套餐 + 计划” > “套餐”。

   ![创建产品](media/azure-stack-create-offer/image01.png)
  
2. 在“新建套餐”下，输入“显示名称”和“资源名称”，然后在“资源组”下选择“新建”或“使用现有项”。 显示名称是产品/服务的友好名称。 此友好名称是用户在订阅套餐时可看到的有关该套餐的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![新建套餐](media/azure-stack-create-offer/image01a.png)
  
3. 选择“基本计划”打开“计划”。 选择要包含在套餐中的计划，然后选择“选择”。 若要创建套餐，请选择“创建”。

   ![选择计划](media/azure-stack-create-offer/image02.png)
  
4. 创建套餐后，可以更改其状态。 必须将产品/服务设为“公共”，用户才能在订阅时获得完整视图。 产品/服务可以是：

   - **公共**：对用户可见。
   - **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
   - **已解除授权**：已对新订阅方关闭。 云管理员可以使用已解除授权来防止将来订阅，但不影响当前订阅方。

   > [!TIP]  
   > 套餐的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

   在套餐的“概述”中，选择“访问性状态”。 选择要使用的状态（例如“公共”），然后选择“保存”。
 
     ![选择状态](media/azure-stack-create-offer/change-stage-1807.png)

     或者，选择“更改状态”，然后选择状态。

    ![选择辅助功能状态](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > 还可以使用 PowerShell 来创建默认套餐、计划和配额。 有关详细信息，请参阅 [Azure Stack PowerShell 模块 1.4.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](azure-stack-provision-vm.md)