---
title: 将 Windows 虚拟机从非托管磁盘转换为托管磁盘 - Azure 托管磁盘 | Azure
description: 如何在资源管理器部署模型中使用 PowerShell 将 Windows VM 从非托管磁盘转换为托管磁盘
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 07/12/2018
ms.date: 07/30/2018
ms.author: v-yeche
ms.openlocfilehash: 50ff214cfb6e2e4b209c136f6b17b53ef0e5436e
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52660470"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>将 Windows 虚拟机从非托管磁盘转换为托管磁盘

如果有使用非托管磁盘的现有 Windows 虚拟机 (VM)，可通过 [Azure 托管磁盘](managed-disks-overview.md)服务将 VM 转换为使用托管磁盘。 此过程会同时转换 OS 磁盘和任何附加的数据磁盘。

本文介绍如何使用 Azure PowerShell 转换 VM。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。

## <a name="before-you-begin"></a>准备阶段

* 请查看[计划迁移到托管磁盘](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

* 请查看[有关迁移到托管磁盘的常见问题](faq-for-disks.md#migrate-to-managed-disks)。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

## <a name="convert-single-instance-vms"></a>转换单实例 VM
本节介绍如何将单实例 Azure VM 从非托管磁盘转换为托管磁盘。 （如果 VM 位于可用性集中，请参阅下一节。） 

1. 使用 [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) cmdlet 解除分配 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM： 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. 使用 [ConvertTo-AzureRmVMManagedDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet 将 VM 转换为托管磁盘。 以下过程转换之前的 VM，包括 OS 磁盘和任何数据磁盘，并启用虚拟机：

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

## <a name="convert-vms-in-an-availability-set"></a>在可用性集中转换 VM

如果要转换为托管磁盘的 VM 位于可用性集中，则需要先将可用性集转换为托管可用性集。

1. 使用 [Update-AzureRmAvailabilitySet](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet 转换可用性集。 以下示例在名为 `myResourceGroup` 的资源组中更新名为 `myAvailabilitySet` 的可用性集：

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  如果可用性集所在的区域只有 2 个托管容错域，但却有 3 个非托管容错域，则此命令会显示类似于“指定的容错域计数 3 必须在 1 到 2 这个范围内”的错误消息。 若要解决此错误，请将容错域更新为 2，并按如下所示将 `Sku` 更新为 `Aligned`：

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 解除分配 VM，并转换可用性集中的 VM。 以下脚本使用 [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) cmdlet 解除分配每个 VM，使用 [ConvertTo-AzureRmVMManagedDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) 进行转换，并在转换进程结束后自动将其重启：

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```

## <a name="troubleshooting"></a>故障排除

如果转换过程中出现错误，或先前转换中的问题导致 VM 处于“失败”状态，请再次运行 `ConvertTo-AzureRmVMManagedDisk` cmdlet。 简单的重试通常可以解决这种情况。
在转换之前，确保所有 VM 扩展都处于“配置成功”状态，否则转换将失败，并出现错误代码 409。

## <a name="next-steps"></a>后续步骤

[将标准托管磁盘转换为高级托管磁盘](convert-disk-storage.md)

使用[快照](snapshot-copy-managed-disk.md)获取 VM 的只读副本。

<!--Update_Description: update meta properties, wording update -->