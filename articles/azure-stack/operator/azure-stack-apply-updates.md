---
title: 安装 Azure Stack 更新 | Microsoft Docs
description: 了解如何安装 Azure Stack 更新。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 09/10/2019
ms.date: 10/21/2019
ms.author: v-jay
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: d249e8457a8d1a604f3d57c6776d4585d8f51a16
ms.sourcegitcommit: 713bd1d1b476cec5ed3a9a5615cfdb126bc585f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72578480"
---
# <a name="install-azure-stack-updates"></a>安装 Azure Stack 更新

*适用于：Azure Stack 集成系统*

可以使用 Azure Stack 中的“更新”  边栏选项卡安装更新包。 本文将指导你完成更新、监视和排除更新过程故障的步骤。 使用“更新”边栏选项卡查看更新信息、安装更新、监视更新进度、查看更新历史记录以及查看当前 Azure Stack 和 OEM 包版本。

可以从管理员门户管理更新，并使用仪表板的“更新”  部分执行以下操作：

- 查看重要信息，例如当前版本。
- 安装更新并监视进度。
- 查看以前安装的更新的更新历史记录。
- 查看云的当前 OEM 包版本。

## <a name="determine-the-current-version"></a>确定当前版本

可以在“更新”边栏选项卡中查看 Azure Stack 的当前版本。  若要打开，请执行以下操作：

1.  打开 Azure Stack 管理员门户。

2.  选择“仪表板”。  在“更新”边栏选项卡中会列出当前版本。 

    ![默认仪表板上的“更新”磁贴](./media/azure-stack-update-apply/image1.png)

    例如，此图像中的版本是 1.1903.0.35。

## <a name="install-updates-and-monitor-progress"></a>安装更新并监视进度

1. 打开 Azure Stack 管理员门户。

2. 选择“仪表板”。  选择“更新”  。

3. 选择要安装的可用更新。 如果没有标记为“可用”  的更新，则需要[准备更新包](azure-stack-update-prepare-package.md)

4. 选择“立即更新”。 

    ![Azure Stack 更新运行详细信息](./media/azure-stack-update-apply/image2.png)

5. 可以查看当更新进程循环访问 Azure Stack 中的各个子系统时的概要状态。 示例子系统包括物理主机、Service Fabric、基础结构虚拟机，以及提供管理员用户和用户门户的服务。 在整个更新过程中，“更新”资源提供程序会报告有关更新的其他详细信息，例如成功的步骤数，以及正在进行的步骤数。

6. 从“更新运行详细信息”边栏选项卡中选择“下载摘要”  以下载完整日志。

    如果在监视更新时遇到问题，可以使用[特权终结点](/azure-stack/operator/azure-stack-privileged-endpoint)来监视 Azure Stack 更新运行的进度。 也可以使用特权终结点在 Azure Stack 门户不可用时，从最后一个成功的步骤恢复失败的更新运行。 有关说明，请参阅[使用 PowerShell 在 Azure Stack 中监视更新](azure-stack-update-monitor.md)。

    ![Azure Stack 更新运行详细信息](./media/azure-stack-update-apply/image3.png)

7. 完成后，“更新”资源提供程序会提供“成功”确认，显示更新过程已完成，以及更新所花费的时间。  在此处，可以使用筛选器查看有关所有更新、可用更新或已安装更新的信息。

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    如果更新失败，“更新”边栏选项卡会报告“需要注意”。   使用“下载完整日志”选项来获取更新失败的位置的概要状态。  Azure Stack 日志收集有助于诊断和故障排除。

## <a name="review-update-history"></a>查看更新历史记录

1. 打开管理员门户。

2. 选择“仪表板”。  选择“更新”  。

3. 选择“更新历史记录”。 

    ![Azure Stack 更新历史记录](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>后续步骤

-   [在 Azure Stack 中管理更新概述](/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack 服务策略](/azure-stack/operator/azure-stack-servicing-policy)  
