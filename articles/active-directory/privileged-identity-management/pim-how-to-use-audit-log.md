---
title: 查看 PIM 中 Azure AD 角色的审核报表 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中查看 Azure AD 角色的审核历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/08/2020
ms.author: v-junlch
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f55d83d26112cc9669ad83a39e90ddfe36c6e5e3
ms.sourcegitcommit: 1bc154c816a5dff47ee051c431cd94826e57aa60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75777072"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>在 PIM 中查看 Azure AD 角色的审核历史记录

可以使用 Privileged Identity Management (PIM) 审核历史记录来查看过去 30 天内所有特权角色的所有角色分配和激活操作。 若要查看 Azure Active Directory (Azure AD) 组织中活动的完整审核历史记录（包括管理员、最终用户和同步活动），可以使用 [Azure Active Directory 安全和活动报告](../reports-monitoring/overview-reports.md)。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从 2019 年 11 月开始，Privileged Identity Management 的 Azure AD 角色部分将更新为与 Azure 资源角色的体验相匹配的新版本。 这将创建附加功能以及[对现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中遵循的过程取决于当前拥有的 Privileged Identity Management 版本。 按照本部分中的步骤确定所拥有的 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本之后，可以选择本文中与该版本匹配的过程。

1. 以具有[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的用户身份登录到 [Azure 门户](https://portal.azure.cn/)。
1. 打开“Azure AD Privileged Identity Management”。  如果在概述页的顶部有横幅，请按照本文“新版本”选项卡中的说明进行操作  。 否则，请按照“先前版本”选项卡中的说明操作  。

    ![Azure AD 角色新版本](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="previous-versiontabprevious"></a>[先前版本](#tab/previous)

## <a name="view-audit-history"></a>查看审核历史记录

按以下步骤查看 Azure AD 角色的审核历史记录。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.cn/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”  。

1. 选择“目录角色审核历史记录”。 

    将会根据审核历史记录显示柱形图和总激活数、每日最大激活数以及每日平均激活数。

    ![目录角色审核历史记录](./media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在页面底部会显示一个表，其中包含可用审核历史记录中每个操作的信息。 列的含义如下：

    | 列 | 说明 |
    | --- | --- |
    | 时间 | 发生操作的时间。 |
    | 请求者 | 已请求角色激活或更改的用户。 如果该值为“Azure 系统”，请查看 Azure 审核历史记录以获取详细信息。  |
    | 操作 | 请求者所采取的操作。 操作可能包含分配、取消分配、激活、停用或 AddedOutsidePIM。 |
    | 成员 | 正在激活或已分配给角色的用户。 |
    | 角色 | 由用户分配或激活的角色。 |
    | 理由 | 在激活期间向原因字段中输入的文本。 |
    | 过期时间 | 已激活角色过期的时间。 仅适用于符合条件的角色分配。 |

1. 若要对审核历史记录排序，请单击“时间”  、“操作”  和“角色”  按钮。

## <a name="filter-audit-history"></a>筛选审核历史记录

1. 在审核历史记录页顶部，单击“筛选”按钮。 

    将显示“更新图表参数”窗格  。

1. 在“时间范围”中，  选择时间范围。

1. 在**角色**中，选中指示要查看的角色的复选框。

    ![“更新图表参数”窗格](./media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 选择“完成”，查看已筛选的审核历史记录。 

# <a name="new-versiontabnew"></a>[新版本](#tab/new)

按以下步骤查看 Azure AD 角色的审核历史记录。

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

可以通过资源审核查看与 Azure AD 角色关联的所有活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”  。

1. 选择“资源审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![带筛选器的资源审核列表](./media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”  。

1. 选择要查看其审核历史记录的资源。

1. 选择“我的审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![当前用户的审核列表](./media/azure-pim-resource-rbac/my-audit-time.png)

---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中查看 Azure 资源角色的活动和审核历史记录](azure-pim-resource-rbac.md)

<!-- Update_Description: wording update -->