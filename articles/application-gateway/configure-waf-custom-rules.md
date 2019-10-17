---
title: 使用 Azure PowerShell 配置 Web 应用程序防火墙 v2 自定义规则
description: 了解如何使用 Azure PowerShell 配置 WAF v2 自定义规则
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 06/18/2019
ms.date: 09/10/2019
ms.author: v-junlch
ms.openlocfilehash: 9dad0c9c7a5f04461bc492e9a0a5bc83736c1f7a
ms.sourcegitcommit: 843028f54c4d75eba720ac8874562ab2250d5f4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70857419"
---
# <a name="configure-web-application-firewall-v2--with-a-custom-rule-using-azure-powershell"></a>通过 Azure PowerShell 使用自定义规则配置 Web 应用程序防火墙 v2

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

自定义规则允许你创建自己的规则，对通过 Web 应用程序防火墙 (WAF) v2 进行传递的每个请求进行评估。 这些规则的优先级高于托管规则集中的其余规则。 自定义规则具有一个操作（允许或阻止）、一个匹配条件和一个运算符以允许完全自定义。

本文创建使用自定义规则的应用程序网关 WAF v2。 如果请求标头包含用户代理 *evilbot*，该自定义规则会阻止流量。

若要查看更多自定义规则示例，请参阅[创建和使用自定义 Web 应用程序防火墙规则](create-custom-waf-rules.md)

如果你想在一个可以复制、粘贴和运行的连续脚本中运行本文中的 Azure PowerShell，请参见 [Azure 应用程序网关 PowerShell 示例](powershell-samples.md)。

## <a name="prerequisites"></a>先决条件

### <a name="azure-powershell-module"></a>Azure PowerShell 模块

如果选择在本地安装并使用 Azure PowerShell，则此脚本需要安装 Azure PowerShell 模块 2.1.0 或更高版本。

1. 若要查找版本，请运行 `Get-Module -ListAvailable Az`。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。
2. 若要创建与 Azure 的连接，请运行 `Connect-AzAccount -Environment AzureChinaCloud`。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="example-script"></a>示例脚本

### <a name="set-up-variables"></a>设置变量

```azurepowershell
$rgname = "CustomRulesTest"

$location = "China North"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>创建资源组

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>创建 VNet

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>创建静态公共 VIP

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>创建池和前端端口

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.chinanorth.chinacloudapp.cn, testbackend2.chinanorth.chinacloudapp.cn

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>创建侦听器、http 设置、规则和自动缩放

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>创建自定义规则并将其应用于 WAF 策略

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-the-application-gateway"></a>创建应用程序网关

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>后续步骤

[详细了解 Web 应用程序防火墙](waf-overview.md)
