---
title: Azure PowerShell 脚本 - 重新生成 Azure Cosmos DB 帐户密钥 | Azure
description: Azure PowerShell 脚本示例 - 重新生成 Azure Cosmos DB 帐户密钥
services: cosmos-db
documentationcenter: cosmosdb
author: rockboyfor
manager: digimobile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
origin.date: 05/10/2017
ms.date: 09/03/2018
ms.author: v-yeche
ms.openlocfilehash: 9e5b31cb3aa0b77c6cf8efda315a46d8d2f0df63
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52666546"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-powershell"></a>使用 PowerShell 重新生成 Azure Cosmos DB 帐户密钥

此示例可使用 Azure CLI 重新生成任何类型的 Azure Cosmos DB 帐户密钥。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroup"
$resourceGroupLocation = "chinanorth"

# Create the resource group
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Database name
$DBName = "testdb"

# Write and read locations and priorities for the database
$locations = @(@{"locationName"="chinanorth"; 
                 "failoverPriority"=0}, 
               @{"locationName"="chinaeast"; 
                  "failoverPriority"=1})

# Consistency policy
$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard"; 
                          "locations"=$locations; 
                          "consistencyPolicy"=$consistencyPolicy}

# Create the database
New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

# Regenerate the primary account key for the database
Invoke-AzureRmResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName `
    -Parameters @{"keyKind"="Primary"}
```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource?view=azurermps-3.8.0) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [Invoke-AzureRmResourceAction](https://docs.microsoft.com/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-3.8.0) | 对 Azure CosmosDB 帐户调用某个操作。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。
<!-- Update_Description: wording update, update link -->