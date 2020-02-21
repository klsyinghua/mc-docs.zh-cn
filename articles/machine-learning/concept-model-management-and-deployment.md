---
title: MLOps：ML 模型管理
titleSuffix: Azure Machine Learning
description: '了解如何使用 Azure 机器学习进行模型管理 (MLOps)。 部署、管理和监视模型以持续对其做出改善。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: a766b4c5c110cd3d2fcd116fb875d0fe7136bb64
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599061"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps：使用 Azure 机器学习进行模型管理、部署和监视

本文介绍如何使用 Azure 机器学习来管理模型的生命周期。 Azure 机器学习使用机器学习操作 (MLOps) 方法。 MLOps 改善了机器学习解决方案的质量和一致性。 

Azure 机器学习提供以下 MLOps 功能：

- **创建可重现的 ML 管道**。 使用管道可为数据准备、训练和评分过程定义可重复且可重用的步骤。
- **从任意位置注册、打包和部署模型**，并跟踪使用模型所需的关联元数据。
- **捕获所需的监管数据以捕获端到端的 ML 生命周期**，包括模型的发布者、做出更改的原因，以及在生产环境中部署或使用模型的时间。
- **针对 ML 生命周期中的事件发出通知和警报**，例如，试验完成、模型注册、模型部署和数据偏移检测。
- **监视 ML 应用程序中的操作和 ML 相关问题**。 比较训练与推理之间的模型输入，浏览特定于模型的指标，以及针对 ML 基础结构提供监视和警报。
- **使用 Azure 机器学习和 Azure DevOps 自动化端到端的 ML 生命周期**，以频繁更新模型、测试新模型，以及连同其他应用程序和服务持续推出新的 ML 模型。

## <a name="create-reproducible-ml-pipelines"></a>创建可重现的 ML 管道

使用 Azure 机器学习中的 ML 管道，将模型训练过程涉及的所有步骤汇聚到一起。

ML 管道可以包含从数据准备、到特征提取、到超参数优化、再到模型评估的所有步骤。 有关详细信息，请参阅 [ML 管道](service/concept-ml-pipelines.md)。

如果使用[设计器](service/concept-designer.md)创建 ML 管道，则随时可以单击设计器页面右上角的“...”，然后选择“克隆”。   克隆管道可以迭代管道设计，而不会丢失旧版本。  

## <a name="register-package-and-deploy-models-from-anywhere"></a>从任意位置注册、打包和部署模型

### <a name="register-and-track-ml-models"></a>注册和跟踪 ML 模型

通过模型注册，可在 Azure 云的工作区中存储模型并确定模型版本。 使用模型注册表，可轻松组织和跟踪定型的模型。

> [!TIP]
> 已注册的模型是构成模型的一个或多个文件的逻辑容器。 例如，如果你有一个存储在多个文件中的模型，则可以在 Azure 机器学习工作区中将这些文件注册为单个模型。 注册后，可以下载或部署已注册的模型，并接收注册的所有文件。

按名称和版本标识已注册的模型。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 在注册期间可以提供其他元数据标记。 然后，可以在搜索模型时使用这些标记。 Azure 机器学习支持可以使用 Python 3.5.2 或更高版本加载的任何模型。

> [!TIP]
> 还可以注册在 Azure 机器学习外部训练的模型。

无法删除在活动部署中使用的已注册模型。
有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)的注册模型部分。

### <a name="package-and-debug-models"></a>打包和调试模型

在将模型部署到生产环境之前，需将其打包成 Docker 映像。 大多数情况下，映像创建操作会在部署期间在后台自动发生。 可以手动指定映像。

如果部署时遇到问题，可以在本地开发环境中部署，以进行故障排除和调试。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)和[排查部署问题](how-to-troubleshoot-deployment.md)。

### <a name="validate-and-profile-models"></a>验证和分析模型

Azure 机器学习可以使用分析功能来确定在部署模型时要使用的理想 CPU 和内存设置。 在此过程中，将使用你为分析过程提供的数据执行模型验证。

### <a name="convert-and-optimize-models"></a>转换和优化模型

