---
title: PowerShell 示例 - 异地复制故障转移组 - 单个 Azure SQL 数据库 | Microsoft Docs
description: 为单个 Azure SQL 数据库设置活动异地复制故障转移组并进行故障转移的 Azure PowerShell 示例脚本。
services: sql-database
documentationcenter: sql-database
author: WenJason
manager: digimobile
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: business continuity, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
origin.date: 09/07/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.openlocfilehash: 690845bee4edd4906ee67b15d26f087831064904
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663663"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-azure-sql-database"></a>使用 PowerShell 为单个 Azure SQL 数据库配置活动异地复制故障转移组

此 PowerShell 脚本示例为单个 Azure SQL 数据库配置活动异地复制故障转移组，并将其故障转移到 Azure SQL 数据库的次要副本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

本教程需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)（安装 Azure PowerShell 模块）。 此外，还需要运行 `Connect-AzureRmAccount -EnvironmentName AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

```powershell
# Login-AzureRmAccount -EnvironmentName AzureChinaCloud
# Set the resource group name and location for your primary server
$primaryresourcegroupname = "myPrimaryResourceGroup-$(Get-Random)"
$primarylocation = "China East"
# Set the resource group name and location for your secondary server
$secondarylocation = "China North"
# Set an admin login and password for your servers
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# Set failover group name - the failover group name has to be unique in the system
$failovergroupname = "failovergroupname-$(Get-Random)"
# Set server names - the logical server names have to be unique in the system
$primaryservername = "primary-server-$(Get-Random)"
$secondaryservername = "secondary-server-$(Get-Random)"
# The sample database name
$databasename = "mySampleDatabase"
# The ip address range that you want to allow to access your servers
$primarystartip = "0.0.0.0"
$primaryendip = "0.0.0.0"
$secondarystartip = "0.0.0.0"
$secondaryendip = "0.0.0.0"

# Create new resource group
$primaryresourcegroup = New-AzureRmResourceGroup -Name $primaryresourcegroupname -Location $primarylocation
$primaryresourcegroup
# Create two new logical servers with a system wide unique server name
$primaryserver = New-AzureRmSqlServer -ResourceGroupName $primaryresourcegroupname `
    -ServerName $primaryservername `
    -Location $primarylocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
$primaryserver
$secondaryserver = New-AzureRmSqlServer -ResourceGroupName $primaryresourcegroupname `
    -ServerName $secondaryservername `
    -Location $secondarylocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
$secondaryserver
# Create a server firewall rule that allows access from the specified IP range
$primaryserverfirewallrule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $primaryresourcegroupname `
    -ServerName $primaryservername `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $primarystartip -EndIpAddress $primaryendip
$primaryserverfirewallrule
$secondaryserverfirewallrule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $primaryresourcegroupname `
    -ServerName $secondaryservername `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $secondarystartip -EndIpAddress $secondaryendip
$secondaryserverfirewallrule
# Create a blank database with S0 performance level on the primary server
$database = New-AzureRmSqlDatabase  -ResourceGroupName $primaryresourcegroupname `
    -ServerName $primaryservername `
    -DatabaseName $databasename -RequestedServiceObjectiveName "S0"
$database
# Create failover group
$failovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $primaryresourcegroupname `
      -ServerName $primaryservername `
      -PartnerServerName $secondaryservername  `
      –FailoverGroupName $failovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
$failovergroup
# Add database to failover group
$failovergroup = Get-AzureRmSqlDatabase `
   -ResourceGroupName $primaryresourcegroupname `
   -ServerName $primaryservername `
   -DatabaseName $databasename | `
   Add-AzureRmSqlDatabaseToFailoverGroup `
   -ResourceGroupName $primaryresourcegroupname ` `
   -ServerName $primaryservername `
   -FailoverGroupName $failovergroupname
$failovergroup
# Initiate a planned failover
Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $primaryresourcegroupname  `
   -ServerName $secondaryservername `
   -FailoverGroupName $failovergroupname 

# Monitor Geo-Replication config and health after failover
Get-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $primaryresourcegroupname  `
   -ServerName $primaryservername 
Get-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $primaryresourcegroupname  `
   -ServerName $secondaryservername 

# Remove the replication link after the failover
Remove-AzureRmSqlDatabaseFailoverGroup `
   -ResourceGroupName $primaryresourcegroupname  `
   -ServerName $secondaryservername `
   -FailoverGroupName $failovergroupname

# Clean up deployment 
# Remove-AzureRmResourceGroup -ResourceGroupName $primaryresourcegroupname


```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlserver) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | 在逻辑服务器中创建弹性池。 |
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary)| 为现有数据库创建辅助数据库，并开始数据复制。 |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase)| 获取一个或多个数据库。 |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary)| 将辅助数据库切换为主数据库，启动故障转移。|
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) | 获取 Azure SQL 数据库和资源组或 SQL Server 之间的异地复制链路。 |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) | 终止 SQL 数据库和指定的辅助数据库之间的数据复制。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
| [New-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasefailovergroup) | 为指定的服务器创建新的 Azure SQL 数据库故障转移组。 |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | 在故障转移组中交换服务器的角色，然后将所有辅助数据库切换到主角色。 |
| [Get-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | 获取特定的 Azure SQL 数据库故障转移组，或列出服务器上的故障转移组。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。