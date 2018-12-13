---
title: 使用 Azure 网络观察程序“IP 流验证”功能验证流量 - PowerShell | Azure
description: 本文介绍如何使用 PowerShell 检查系统是允许还是拒绝进出虚拟机的流量
services: network-watcher
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/22/2017
ms.date: 04/30/2018
ms.author: v-yeche
ms.openlocfilehash: eb2d4f28cca3610920c2feb4b19a89ac87096cc4
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648966"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 网络观察程序的组件（即“IP 流验证”功能）检查系统是允许还是拒绝进出 VM 的流量

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

“IP 流验证”是网络观察程序的一项功能，用于验证是否允许流量进出虚拟机。 此方案用于获取虚拟机的当前状态，即其是否可以与外部资源或后端通信。 可以使用“IP 流验证”功能验证网络安全组 (NSG) 规则是否正确配置，以及对 NSG 规则所阻止的流进行故障排除。 使用“IP 流验证”功能的另一个原因是确保要阻止的流量已由 NSG 正确阻止。

## <a name="before-you-begin"></a>准备阶段

此方案假定用户已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序，或者已经有网络观察程序的实例。 此方案还假定要使用的包含有效虚拟机的资源组已存在。

## <a name="scenario"></a>方案

此方案使用“IP 流验证”功能来验证虚拟机是否可以与已知的必应 IP 地址通信。 如果流量被拒绝，此方案将返回拒绝该流量的安全规则。 若要了解有关 IP 流验证的详细信息，请访问 [IP 流验证概述](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>检索网络观察程序

第一步是检索网络观察程序实例。 将 `$networkWatcher` 变量传递给 IP 流验证 cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "chinanorth" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>获取 VM

“IP 流验证”功能测试从虚拟机上的 IP 地址发往远程目标或从远程目标发往虚拟机上的 IP 地址的流量。 cmdlet 需要虚拟机的 ID。 如果已知道要使用的虚拟机的 ID，则可以跳过此步骤。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>获取 NIC

需要虚拟机上的 NIC 的 IP 地址。 使用下面的命令检索附加到虚拟机的 NIC。 如果已知道要在虚拟机上测试的 IP 地址，则可以跳过此步骤。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>运行“IP 流验证”

运行 `Test-AzureRmNetworkWatcherIPFlow` cmdlet 测试流量。 在此示例中，将使用第一个 NIC 上的第一个 IP 地址。

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> 若要运行“IP 流验证”功能，VM 资源必须已分配。

## <a name="review-results"></a>查看结果

运行 `Test-AzureRmNetworkWatcherIPFlow` 之后将返回结果。以下示例是从前一步骤返回的结果。

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>后续步骤

如果流量不应被阻止但却被阻止，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)，查找已定义的网络安全组和安全规则。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png


<!--Update_Description: updata link, wording update -->