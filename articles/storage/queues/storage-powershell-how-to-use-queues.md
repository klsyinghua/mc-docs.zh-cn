---
title: 使用 PowerShell 对 Azure 队列存储执行操作 | Azure
description: 如何使用 PowerShell 对 Azure 队列存储执行操作
services: storage
documentationcenter: storage
author: forester123
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 09/14/2017
ms.date: 07/02/2018
ms.author: v-johch
ms.openlocfilehash: cbb8ea8a12e43f5cd523f327fced6adfe2a706e1
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52660908"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 执行 Azure 队列存储操作

Azure 队列存储是一项可存储大量消息的服务，用户可以通过 HTTP 或 HTTPS 从世界任何地方访问这些消息。 有关详细信息，请参阅 [Azure 队列简介](storage-queues-introduction.md)。 此操作指南文章介绍常见的队列存储操作。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 创建队列
> * 检索队列
> * 添加消息
> * 读取消息
> * 删除消息 
> * 删除队列

本操作指南需要 Azure PowerShell 模块 3.6 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)（安装 Azure PowerShell 模块）。

队列的数据平面没有相应的 PowerShell cmdlet。 若要执行数据平面操作（如添加消息、读取消息和删除消息），必须使用 PowerShell 中公开的 .NET 存储客户端库。 创建消息对象，然后可以使用命令（例如 AddMessage）对该消息执行操作。 本文介绍如何执行该操作。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount -EnvironmentName AzureChinaCloud` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="retrieve-list-of-locations"></a>检索位置列表

如果你不知道要使用哪个位置，可以列出可用的位置。 显示列表后，找到要使用的位置。 本练习使用“中国东部”。 将此内容存储在变量 location 中，以供以后使用。

```powershell
Get-AzureRmLocation | select Location 
$location = "China East"
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建资源组。 

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 将资源组名称存储在变量中，以供以后使用。 本示例在“中国东部”区域创建名为“howtoqueuesrg”的资源组。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>创建存储帐户

使用 [ New-AzureRmStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 创建具有本地冗余存储 (LRS) 的标准通用存储帐户。 获取用于定义要使用的存储帐户的存储帐户上下文。 对存储帐户执行操作时，引用上下文而不是重复提供凭据。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>创建队列

以下示例首先使用存储帐户上下文（包括存储帐户名称及其访问密钥）与 Azure 存储建立连接。 接下来，它调用 [New-AzureStorageQueue](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragequeue) cmdlet 以创建名为“queuename”的队列。

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

有关命名 Azure 队列服务命名约定的信息，请参阅 [Naming Queues and Metadata](http://msdn.microsoft.com/library/azure/dd179349.aspx)（命名队列和元数据）。

## <a name="retrieve-a-queue"></a>检索队列

可以查询和检索存储帐户中的特定队列，或者所有队列的列表。 以下示例演示如何检索存储帐户中的所有队列以及特定队列；这两个命令都使用 [ Get-AzureStorageQueue ](https://docs.microsoft.com/powershell/module/azure.storage/get-azurestoragequeue) cmdlet。

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>向队列添加消息

影响队列中的实际消息的操作使用 PowerShell 中公开的 .NET 存储客户端库。 若要在队列中添加消息，请创建消息对象 [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) 类的新实例。 接下来，调用 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 方法。 可从字符串（UTF-8 格式）或字节数组创建 CloudQueueMessage。

以下示例演示如何向队列中添加消息。

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

如果使用 [Azure 存储资源管理器](http://storageexplorer.com)，可以连接到 Azure 帐户并查看存储帐户中的队列，然后在队列中向下钻取以查看队列中的消息。 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>从队列中读取一条消息，然后删除该消息

尽量以先进先出的顺序读取消息。 但不能保证完全这样做。 从队列中读取消息时，消息将对查看此队列的所有其他进程不可见。 这可确保如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。  

**不可见超时**定义了消息在再次可供处理之前保持不可见的时间。 默认为 30 秒。 

代码分两步从队列中读取消息。 调用 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) 方法时，将获取队列中的下一条消息。 从 **GetMessage** 返回的消息变得对从此队列读取消息的任何其他代码不可见。 若要完成从队列中删除消息，请调用 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) 方法。 

在以下示例中，你将读完三条队列消息，然后等待 10 秒（不可见超时）。 然后再次读取这三条消息，读取后，通过调用 DeleteMessage  删除该消息。 如果在删除消息后尝试读取队列，$queueMessage 将返回为 NULL。

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>删除队列
若要删除队列及其中包含的所有消息，请调用 Remove-AzureStorageQueue cmdlet。 以下示例演示如何使用 Remove-AzureStorageQueue cmdlet 删除本练习中使用的特定队列。

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>清理资源

若要删除已在此练习中创建的所有资产，请删除该资源组。 这会一并删除组中包含的所有资源。 在这种情况下，它会删除创建的存储帐户以及资源组本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了使用 PowerShell 进行队列存储管理的基本知识，其中包括如何：

> [!div class="checklist"]
> * 创建队列
> * 检索队列
> * 添加消息
> * 读取下一条消息
> * 删除消息 
> * 删除队列

### <a name="azure-powershell-storage-cmdlets"></a>Azure PowerShell 存储 cmdlet
* [存储 PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.storage#storage)

### <a name="azure-storage-explorer"></a>Azure 存储资源管理器
* [Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fstorage%2fblobs%2ftoc.json)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。