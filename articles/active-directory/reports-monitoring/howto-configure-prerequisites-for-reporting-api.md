---
title: 访问 Azure Active Directory 报告 API 的先决条件 | Microsoft 文档
description: 了解有关访问 Azure AD 报告 API 的先决条件
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
origin.date: 08/30/2019
ms.date: 10/11/2019
ms.author: v-junlch
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 704176ce2224a4868cb849ccb4bcb00a85dc25ff
ms.sourcegitcommit: 74f50c9678e190e2dbb857be530175f25da8905e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72292107"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>访问 Azure Active Directory 报告 API 的先决条件

[Azure Active Directory (Azure AD) 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](/api-management/api-management-howto-protect-backend-with-aad) 授权访问 Web API。

若要准备访问报告 API，需要执行以下操作：

1. [分配角色](#assign-roles)
2. [注册应用程序](#register-an-application)
3. [授予权限](#grant-permissions)
4. [收集配置设置](#gather-configuration-settings)

## <a name="assign-roles"></a>分配角色

若要通过 API 访问报告数据，需分配以下角色之一：

- 安全读取者

- 安全管理员

- 全局管理员角色


## <a name="register-an-application"></a>注册应用程序

即使是在使用脚本来访问报告 API，也需要注册。 注册会提供一个**应用程序 ID**，该 ID 是进行授权调用所必需的，代码也通过该 ID 来接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中**全局管理员**目录角色成员的 Azure 管理员帐户登录到 [Azure 门户](https://portal.azure.cn)。

> [!IMPORTANT]
> 在具有管理员权限的凭据下运行的应用程序可能具有非常强大的功能，因此请务必将应用程序的 ID 和机密凭据放置在一个安全的位置。
> 

**若要注册 Azure AD 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.cn)中，从左侧导航窗格中选择“Azure Active Directory”。 
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页面中，选择“应用注册”  。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 在“应用注册”页面中，选择“新建注册”   。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. “注册应用程序”页： 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在“名称”  文本框中，键入 `Reporting API application`。

    b. 对于“支持的帐户类型”设置，请选择“仅限此组织中的帐户”。  

    c. 在“重定向 URL”  中，选择“Web”文本框  ，然后键入 `https://localhost`。

    d. 选择“注册”  。 


## <a name="grant-permissions"></a>授予权限 

根据想要访问的 API，你需要向应用授予以下权限：  

| API | 权限 |
| --- | --- |
| Azure Active Directory | 读取目录数据 |
| Microsoft Graph | 读取所有审核日志数据 |


![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/36.png)

以下部分列出适用于这两个 API 的步骤。 如果你不想访问其中一个 API，可以跳过相关步骤。

**若要授予应用程序使用 API 的权限，请执行以下操作：**


1. 选择“API 权限”  ，然后选择“添加权限”  。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. 在“请求 API 权限”页上，  找到“支持旧版 API”  **Azure Active Directory Graph**。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. 在“所需权限”页上选择“应用程序权限”，   展开“目录”复选框  **Directory.ReadAll**。  选择“添加权限”  。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. 在“报表 API 应用程序 - API 权限”页上，选择“授予管理员许可”。   

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 注意：**Microsoft Graph** 在 API 注册过程中默认添加。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>收集配置设置 

本部分介绍了如何从目录中获取以下设置：

- 域名
- 客户端 ID
- 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.cn)中，在左侧导航窗格中，选择“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页上，选择“自定义域名”  。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.cn)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 从“应用注册”  页面中选择你的应用程序。

3. 从应用程序页面中，导航到“应用程序 ID”  并选择“单击以复制”。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密
 避免尝试访问审核日志或使用 API 登录时出现的错误。

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.cn)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  从“应用注册”  页面中选择你的应用程序。

3.  在“API 应用程序”页上选择“证书和机密”，然后在“客户端机密”部分单击“+ 新建客户端机密”。     

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. 在“添加客户端机密”页上，添加以下内容： 

    a. 在“说明”  文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。  

    c. 单击“保存”  。

    d. 复制密钥值。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>排查报告 API 中的错误

本部分列出了使用 MS Graph API 访问活动报告时可能遇到的常见错误消息及其解决步骤。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>访问 Microsoft Graph V2 终结点时出现 500 HTTP 内部服务器错误

目前，我们不支持 Microsoft Graph v2 终结点，请务必使用 Microsoft Graph v1 终结点访问活动日志。

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>错误：租户不是 B2C，或者租户没有 Premium 许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-the-allowed-roles-does-not-include-user"></a>错误：允许的角色中不包括“用户”。 

 避免尝试访问审核日志或使用 API 登录时出现的错误。 确保帐户属于 Azure Active Directory 租户中的“安全读取者”或“报表读取者”角色   。

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误：应用程序缺少 AAD“读取目录数据”权限 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>错误：应用程序缺少 MSGraph API“读取所有审核日志数据”权限

按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)

<!-- Update_Description: wording update -->