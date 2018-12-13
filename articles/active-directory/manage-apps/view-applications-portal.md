---
title: 查看租户应用程序 - Azure Active Directory | Microsoft Docs
description: 使用 Azure 门户查看 Azure Active Directory (Azure AD) 租户中的应用程序。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
origin.date: 07/25/2018
ms.date: 08/29/2018
ms.author: v-junlch
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: 5692146e0f4058de14df8ace869eb84625a7ba69
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52645912"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>查看 Azure Active Directory 租户应用程序

本快速入门使用 Azure 门户查看 Azure Active Directory (Azure AD) 租户中的应用程序。

## <a name="before-you-begin"></a>准备阶段

若要查看结果，Azure AD 租户中需至少有一个应用程序。

以 Azure AD 租户全局管理员、云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.cn)。

## <a name="find-the-list-of-tenant-applications"></a>查找租户应用程序的列表

Azure AD 租户应用程序可以在 Azure 门户的“企业应用”部分查看。

若要查找租户应用程序，请执行以下操作：

1. 在 **[Azure 门户](https://portal.azure.cn)** 的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“企业应用程序”。 

3. 在“应用程序类型”下拉菜单中，选择“所有应用程序”，然后单击“应用”。 此时会显示租户应用程序的随机示例。

    ![企业应用](./media/view-applications-portal/open-enterprise-apps.png)
   
4. 若要查看更多应用程序，请单击列表底部的“显示更多”。 根据租户中应用程序的数目，也许[搜索特定应用程序](#search-for-a-tenant-application)比滚动浏览整个列表要容易些。

## <a name="select-viewing-options"></a>选择查看选项

在此部分，请根据要查找的内容选择选项。

1. 可以根据“应用程序类型”、“应用程序状态”和“应用程序可见性”的相应选项查看应用程序。 

    ![搜索选项](./media/view-applications-portal/search-options.png)

2. 在“应用程序类型”下选择下述选项之一：

    - “企业应用程序”显示非 Microsoft 应用程序。
    - “Microsoft 应用程序”显示 Microsoft 应用程序。
    - “所有应用程序”显示非 Microsoft 应用程序和 Microsoft 应用程序。

3. 在“应用程序状态”下选择“任何”、“已禁用”或“已启用”。 “任何”选项包括已禁用和已启用的应用程序。

4. 在“应用程序可见性”下选择“任何”或“已隐藏”。 “已隐藏”选项显示存在于租户中但对用户不可见的应用程序。

5. 选择所需选项后，请单击“应用”。
 

## <a name="search-for-a-tenant-application"></a>搜索租户应用程序

若要搜索特定的应用程序，请执行以下操作：

1. 在“应用程序类型”菜单中，选择“所有应用程序”，然后单击“应用”。

2. 输入要查找的应用程序的名称。 如果应用程序已添加到 Azure AD 租户中，则会显示在搜索结果中。 此示例显示 GitHub 尚未添加到租户应用程序中。

    ![搜索应用程序](./media/view-applications-portal/search-for-tenant-application.png)

3. 尝试输入应用程序名称的头几个字母。  此示例显示以 **Sales** 开头的所有应用程序。

    ![使用前缀搜索](./media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何查看 Azure AD 租户中的应用程序，以及如何通过应用程序类型、状态和可见性筛选应用程序列表。 另外还介绍了如何搜索特定应用程序。

找到要查找的应用程序以后，即可继续操作，通过单击该应用程序来查看或编辑属性和配置选项。 



<!-- Update_Description: wording update -->