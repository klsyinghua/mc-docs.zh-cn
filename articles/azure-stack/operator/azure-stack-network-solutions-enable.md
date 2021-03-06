---
title: 使用 Fortinet FortiGate 在 Azure Stack 中提供网络解决方案 | Microsoft Docs
description: 了解如何使用 Fortinet FortiGate 在 Azure Stack 中启用网络解决方案
services: azure-stack
author: WenJason
ms.service: azure-stack
ms.topic: how-to
origin.date: 09/30/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 7a8ca873d4b3edf592881b000cb97220bc608201
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020370"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>使用 Fortinet FortiGate 在 Azure Stack 中提供网络解决方案

可以将 FortiGate 下一代防火墙添加到 Azure Stack 市场。 FortiGate 使用户能够创建网络解决方案，例如 Azure Stack 的虚拟专用网络 (VPN) 和 VNET 对等互连。 网络虚拟设备 (NVA) 控制从外围网络流到其他网络或子网的网络流量。 

有关 Azure 市场中的 FortiGate 的详细信息，请参阅 [Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://market.azure.cn/zh-cn/marketplace/apps/fortinet-cn.fortinet_fortigate-vm_v6_0?tab=Overview)。

## <a name="download-the-required-azure-stack-marketplace-items"></a>下载所需的 Azure Stack 市场项

1.  打开 Azure Stack 管理员门户。

2.  依次选择“市场管理”  、“从 Azure 添加”  。

3. 在搜索框中键入 `Forti`，并双击“>”选择“下载”  以获取以下项的最新可用版本： 
    - 适用于 Azure BYOL 的 Fortinet FortiGate-VM
    - FortiGate NGFW - 单一 VM 部署 (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  等到市场项的状态为“已下载”  。 下载这些项可能需要几分钟。

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>后续步骤

[使用 FortiGate NVA 设置 Azure Stack 的 VPN](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[如何通过对等互连连接两个 VNET](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[如何使用 Fortinet FortiGate NVA 建立 VNET 到 VNET 的连接](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
