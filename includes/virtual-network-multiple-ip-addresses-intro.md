---
title: include 文件
description: include 文件
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/09/2018
ms.date: 05/07/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2a5f6f4d5164bfbdb463d902a85d7b753d8ddcbd
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52646077"
---
> [!div class="op_single_selector"]
> * [Azure 门户](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

在一个 Azure 虚拟机 (VM) 上可以附加一个或多个网络接口 (NIC)。 可为任何 NIC 分配一个或多个静态/动态的公共和专用 IP 地址。 为 VM 分配多个 IP 地址可实现以下功能：

* 在一台服务器上托管具有不同 IP 地址和 SSL 证书的多个网站或服务。
* 用作网络虚拟设备，例如防火墙或负载均衡器。
* 能够将任何 NIC 的任意专用 IP 地址添加到 Azure 负载均衡器后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要了解有关如何对多个 IP 配置进行负载均衡的详细信息，请阅读 [对多个 IP 配置进行负载均衡](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fvirtual-network%2ftoc.json) 一文。

每个附加到 VM 的 NIC 都具有一个或多个与之关联的 IP 配置。 系统会为每个配置分配一个静态或动态专用 IP 地址。 每个配置还可拥有一个与之关联的公共 IP 地址资源。 系统会为公共 IP 地址资源分配一个动态或静态公共 IP 地址。 若要详细了解 Azure 中的 IP 地址，请阅读 [Azure 中的 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)一文。 

分配给 NIC 的专用 IP 地址数目存在限制。 能够在 Azure 订阅中使用的公共 IP 地址数也存在限制。 有关详细信息，请参阅 [Azure 限制](../articles/azure-subscription-service-limits.md?toc=%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。
