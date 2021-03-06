---
title: 如何获取 Azure Stack Hub 的身份验证信息 | Microsoft Docs
description: 了解如何获取 Azure Stack Hub 的身份验证信息
services: azure-stack
author: WenJason
ms.service: azure-stack
ms.topic: how-to
origin.date: 12/13/2019
ms.date: 01/13/2020
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 2766debf65f82ecf9100eebf0c3614d51a38326e
ms.sourcegitcommit: 166549d64bbe28b28819d6046c93ee041f1d3bd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737984"
---
# <a name="how-to-get-authentication-information-for-azure-stack-hub"></a>如何获取 Azure Stack Hub 的身份验证信息

若要对 Azure Stack Hub 进行身份验证，需要提供订阅 ID、租户 ID 和位置以及 Azure Stack Hub 资源管理器终结点。 可以从 [Azure Stack Hub 资源管理器终结点](/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-resource-manager-endpoint)为 Azure Stack Hub 获取这些值。 或者，可以使用本文中的步骤获取它们。

## <a name="values-needed-to-authenticate"></a>进行身份验证所需的值

需要以下信息：

-   **订阅 ID**  

    使用订阅 ID 访问 Azure Stack 中的套餐。

-   **租户 ID**

    目录是保留用户、应用程序、组和服务主体相关信息的容器。 目录租户是一个组织，例如 Microsoft 或你自己的公司。

-   **位置**

    位置（或区域）是一组数据中心，部署在定义了延迟的外围中，通过专用的区域性低延迟网络互相连接。 如果使用 Azure Stack Hub，你的位置可能包含本地数据中心，而不是 Azure 区域。

-   **Azure Stack Hub 资源管理器终结点**

    Azure 资源管理器是一种管理框架，可供管理员用来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

## <a name="get-the-subscription-id"></a>获取订阅 ID

获取订阅 ID：

1.  登录到 Azure Stack Hub 用户门户。

2.  选择“所有服务”  。

    > ![Azure Stack Hub 身份验证信息订阅 ID 租户 ID](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  选择 **订阅**。

4.  选择要使用的订阅。

5.  从“概述”复制“订阅 ID”   。

## <a name="get-the-tenant-id"></a>获取租户 ID

获取租户 ID：

1.  登录到 Azure Stack Hub 用户门户。

2.  将鼠标悬停在边栏选项卡右上方的“用户名”上。

3.   目录 ID 是租户 ID。

## <a name="get-the-azure-resource-manager-endpoint"></a>获取 Azure 资源管理器终结点

Azure 资源管理器终结点是用于 Azure Stack Hub 的部署和管理服务的元数据终结点。 它提供一个管理层用于在 Azure 订阅中创建、更新和删除资源。

对于集成系统，Azure 资源管理器终结点的 URL 是：<br>`https://management.<location>.<fqdn>.com`

若要获取指向属性（如库终结点、图形终结点、门户终结点、登录终结点和受众）的元数据终结点，URL 为：`<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>后续步骤

详细了解如何将 [Azure Stack 资源管理器](/azure-stack/user/azure-stack-version-profiles?view=azs-1910) 与 Azure Stack Hub 配合使用。
