---
title: 为 Azure 和 Azure Stack Hub 应用配置混合云标识
description: 了解如何为 Azure 和 Azure Stack Hub 应用配置混合云标识。
author: WenJason
ms.service: azure-stack
ms.topic: article
origin.date: 11/05/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 2a4ea70a99b2a843c605eec289ea960d4e597b66
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020533"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-hub-applications"></a>为 Azure 和 Azure Stack Hub 应用程序配置混合云标识

*适用于：Azure Stack Hub 集成系统和 Azure Stack Hub 开发工具包*

了解如何为 Azure 和 Azure Stack Hub 应用配置混合云标识。

有两个选项可用来向 Azure 和 Azure Stack Hub 中的应用授予访问权限。

 * 当 Azure Stack Hub 与 Internet 建立了不间断的连接时，可以使用 Azure Active Directory (Azure AD)。
 * 当 Azure Stack Hub 从 Internet 断开了连接时，可以使用 Azure Directory 联合身份验证服务 (AD FS)。

使用服务主体向 Azure Stack Hub 应用授予访问权限，以便在 Azure Stack Hub 中使用 Azure 资源管理器进行部署或配置。

在此解决方案中，你将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 在 Azure 和 Azure Stack Hub 中建立一个混合标识
> - 检索用于访问 Azure Stack Hub API 的令牌。

必须拥有 Azure Stack Hub 操作员权限才能完成此解决方案中的步骤。

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Azure Stack Hub 是 Azure 的扩展。 Azure Stack Hub 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)一文回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用设计，从而最大限度地减少生产环境中的难题。


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中创建适用于 Azure AD 的服务主体

如果已使用 Azure AD 部署 Azure Stack Hub 作为标识存储，则可以创建服务主体，就像对 Azure 所做的那样。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal)介绍了如何通过门户执行这些步骤。 在开始之前，请确保你具有[所需的 Azure AD 权限](/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 创建适用于 AD FS 的服务主体

如果已使用 AD FS 部署 Azure Stack Hub，则可以使用 PowerShell 创建服务主体、为角色分配访问权限以及使用该标识从 PowerShell 登录。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)介绍了如何使用 PowerShell 执行所需步骤。

## <a name="using-the-azure-stack-hub-api"></a>使用 Azure Stack Hub API

[Azure Stack Hub API](../user/azure-stack-rest-api-use.md) 解决方案将引导你完成检索令牌以访问 Azure Stack Hub API 的过程。

## <a name="connect-to-azure-stack-hub-using-powershell"></a>使用 Powershell 连接到 Azure Stack Hub

[在 Azure Stack Hub 中使用 PowerShell 启动并运行](../operator/azure-stack-powershell-install.md)快速入门演练了安装 Azure PowerShell 并连接到 Azure Stack Hub 安装所要执行的步骤。

### <a name="prerequisites"></a>先决条件

需要一个通过你可以访问的订阅连接到 Azure Active Directory 的 Azure Stack Hub 安装。 如果没有 Azure Stack Hub 安装，可以使用这些说明来安装 [Azure Stack Hub 开发工具包](../asdk/asdk-install.md)。

#### <a name="connect-to-azure-stack-hub-using-code"></a>使用代码连接到 Azure Stack Hub

若要使用代码连接到 Azure Stack Hub，请使用 Azure 资源管理器终结点 API 来获取 Azure Stack Hub 安装的身份验证和 Graph 终结点，然后使用 REST 请求进行身份验证。 可在 [GitHub](https://github.com/shriramnat/HybridARMApplication) 中找到示例客户端应用程序。

>[!Note]
>除非所选语言的 Azure SDK 支持 Azure API 配置文件，否则 SDK 可能无法与 Azure Stack Hub 一起使用。 有关 Azure API 配置文件的详细信息，请参阅[管理 API 版本配置文件](../user/azure-stack-version-profiles.md)一文。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack Hub 如何处理标识，请参阅 [Azure Stack Hub 的标识体系结构](../operator/azure-stack-identity-architecture.md)。