将模型转换为 [Open Neural Network Exchange](https://onnx.ai) (ONNX) 可以提高性能。 一般情况下，转换为 ONNX 可将性能提高 2 倍。

有关包含 Azure 机器学习的 ONNX 的详细信息，请参阅[创建和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

已训练的机器学习模型将在云中或本地部署为 Web 服务。 也可以将模型部署到 Azure IoT Edge 设备。 部署使用 CPU、GPU 或现场可编程门阵列 (FPGA) 进行推理。 也可以使用 Power BI 中的模型。

将模型用作 Web 服务或 IoT Edge 设备时，需提供以下项：

* 用于对提交到服务/设备的数据进行评分的模型。
* 一个入口脚本。 此脚本接受请求，使用模型对数据评分，然后返回响应。
* 用于描述模型和入口脚本所需的依赖项的 conda 环境文件。
* 模型和入口脚本所需的任何其他资产，例如文本、数据等。

还需要提供目标部署平台的配置。 例如，部署到 Azure Kubernetes 服务时的 VM 系列类型、可用内存和核心数。

创建映像时，还会添加 Azure 机器学习所需的组件。 例如，运行 Web 服务以及与 IoT Edge 交互所需的资产。

#### <a name="batch-scoring"></a>批处理评分
支持通过 ML 管道进行批量评分。 有关详细信息，请参阅[针对大数据的批量预测](how-to-run-batch-predictions.md)。

#### <a name="real-time-web-services"></a>实时 Web 服务

可以在包含以下计算目标的 **Web 服务**中使用模型：

* Azure 容器实例
* Azure Kubernetes 服务
* 本地开发环境

若要将模型部署为 Web 服务，必须提供以下项：

* 模型或模型系综。
* 使用模型所需的依赖项。 例如，接受请求并调用模型、conda 依赖项等的脚本。
* 用于描述如何以及在何处部署模型的部署配置。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT Edge 设备

可以通过 **Azure IoT Edge 模块**在 IoT 设备中使用模型。 IoT Edge 模块将部署到支持推理或模型评分的硬件设备。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支持使用机器学习模型进行数据分析。 有关详细信息，请参阅 [Power BI 中的 Azure 机器学习集成（预览版）](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>捕获所需的监管数据以捕获端到端的 ML 生命周期

Azure ML 提供用于跟踪所有 ML 资产的端到端审核线索的功能。 具体而言：

- Azure ML [与 Git 集成](how-to-set-up-training-targets.md#gitintegration)，可跟踪有关存储库/分支/提交代码的来源位置的信息。
- [Azure ML 数据集](how-to-create-register-datasets.md)可帮助你跟踪、分析数据及控制其版本。 
- Azure ML 运行历史记录存储用于训练模型的代码、数据和计算的快照。
- Azure ML 模型注册表捕获与模型关联的所有元数据（训练该模型的试验、模型的部署位置、其部署是否正常）。

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>针对 ML 生命周期中的事件发出通知和警报以及进行自动化处理
Azure ML 将关键事件发布到 Azure 事件网格。使用事件网格可以针对 ML 生命周期中的事件发出通知并自动采取措施。 有关详细信息，请参阅[此文档](how-to-use-event-grid.md)。


## <a name="monitor-for-operational--ml-issues"></a>监视操作和 ML 问题

使用监视功能可以了解正在将哪些数据发送到模型，以及模型返回的预测。

此信息可帮助你了解模型的使用方式。 收集的输入数据还可以在训练模型的未来版本时使用。

有关详细信息，请参阅[如何启用模型数据收集](how-to-enable-data-collection.md)。

## <a name="automate-the-ml-lifecycle"></a>自动化 ML 生命周期 

可以使用 GitHub 和 Azure Pipelines 来创建用于训练模型的持续集成过程。 在典型方案中，当数据科学家将某项更改签入项目的 Git 存储库时，Azure 管道将启动训练运行。 然后，可以检查该运行的结果，以了解已训练模型的性能特征。 还可以创建一个管道用于将模型部署为 Web 服务。

安装 [Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)可以更轻松地使用 Azure Pipelines。 该扩展为 Azure Pipelines 提供以下增强：

* 定义服务连接时启用工作区选择。
* 使发布管道可由训练管道中创建的已训练模型触发。

有关将 Azure Pipelines 与 Azure 机器学习配合使用的详细信息，请参阅[使用 Azure Pipelines 实现 ML 模型的持续集成和部署](https://docs.microsoft.com/azure/azure/devops/pipelines/targets/azure-machine-learning)一文，以及 [Azure 机器学习 MLOps](https://aka.ms/mlops) 存储库。

## <a name="next-steps"></a>后续步骤

阅读并探索以下资源来了解详细信息：

+ [如何以及在何处使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)

+ [教程：在 ACI 中部署图像分类模型](tutorial-deploy-models-with-aml.md)。

+ [端到端 MLOps 示例存储库](https://github.com/microsoft/MLOps)

+ [使用 Azure Pipelines 实现 ML 模型的 CI/CD](https://docs.microsoft.com/azure/azure/devops/pipelines/targets/azure-machine-learning)

+ 创建[使用已部署的模型](how-to-consume-web-service.md)的客户端

+ [规模化机器学习](https://docs.microsoft.com/azure/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 参考体系结构和最佳做法存储库](https://github.com/microsoft/AI)