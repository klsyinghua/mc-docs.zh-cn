---
title: 缩放 Azure SignalR 服务的实例
description: 了解如何通过 Azure 门户或 Azure CLI 缩放 Azure SignalR 服务实例，以添加或减少容量。
author: sffamily
ms.service: signalr
ms.topic: conceptual
origin.date: 11/21/2019
ms.date: 12/16/2019
ms.author: v-tawe
ms.openlocfilehash: 8833f7a1b7ea1cc21a024ff9cf5c49b972327823
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348540"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>如何缩放 Azure SignalR 服务实例？
本文介绍如何缩放 Azure SignalR 服务的实例。 有两种方案可用于缩放、纵向扩展和横向扩展。

* [纵向扩展](https://wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多单位、连接、消息等。 可以通过将定价层从“免费”更改为“标准”进行纵向扩展。
* [横向扩展](https://wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加 SignalR 单位数。 最多可以横向扩展到 100 个单位。

应用缩放设置需要花费几分钟时间。 这些设置不需要更改代码或重新部署服务器应用程序。

有关单个 SignalR 服务的定价和容量信息，请参阅 [Azure SignalR 服务定价详细信息](https://www.azure.cn/pricing/details/signalr-service/)。  

> [!NOTE]
> 将 SignalR 服务从“免费”层更改为“标准”层（或反之）会更改公共服务 IP。在整个 Internet 中将更改传播到 DNS 服务器通常需要 3-60 分钟。   在更新 DNS 之前，服务可能不可访问。 一般我们不建议经常更改定价层。


## <a name="scale-on-azure-portal"></a>在 Azure 门户中缩放

1. 在浏览器中，打开 [Azure 门户](https://portal.azure.cn)。

2. 在 SignalR 服务页上的左侧菜单中，选择“缩放”。 
   
3. 选择定价层，然后单击“选择”。  需要设置“标准”层的单位计数。 
   
    ![在门户中缩放](./media/signalr-howto-scale/signalr-howto-scale.png)

4. 单击“保存”  。

## <a name="scale-using-azure-cli"></a>使用 Azure CLI 进行缩放

此脚本将创建“免费”层的新 SignalR 服务资源和新的资源组，并将此资源纵向扩展到“标准”层。   

```azurecli
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location chinaeast

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

记下为新资源组生成的实际名称。 如果要删除所有组资源，将使用该资源组名称。

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>比较定价层

有关详细信息，例如每个定价层包含的消息和连接，请参阅 [SignalR Service 定价详细信息](https://www.azure.cn/pricing/details/signalr-service/)。

有关每个层中的服务限制、配额和约束表格，请参阅 [SignalR 服务限制](../azure-subscription-service-limits.md#azure-signalr-service-limits)。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何缩放单个 SignalR 服务实例。

缩放、分片和跨区域方案还支持多个终结点。

> [!div class="nextstepaction"]
> [使用多个实例缩放 SignalR 服务](./signalr-howto-scale-multi-instances.md)