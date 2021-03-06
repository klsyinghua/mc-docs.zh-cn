---
title: Azure AD Connect 同步：更改 AD DS 帐户密码 | Microsoft Docs
description: 本主题文档介绍如何在更改 AD DS 帐户的密码以后更新 Azure AD Connect。
services: active-directory
keywords: AD DS 帐户, Active Directory 帐户, 密码
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/09/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: 5c20e8c1dab75fa12bab151339979c68a934f03a
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648462"
---
# <a name="changing-the-ad-ds-account-password"></a>更改 AD DS 帐户密码
AD DS 帐户是指 Azure AD Connect 用来与本地 Active Directory 通信的用户帐户。 如果更改 AD DS 帐户的密码，则必须使用新密码更新 Azure AD Connect 同步服务。 否则，同步服务将再也不能正确地通过本地 Active Directory 进行同步，会遇到以下错误：

- 在 Synchronization Service Manager 中，任何通过本地 AD 进行的导入或导出操作都会失败，出现 **no-start-credentials** 错误。

- 在 Windows 事件查看器下，应用程序事件日志包含**事件 ID 为 6000** 且内容为“管理代理 "contoso.com" 无法运行，因为凭据无效”的错误。


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>如何使用 AD DS 帐户的新密码更新同步服务
若要使用新密码更新同步服务，请执行以下操作：

1. 启动 Synchronization Service Manager（“开始”→“同步服务”）。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. 转到“连接器”选项卡。

3. 选择“AD 连接器”，该连接器对应于其密码已更改的 AD DS 帐户。

4. 在“操作”下面，选择“属性”。

5. 在弹出对话框中，选择“连接到 Active Directory 林”：

6. 在“密码”文本框中输入 AD DS 帐户的新密码。

7. 单击“确定”保存新密码并关闭弹出对话框。

8. 在 Windows 服务控制管理器下重新启动 Azure AD Connect 同步服务。 这是为了确保从内存缓存中删除对旧密码的任何引用。

## <a name="next-steps"></a>后续步骤
**概述主题**

- [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)

- [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)

