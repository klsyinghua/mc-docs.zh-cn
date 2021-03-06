---
title: 适用于 Azure 和 Azure Stack 的混合模式与解决方案示例
description: 混合模式和解决方案示例的概述，可帮助用户在 Azure 和 Azure Stack 上学习和构建混合解决方案。
author: WenJason
ms.service: azure-stack
ms.topic: overview
origin.date: 11/05/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 35b6e311673180f07b1a027643860f92d3a297bd
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020568"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>适用于 Azure 和 Azure Stack 的混合模式与解决方案示例

Microsoft 通过单个一致的 Azure 生态系统提供 Azure 和 Azure Stack 产品与解决方案。 Azure Stack 系列是 Azure 的扩展。 

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>混合云和混合应用

Azure Stack 通过实现混合云来为本地环境和 Edge 提供云计算的敏捷性。  Azure Stack Hub、Azure Stack HCI 和 Azure Stack Edge 将 Azure 从云扩展到主权数据中心、分支机构、现场和更远的范围。 利用这组多样化的功能，可以：

- 重复使用代码并在 Azure 与本地环境中一致地运行云原生应用。
- 使用 Azure 服务的可选连接来运行传统的虚拟化工作负荷。
- 将数据传输到云，或将数据保留在主权数据中心，以保持合规。
- 运行硬件加速的机器学习、容器化或虚拟化工作负荷，所有这些操作都可以在智能边缘进行。

跨云的应用程序也称为*混合应用程序*。 你可以在 Azure 中构建混合云应用，并将其部署到位于任何位置的已连接或已断开连接的数据中心。

混合应用程序方案根据可用于开发的资源而有很大的不同。 此外，它们根据地理位置、安全性、Internet 访问等考虑因素而各不相同。 尽管此处所述的模式和解决方案可能无法解决所有要求，但提供了指导原则和示例供用户探索，并可以在实施混合解决方案时重复使用。

## <a name="design-patterns"></a>设计模式

设计模式从真实的客户方案和经验中得出一般化且可重复的设计指导。 模式是抽象的，可适用于不同类型的方案或垂直行业。 每种模式阐述了上下文和问题，并提供解决方案示例的概述。 解决方案示例旨在用作模式的可能实施方案。

模式文章有两种类型：

- 单模式：提供适用于单个通用方案的设计指导。
- 多模式：提供使用多模式应用程序的设计指导。 为了解决更复杂的方案或行业特定的问题，我们经常需要使用这种模式。

## <a name="solution-deployment-guides"></a>解决方案部署指南

分步式部署指南可帮助用户部署解决方案示例。 该指南也可能会参考 GitHub [解决方案示例存储库](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)中存储的随附代码示例。 

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure Stack 产品和解决方案系列](/azure-stack)详细了解产品和解决方案的整个阵容。
- 浏览目录中的“模式”和“解决方案部署指南”部分，以详细了解每种模式和解决方案。
- 阅读[混合应用程序设计注意事项](overview-app-design-considerations.md)，以了解设计、部署和操作混合应用程序时的软件质量要素。
- [在 Azure Stack 上设置开发环境](../user/azure-stack-dev-start.md)和在 Azure Stack 上[部署第一个应用](../user/azure-stack-dev-start-deploy-app.md)。
