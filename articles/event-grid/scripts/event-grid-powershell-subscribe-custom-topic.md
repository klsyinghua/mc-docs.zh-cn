---
title: Azure PowerShell 脚本示例 - 订阅自定义主题 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 订阅自定义主题
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: v-yiso
ms.openlocfilehash: 3948491022cac578836bc5ebed8b8bf68ac24e0c
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195316"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>使用 PowerShell 订阅自定义主题的事件

此脚本创建自定义主题事件的事件网格订阅。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

预览示例脚本需要事件网格模块。 若要安装，请运行 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!--[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]-->

## <a name="sample-script---preview-module"></a>示例脚本 - 预览模块

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

<!--[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]-->

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
