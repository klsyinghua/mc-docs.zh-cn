---
title: 使用 Azure 网络观察程序“IP 流验证”功能验证流量 - Azure CLI | Azure
description: 本文介绍如何使用 Azure CLI 检查是允许还是拒绝进出虚拟机的流量
services: network-watcher
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/22/2017
ms.date: 04/30/2018
ms.author: v-yeche
ms.openlocfilehash: 340b628e0becd47b06682f7c83681bfc3dabdf3c
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52653661"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 网络观察程序的组件（即“IP 流验证”功能）检查是允许还是拒绝进出 VM 的流量

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

“IP 流验证”是网络观察程序的一项功能，用于验证是否允许流量进出虚拟机。 此方案用于获取虚拟机的当前状态，即其是否可以与外部资源或后端通信。 可以使用“IP 流验证”功能验证网络安全组 (NSG) 规则是否正确配置，以及对 NSG 规则所阻止的流进行故障排除。 使用“IP 流验证”功能的另一个原因是确保要阻止的流量已由 NSG 正确阻止。

本文使用资源管理部署模型的新一代 CLI (Azure CLI 2.0)，其适用于 Windows、Mac 和 Linux。

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-latest)。

## <a name="before-you-begin"></a>准备阶段

此方案假定用户已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序，或者已经有网络观察程序的实例。 此方案还假定要使用的包含有效虚拟机的资源组已存在。

## <a name="scenario"></a>方案

此方案使用“IP 流验证”功能来验证虚拟机是否可以与已知的必应 IP 地址通信。 如果流量被拒绝，此方案将返回拒绝该流量的安全规则。 若要了解有关 IP 流验证的详细信息，请访问 [IP 流验证概述](network-watcher-ip-flow-verify-overview.md)

## <a name="get-a-vm"></a>获取 VM

“IP 流验证”功能测试从虚拟机上的 IP 地址发往远程目标或从远程目标发往虚拟机上的 IP 地址的流量。 cmdlet 需要虚拟机的 ID。 如果已知道要使用的虚拟机的 ID，则可以跳过此步骤。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]


```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>获取 NIC

需要虚拟机上的 NIC 的 IP 地址。 使用下面的命令检索附加到虚拟机的 NIC。 如果已知道要在虚拟机上测试的 IP 地址，则可以跳过此步骤。

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>运行“IP 流验证”

运行 `az network watcher test-ip-flow` cmdlet 测试流量。 在此示例中，将使用第一个 NIC 的第一个 IP 地址。

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> 若要运行“IP 流验证”功能，VM 资源必须已分配。

## <a name="review-results"></a>查看结果

运行 `az network watcher test-ip-flow` 之后将返回结果。以下示例是从前一步骤返回的结果。

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>后续步骤

如果流量被阻止且不应被阻止，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)找到定义的网络安全组和安全规则。

访问[使用网络观察程序审核网络安全组 (NSG)](network-watcher-nsg-auditing-powershell.md)，了解如何审核 NSG 设置。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

<!--Update_Description: update meta properties, update link -->