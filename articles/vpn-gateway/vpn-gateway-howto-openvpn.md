---
title: 如何在 Azure VPN 网关上配置 OpenVPN：PowerShell
description: 为 Azure VPN 网关配置 OpenVPN 的步骤
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 05/21/2019
ms.date: 02/10/2020
ms.author: v-jay
ms.openlocfilehash: 4ca0321b8d896b379f3dfa8779a59f0f66880c24
ms.sourcegitcommit: 925c2a0f6c9193c67046b0e67628d15eec5205c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068555"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>为 Azure 点到站点 VPN 网关配置 OpenVPN

本文可帮助你在 Azure VPN 网关上设置 **OpenVPN® 协议**。 本文假设你已拥有一个可正常工作的点到站点环境。 如果并非如此，请按照步骤 1 中的说明来创建点到站点 VPN。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1.创建点到站点 VPN

如果还没有正常运行的“点到站点”环境，请按照说明创建一个。 请参阅[创建点到站点 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，以创建和配置使用本机 Azure 证书身份验证的点到站点 VPN 网关。 

> [!IMPORTANT]
> OpenVPN 不支持基本 SKU。

## <a name="enable"></a>2.在网关上启用 OpenVPN

在网关上启用 OpenVPN。 在运行以下命令之前，请确保已为点到站点（IKEv2 或 SSTP）配置网关：

```azurepowershell
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>后续步骤

若要为 OpenVPN 配置客户端，请参阅[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

**“OpenVPN”是 OpenVPN Inc. 的商标。**