---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能。
services: stream-analytics
author: lingliw
ms.author: v-lingwu
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 10/30/2019
ms.date: 1/6/2020
ms.openlocfilehash: 2d5e97bf4642bbc2cf56b4c70f5b6a7b5a6adde9
ms.sourcegitcommit: e0b57f74aeb9022ccd16dc6836e0db2f40a7de39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75855663"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="public-previews"></a>公共预览版

以下功能以公共预览版提供。 现在可以使用这些功能，但请勿在生产环境中使用它们。

### <a name="online-scaling"></a>联机缩放

进行联机缩放时，在需要更改 SU 分配的情况下无需停止作业。 可以增加或减少正在运行的作业的 SU 容量，但无需停止它。 这基于流分析目前提供的针对长时间运行的关键管道的客户承诺。 有关详细信息，请参阅[配置 Azure 流分析流式处理单元](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)。

### <a name="c-custom-de-serializers"></a>C# 自定义反序列化程序
开发人员可以利用 Azure 流分析的强大功能来处理 Protobuf 格式、XML 格式或任何自定义格式的数据。 可以在 C# 中实现[自定义反序列化程序](custom-deserializer-examples.md)，然后使用它对 Azure 流分析接收的事件进行反序列化。

### <a name="extensibility-with-c-custom-code"></a>使用 C# 自定义代码的可扩展性

在云中或 IoT Edge 上创建流分析模块的开发人员可以编写或重复使用自定义 C# 函数，并在查询中通过 [用户定义函数](stream-analytics-edge-csharp-udf-methods.md)直接调用这些函数。

### <a name="managed-identity-authentication-with-power-bi"></a>Power BI 的托管标识身份验证

对于通过 Power BI 进行的基于托管标识的身份验证（用于获取动态仪表板操作体验），Azure 流分析提供完全支持。

### <a name="anomaly-detection"></a>异常检测

Azure 流分析的机器学习模型除了支持双向、慢正和慢负趋势检测外，还支持“峰值”和“低值”检测   。 有关详细信息，请访问 [Azure 流分析中的异常情况检测](stream-analytics-machine-learning-anomaly-detection.md)。

### <a name="debug-query-steps-in-visual-studio"></a>在 Visual Studio 中调试查询步骤

在适用于 Visual Studio 的 Azure 流分析工具中执行本地测试时，可以轻松预览数据关系图上的中间行集。 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>针对 Visual Studio Code 中的实时数据进行本地测试

将作业提交到 Azure 之前，可以针对本地计算机上的实时数据测试查询。 每个测试迭代平均使用不到 2 到 3 秒的时间，因此开发过程非常高效。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>适用于 Azure 流分析的 Visual Studio Code

可以在 Visual Studio Code 中创建 Azure 流分析作业。 请参阅我们的 [VS Code 入门教程](/stream-analytics/quick-create-vs-code)。


### <a name="anomaly-detection"></a>异常检测

Azure 流分析引入了新的机器学习模型，除了支持双向、慢正和慢负趋势检测外，还支持“峰值”和“低值”检测   。 

### <a name="integration-with-azure-machine-learning"></a>与 Azure 机器学习集成

可使用机器学习 (ML) 函数缩放流分析作业。 若要详细了解如何在流分析作业中使用 ML 函数，请访问[使用 Azure 机器学习函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)。 查看[使用 Azure 流分析和 Azure 机器学习执行情绪分析](stream-analytics-machine-learning-integration-tutorial.md)的实际方案。


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 用户定义函数

使用 .NET Standard 用户定义函数，可以将 .NET Standard 代码作为流式管道的一部分运行。 可以创建简单的 C# 类或导入完整的项目和库。 Visual Studio 支持完整的创作和调试体验。 有关详细信息，请访问[为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他预览版功能

以下功能也可根据要求在预览版中使用。

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>通过 Azure 机器学习管理的自定义 ML 模型提供实时高性能评分

Azure 流分析利用预先训练的自定义机器学习模型来支持高性能实时评分，这些模型通过 Azure 机器学习进行管理，在 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 中进行托管，不需编写代码。 [注册](https://aka.ms/asapreview1)即可获取预览版

### <a name="support-for-azure-stack"></a>支持 Azure Stack
此功能在 Azure IoT Edge 运行时上启用，可利用自定义 Azure Stack 功能，例如对在 Azure Stack 上运行的本地输入和输出的本机支持（例如，事件中心、IoT 中心、Blob 存储）。 利用这一新的集成，可以构建混合体系结构，可以在数据生成位置附近分析数据，从而降低延迟并最大限度地提高见解。
使用此功能，可以构建混合体系结构，可以在数据生成位置附近分析数据，从而降低延迟并最大限度地获取见解。 必须[注册](https://aka.ms/asapreview1)才能获取此预览版。
