---
title: 在 Azure Stack 中订阅套餐
description: 在 Azure Stack 中创建套餐的订阅
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: conceptual
origin.date: 10/05/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 6da842d10b81434092fb25c41b9df46f109f67e2
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020308"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>在 Azure Stack 中创建套餐的订阅

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[创建某个套餐](azure-stack-create-offer.md)后，用户需要订阅此套餐才能使用此套餐。 用户订阅套餐有两种方式：

- 在管理员门户中以云操作员身份为用户创建订阅。 创建的订阅可用于公共和专用套餐。
- 租户用户可以在使用用户门户时订阅公共套餐。  

## <a name="create-a-subscription-as-a-cloud-operator"></a>以云操作员的身份创建订阅

云操作员使用管理员门户为用户创建套餐订阅。 你可以为自己的目录租户成员创建订阅。 如果已启用[多租户](azure-stack-enable-multitenancy.md)，则还可以为其他目录租户中的用户创建订阅。

如果不希望租户创建他们自己的订阅，请将所有套餐设置为私有，然后为租户创建订阅。 将 Azure Stack 与外部计帐系统或服务目录系统集成时，通常会使用此方法。

为某个用户创建订阅后，该用户可以登录到用户门户，然后会发现他（她）已订阅套餐。  

### <a name="to-create-a-subscription-for-a-user"></a>为用户创建订阅

1. 在管理门户中，转到“用户订阅”。 
2. 选择“设置”  （应用程序对象和服务主体对象）。 在“新建用户订阅”下，输入以下信息：   

   - **显示名称** – 用于标识订阅的友好名称，显示为“用户订阅名称”。 
   - **用户** – 从此订阅的可用目录租户中指定一个用户。 用户名显示为“所有者”。   用户名的格式取决于标识解决方案。 例如：

     - **Azure AD：** `<user1>@<contoso.partner.onmschina.cn>`

     - **AD FS：** `<user1>@<azurestack.local>`

   - **目录租户** – 选择用户帐户所属的目录租户。 如果未启用多租户，则只能使用本地目录租户。

3. 选择“套餐”。  在“套餐”下，选择此订阅的**套餐**。  由于要为用户创建订阅，因此请选择“私有”作为可访问性状态。 

4. 选择“创建”以创建订阅。  “用户订阅”下面会显示新订阅。  用户在登录到用户门户后可以看到订阅详细信息。

### <a name="to-make-an-add-on-plan-available"></a>提供附加计划

云操作员随时可将计划添加到以前创建的订阅：

1. 在管理门户中，选择“所有服务”  ，然后在“管理资源”  类别下，选择“用户订阅”  。 选择想要更改的订阅。

2. 依次选择“附加计划”、“+添加”。    

3. 在“添加计划”下，选择要添加为附加计划的计划。 

## <a name="create-a-subscription-as-a-user"></a>以用户的身份创建订阅

以用户身份登录到用户门户，查找并订阅目录租户（组织）的公共套餐和附加计划。

>[!NOTE]
>如果 Azure Stack 环境支持[多租户](azure-stack-enable-multitenancy.md)，则也可以从远程目录租户订阅套餐。

### <a name="to-subscribe-to-an-offer"></a>订阅套餐

1. [登录](../asdk/asdk-connect.md)到 [Azure Stack 用户门户](https://portal.local.azurestack.external)，选择“获取订阅”。 

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. 在“获取订阅”下的“显示名称”中输入订阅的友好名称。   选择“套餐”，然后在“选择套餐”下选择套餐。   选择“创建”以创建订阅。 

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. 订阅套餐之后，请刷新门户以查看哪些服务是新订阅的一部分。

4. 若要查看创建的订阅，请选择“所有服务”  ，然后在“常规”  类别下选择“订阅”  。 选择订阅以查看其详细信息。  

### <a name="to-enable-an-add-on-plan-in-your-subscription"></a>在订阅中启用附加计划

如果你订阅的套餐包含附加计划，则可以随时将该计划添加到你的订阅中。  

1. 在用户门户中，选择“所有服务”。  接下来，在“常规”  类别下选择“订阅”  ，然后选择要更改的订阅。 如果有可用的附加计划，则“+ 添加计划”将处于活动状态，并显示“附加计划”的磁贴。  

   如果“+ 添加计划”未处于活动状态，则表示与该订阅关联的套餐没有附加计划。 

1. 选择“+ 添加计划”或“附加计划”磁贴。   在“附加计划”下，选择想要添加的计划。 

## <a name="next-steps"></a>后续步骤

详细了解用户现在如何将资源部署到其订阅： 
  - [几个用户快速入门](../user/azure-stack-quick-windows-portal.md)演示如何使用 PowerShell、Azure CLI 和用户门户预配 Windows 和 Linux 虚拟机。 
