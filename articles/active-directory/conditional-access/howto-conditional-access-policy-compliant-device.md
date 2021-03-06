---
title: 条件访问 - 要求合规设备 - Azure Active Directory
description: 创建要求合规设备的自定义条件访问策略
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
ms.openlocfilehash: 344a4bc52c4acfc9003dc3ec9a76458070bef926
ms.sourcegitcommit: bc5f8b4f8ccd7c723f64055825508d1dfcc2162b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75859275"
---
# <a name="conditional-access-require-compliant-devices"></a>条件访问：要求符合的设备

部署了 Microsoft Intune 的组织可以根据从其设备返回的信息来确定符合合规性要求的设备，例如以下要求：

* 要求使用 PIN 来解锁
* 要求设备加密
* 要求操作系统版本有一个下限或上限
* 要求设备未越狱或取得 root 权限

此策略符合性信息会转发给 Azure AD，其中的条件访问可以进行决策，确定是授予还是阻止资源访问权限。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求将访问资源的设备标记为符合组织的 Intune 符合性策略。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下  选择“用户和组”  ，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
   1. 如果必须将特定应用程序排除在策略之外，可以在“选择排除的云应用”下的“排除”选项卡中选择它们，然后选择“选择”。   
   1. 选择“完成”  。
1. 在“访问控制”   >   “授予”下，选择“要求将设备标记为合规”。 
   1. 选择“选择”  。
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

### <a name="known-behavior"></a>已知行为

在 Windows 7、iOS、Android、macOS 和某些第三方 Web 浏览器上，Azure AD 使用客户端证书来标识设备，该证书是在向 Azure AD 注册设备时预配的。 用户首次通过浏览器登录时，系统会提示用户选择此证书。 最终用户必须选择此证书才能继续使用浏览器。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[设备符合性策略与 Azure AD 配合使用](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)

