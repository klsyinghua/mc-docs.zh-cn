---
title: 在 Azure Stack 中使用命令行部署模板 | Microsoft Docs
description: 了解如何使用 Azure 跨平台命令行接口 (CLI) 将模板部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
origin.date: 10/07/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 46a14abaab40b2675e532bc8e0e9560399b0cc61
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020024"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>在 Azure Stack 中使用命令行部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure 命令行接口 (CLI) 在 Azure Stack 中部署 Azure 资源管理器模板。 Azure 资源管理器模板可通过单个协调操作部署和设置应用的资源。

## <a name="deploy-template"></a>部署模板

1. 浏览 [AzureStack-QuickStart-Templates 存储库](https://aka.ms/AzureStackGitHub)，找到 **101-create-storage-account** 模板。 将模板 (`azuredeploy.json`) 和参数文件 `(azuredeploy.parameters.json` 保存到本地驱动器上的某个位置，例如 `C:\templates\`
2. 导航到文件所下载到的文件夹。 
3. 使用 Azure CLI [安装并连接](azure-stack-version-profiles-azurecli2.md)到 Azure Stack。
4. 通过以下命令更新区域和位置。 如果使用的是 ASDK，请使用 `local` 作为位置参数。 若要部署模板，请执行以下操作：
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

此命令将模板部署到 Azure Stack 实例中的资源组 **testDeploy**。

## <a name="validate-template-deployment"></a>验证模板部署

若要查看资源组和存储帐户，请运行以下 CLI 命令：

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)。
