---
title: 如何为自定义开发的应用程序查找所需的特定 API | Microsoft Docs
description: 如何配置访问自定义开发的 Azure AD 应用程序中的特定 API 所需的权限
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 09/11/2018
ms.date: 10/08/2018
ms.author: v-junlch
ms.openlocfilehash: 22065c8c8ed775dec60e6727ee65e2f7869ebf91
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52656978"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>如何为自定义开发的应用程序查找所需的特定 API

访问 API 需要配置访问作用域和访问角色。 如果想要将资源应用程序 Web API 公开至客户端应用程序，需要为 API 配置访问作用域和访问角色。 如果想要让客户端应用程序访问 Web API，需要配置权限以访问应用注册中的 API。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>将资源应用程序配置为公开 Web API

当 Web API 公开后，将权限添加到应用注册时，API 会显示在“选择 API”列表中。 要添加访问作用域，请按照[将访问作用域添加到资源应用程序](/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application)中概述的步骤操作。

## <a name="configuring-a-client-application-to-access-web-apis"></a>将客户端应用程序配置为访问 Web API

在将权限添加到应用注册时，可**添加 API 访问**到已公开的 Web API。 若要访问 Web API，请按照[添加凭据或权限以访问 Web API](/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis)中概述的步骤操作。

## <a name="next-steps"></a>后续步骤

-   [将应用程序与 Azure Active Directory 集成](/active-directory/develop/active-directory-integrating-applications)

-   [了解 Azure Active Directory 应用程序清单](/active-directory/develop/active-directory-application-manifest)


