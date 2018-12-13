---
title: 教程 - 在 Azure 中为 Window 创建虚拟机规模集 | Azure
description: 本教程介绍如何通过 Azure PowerShell 使用虚拟机规模集在 Windows VM 上创建和部署高度可用的应用程序
services: virtual-machine-scale-sets
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
origin.date: 11/07/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 89f282b2c0ee56194017c598d56dd5bb93a9a46f
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675165"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>教程：使用 Azure PowerShell 在 Windows 上创建虚拟机规模集和部署高度可用的应用
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数。 也可以定义规则，以便根据 CPU、内存需求或网络流量等资源使用情况进行自动缩放。 在本教程中，请在 Azure 中部署虚拟机规模集，并了解如何执行以下操作：

> [!div class="checklist"]
> * 使用自定义脚本扩展定义要缩放的 IIS 站点
> * 为规模集创建负载均衡器
> * 创建虚拟机规模集
> * 增加或减少规模集中的实例数
> * 创建自动缩放规则

<!--[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]-->

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 6.0.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="scale-set-overview"></a>规模集概述
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 规模集中的 VM 将分布在逻辑容错域和更新域的一个或多个*放置组*中。 放置组是配置类似的 VM 的组，与[可用性集](tutorial-availability-sets.md)相似。

可以根据需要在规模集中创建 VM。 可以定义自动缩放规则来控制如何以及何时在规模集中添加或删除 VM。 这些规则可以根据 CPU 负载、内存用量或网络流量等指标触发。

使用 Azure 平台映像时，规模集最多支持 1,000 个 VM。 对于有重要安装或 VM 自定义要求的工作负荷，可能需要[创建自定义 VM 映像](tutorial-custom-images.md)。 使用自定义映像时，在规模集中最多可以创建 300 个 VM。

## <a name="create-a-scale-set"></a>创建规模集
使用 [New-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmss) 创建虚拟机规模集。 以下示例创建名为 *myScaleSet* 且使用 *Windows Server 2016 Datacenter* 平台映像的规模集。 虚拟网络、公共 IP 地址和负载均衡器的 Azure 网络资源均会自动创建。 出现提示时，可以针对规模集中的 VM 实例设置自己的管理凭据：

```PowerShell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "ChinaEast" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。

## <a name="deploy-sample-application"></a>部署示例应用程序
若要测试规模集，请安装一个基本的 Web 应用程序。 使用 Azure 自定义脚本扩展下载并运行一个脚本，以便在 VM 实例上安装 IIS。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 有关详细信息，请参阅[自定义脚本扩展概述](extensions-customscript.md)。

使用自定义脚本扩展安装基本的 IIS Web 服务器。 应用可安装 IIS 的自定义脚本扩展，如下所示：

```PowerShell
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>允许流量发往应用程序

若要允许访问基本的 Web 应用程序，请使用 [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermnetworksecurityruleconfig) 和 [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermnetworksecuritygroup) 创建网络安全组。 有关详细信息，请参阅 [Azure 虚拟机规模集的网络](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md)。

```PowerShell
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location ChinaEast `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>测试规模集
若要查看运行中的规模集，请使用 [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例显示创建为规模集一部分的“myPublicIP”的 IP 地址：

```PowerShell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

将公共 IP 地址输入到 Web 浏览器中。 将显示 Web 应用，包括负载均衡器将流量分发到的 VM 的主机名：

![运行 IIS 网站](./media/tutorial-create-vmss/running-iis-site.png)

若要查看规模集的实际运行情况，可以强制刷新 Web 浏览器，以查看负载均衡器如何在运行应用的所有 VM 之间分发流量。

## <a name="management-tasks"></a>管理任务
在规模集的整个生命周期内，可能需要运行一个或多个管理任务。 此外，可能还需要创建自动执行各种生命周期任务的脚本。 Azure PowerShell 提供了一种用于执行这些任务的快速方法。 以下是一些常见任务。

### <a name="view-vms-in-a-scale-set"></a>查看规模集中的 VM
若要在规模集中查看 VM 实例的列表，请使用 [Get-AzureRmVmssVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)，如下所示：

```PowerShell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

以下示例输出显示了规模集中的两个 VM 实例：

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   chinaeast Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   chinaeast Standard_DS1_v2          1         Succeeded
```

若要查看特定 VM 实例的其他信息，请将 `-InstanceId` 参数添加到 [Get-AzureRmVmssVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)。 以下示例查看 VM 实例 *1* 的相关信息：

```PowerShell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="increase-or-decrease-vm-instances"></a>增加或减少 VM 实例
若要查看规模集中当前包含的实例数，请使用 [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) 并查询 sku.capacity：

```PowerShell
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

然后，可以使用 [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) 手动增加或减少规模集中虚拟机的数目。 以下示例将规模集中 VM 的数目设置为 *3*：

```PowerShell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

这将花费数分钟来更新规模集中指定数目的实例。

### <a name="configure-autoscale-rules"></a>配置自动缩放规则
你可以定义自动缩放规则，而不是手动缩放规模集中实例的数目。 这些规则监视规模集中的实例，并根据所定义的指标和阈值做出相应响应。 如果在 5 分钟内平均 CPU 负载高于 60%，以下示例将增加一个实例。 如果平均 CPU 负载低于 30% 且持续时间超过 5 分钟，则将减少一个实例：

```PowerShell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "China East"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

有关使用自动缩放的详细设计信息，请参阅[自动缩放最佳做法](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling)。

## <a name="next-steps"></a>后续步骤
在本教程中，你已创建了一个虚拟机规模集。 你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 使用自定义脚本扩展定义要缩放的 IIS 站点
> * 为规模集创建负载均衡器
> * 创建虚拟机规模集
> * 增加或减少规模集中的实例数
> * 创建自动缩放规则

请继续学习下一教程，详细了解虚拟机的负载均衡概念。

> [!div class="nextstepaction"]
> [对虚拟机进行负载均衡](tutorial-load-balancer.md)

<!--Update_Description: update meta properties, wording update -->