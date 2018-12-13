---
title: Azure PowerShell 脚本示例 - 备份和还原服务
description: Azure PowerShell 脚本示例 - 备份和还原服务
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
origin.date: 11/16/2017
ms.date: 08/13/2018
ms.author: v-yiso
ms.custom: mvc
ms.openlocfilehash: d6d22cc40939cd05b90f94a93e6e72893b103590
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648206"
---
# <a name="backup-and-restore-service"></a>备份和还原服务

本文中显示的此示例演示如何备份和还原 API 管理服务实例。 


如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount -Environment AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell
##########################################################
#  Script to backup and restore api management service.
###########################################################

$random = (New-Guid).ToString().Substring(0,8)

#Azure specific details
$subscriptionId = "my-azure-subscription-id"
 

# Api Management service specific details
$apiManagementName = "apim-$random"
$resourceGroupName = "apim-rg-$random"
$location = "China East"
$organisation = "Contoso"
$adminEmail = "admin@contoso.com"
 
# Storage Account details
$storageAccountName = "backup$random"
$containerName = "backups"
$backupName = $apiManagementName + ".apimbackup"
 
# Select into the ResourceGroup
Select-AzureRmSubscription -SubscriptionId $subscriptionId
 
# Create a Resource Group 
New-AzureRmResourceGroup -Name $resourceGroupName -Location $location -Force
 
# Create storage account    
New-AzureRmStorageAccount -StorageAccountName $storageAccountName -Location $location -ResourceGroupName $resourceGroupName -Type Standard_LRS
$storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName)[0].Value
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey
 
# Create API Management service
New-AzureRmApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization $organisation -AdminEmail $adminEmail
 
# Backup API Management service.
Backup-AzureRmApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName -StorageContext $storageContext -TargetContainerName $containerName -TargetBlobName $backupName
 
# Restore API Management service
Restore-AzureRmApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName -StorageContext $storageContext -SourceContainerName $containerName -SourceBlobName $backupName
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us//powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [PowerShell 示例](../powershell-samples.md)中找到 Azure API 管理的其他 Azure Powershell 示例。