---
title: 在 Azure 门户中创建 Log Analytics 工作区 | Microsoft Docs
description: 了解如何在 Azure 门户中创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
ms.service: azure-monitor
author: lingliw
manager: digimobile
ms.subservice: logs
ms.topic: conceptual
origin.date: 03/12/2019
ms.date: 04/12/2019
ms.author: v-lingwu
ms.openlocfilehash: 517b72bde893953afc3c3802962d3e65da6b6c3b
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028910"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Log Analytics 工作区
使用“Log Analytics 工作区”菜单通过 Azure 门户创建 Log Analytics 工作区  。 Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。 如果打算从以下源收集数据，则需要 Log Analytics 工作区：

* 订阅中的 Azure 资源
* 受 System Center Operations Manager 监视的本地计算机
* Configuration Manager 中的设备集合 
* Azure 存储中的诊断或日志数据

对于其他源，如环境中的 Azure VM 和 Windows VM 或 Linux VM，请参阅以下主题：

*  [从 Azure 虚拟机收集数据](../learn/quick-collect-azurevm.md) 
*  [从混合 Linux 计算机收集数据](../learn/quick-collect-linux-computer.md)
*  [从混合 Windows 计算机收集数据](quick-collect-windows-computer.md)

如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.cn](https://portal.azure.cn) 中登录 Azure 门户。 

## <a name="create-a-workspace"></a>创建工作区
1. 在 Azure 门户中，单击“所有服务”  。 在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”  。

    ![Azure 门户](media/quick-create-workspace/azure-portal-01.png)
  
2. 单击“添加”，然后为以下各项选择选项  ：

   * 为新的 Log Analytics 工作区  提供名称，如 DefaultLAWorkspace  。 此名称在所有 Azure Monitor 订阅中必须全局唯一。
   * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   * 对于**资源组**，选择要使用已设置的现有资源组，还是要创建一个新资源组。  
   * 选择可用**位置**。  有关详细信息，请参阅[提供 Log Analytics 的区域](/home/features/products-by-region)。
<!-- OMS is unavailable -->

3. 在“Log Analytics 工作区”窗格上提供所需信息后，单击“确定”   。  

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度  。 

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。 

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[收集要在 Log Analytics 中使用的 Azure 服务日志和指标](../platform/resource-logs-collect-workspace.md)。  
* 连接 [Configuration Manager](../platform/collect-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[监视解决方案](../insights/solutions.md)以及如何从工作区添加或删除解决方案。
