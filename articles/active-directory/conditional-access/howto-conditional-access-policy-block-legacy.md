---
title: 条件访问 - 阻止旧式身份验证 - Azure Active Directory
description: 创建用于阻止旧式身份验证协议的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: v-junlch
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dd6bc61c71b35711b8675c1b458fd5caf55eed2
ms.sourcegitcommit: bc5f8b4f8ccd7c723f64055825508d1dfcc2162b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75859278"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件访问：阻止传统身份验证

由于与旧式身份验证协议相关的风险在增加，Microsoft 建议组织阻止使用这些协议进行的身份验证请求，并要求使用新式身份验证。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将帮助创建条件访问策略来阻止旧式身份验证请求。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下  选择“用户和组”  ，然后选择任何必须保留使用旧式身份验证的功能的帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”下，选择“所有云应用”   。
   1. 选择“完成”  。
1. 在“条件”   >   “客户端应用(预览)”下，将“配置”设置为“是”。  
   1. 仅勾选“移动应用和桌面客户端”   >   “其他客户端”框。
   1. 选择“完成”  。
1. 在“访问控制”   >   “授予”下，选择“阻止访问”。 
   1. 选择“选择”  。
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

