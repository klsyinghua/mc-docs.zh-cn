---
title: 什么是工作区
titleSuffix: Azure Machine Learning
description: 工作区是 Azure 机器学习的顶级资源。 它保留所有训练运行的历史记录，包括日志、指标、输出和脚本快照。 使用此信息可以确定哪个训练运行产生最佳模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: f4bb62536de9bd6164b7f4c7b2f915b867d8123a
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599059"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什么是 Azure 机器学习工作区？

工作区是 Azure 机器学习的顶级资源，为使用 Azure 机器学习时创建的所有项目提供了一个集中的处理位置。  工作区保留所有训练运行的历史记录，包括日志、指标、输出和脚本快照。 使用此信息可以确定哪个训练运行产生最佳模型。  

拥有了所需的模型后，可将其注册到工作区。 然后，使用已注册的模型和评分脚本，将其部署到 Azure 容器实例、Azure Kubernetes 服务中或部署到现场可编程门阵列 (FPGA) 作为基于 REST 的 HTTP 终结点。 还可以将模型作为模块部署到 Azure IoT Edge 设备。

定价和可用的功能取决于为工作区选择了[普通版还是企业版](overview-what-is-azure-ml.md#sku)。 在[创建工作区](#create-workspace)时需要选择版本。  也可以从普通版[升级](#upgrade)到企业版。

## <a name="taxonomy"></a>分类 

下图演示了工作区的分类：

[![工作区分类](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

此图显示了工作区的以下组件：

+ 工作区可以包含 [Azure 机器学习计算实例](concept-compute-instance.md)和配置了运行 Azure 机器学习所需的 Python 环境的云资源。

+ [用户角色](how-to-assign-roles.md)使你可以与其他用户、团队或项目共享你的工作区。
+ [计算目标](concept-azure-machine-learning-architecture.md#compute-targets)用于运行试验。
+ 创建工作区的同时还会创建[关联的资源](#resources)。
+ [试验](concept-azure-machine-learning-architecture.md#experiments)是用于构建模型的训练用运行。  
+ [管道](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重复使用的工作流，用于训练和重新训练模型。
+ [数据集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)帮助管理用于模型训练和管道创建的数据。
+ 有了要部署的模型后，需要创建一个已注册的模型。
+ 使用已注册的模型和评分脚本创建[部署终结点](concept-azure-machine-learning-architecture.md#endpoints)。

## <a name="tools-for-workspace-interaction"></a>用于进行工作区交互的工具

可以通过以下方式与工作区交互：

+ 在 Web 上：
    + [Azure 机器学习工作室](https://ml.azure.com) 
    + [Azure 机器学习设计器（预览版）](concept-designer.md)- 仅在[企业版](overview-what-is-azure-ml.md#sku)工作区中可用。
+ 在任何 Python 环境中使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
+ 在任何 R 环境中使用[适用于 R 的 Azure 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)。
+ 在命令行上使用 Azure 机器学习 [CLI 扩展](/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>使用工作区进行机器学习

机器学习任务将项目读取到和/或写入工作区。

+ 运行试验以训练模型 - 将试验运行结果写入工作区。
+ 使用自动机器学习训练模型 - 将训练结果写入工作区。
+ 在工作区中注册模型。
+ 部署模型 - 使用注册的模型来创建部署。
+ 创建并运行可重用的工作流。
+ 查看机器学习项目，如试验、管道、模型和部署。
+ 跟踪和监视模型。

## <a name="workspace-management"></a>工作区管理

还可以执行以下工作区管理任务：

| 工作区管理任务   | 门户              | 工作室 | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| 创建工作区        | **&check;**     | | **&check;** | **&check;** |
| 管理工作区访问权限    | **&check;**   || |  **&check;**    |
| 升级到企业版    | **&check;** | **&check;**  | |     |
| 创建和管理计算资源    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| 创建笔记本 VM |   | **&check;** | |     |

> [!NOTE]
> 计算实例仅适用于区域为“美国中北部”或“英国南部”的工作区   。
>如果你的工作区在任何其他区域，则可以继续创建并使用[笔记本 VM](concept-compute-instance.md#notebookvm)。

## <a name='create-workspace'></a> 创建工作区

创建工作区时，可以选择使用[基本版或企业版](overview-what-is-azure-ml.md#sku)来创建工作区。 版本确定工作区中可用的功能。 企业版的突出功能包括可以访问 [Azure 机器学习设计器](concept-designer.md)和提供工作室版本的[自动机器学习试验](tutorial-first-experiment-automated-ml.md)构建功能。  有关详细信息和定价信息，请参阅 [Azure 机器学习定价](https://www.azure.cn/pricing/details/machine-learning/)。

可通过多种方式创建工作区：  

* 使用 [Azure 门户](how-to-manage-workspace.md)作为点击界面来逐步完成每个步骤。
* 使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 从 Python 脚本或 Jupiter 笔记本动态创建工作区
* 需要按企业安全标准自动创建或自定义创建时，请使用 [Azure 资源管理器模板](how-to-create-workspace-template.md)或 [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)。
* 如果使用 Visual Studio Code，请使用 [VS Code 扩展](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)。

## <a name="upgrade"></a> 升级到企业版

可使用 Azure 门户将[工作区从基本版升级到企业版](how-to-manage-workspace.md#upgrade)。 不能将企业版工作区降级为基本版工作区。 

## <a name="resources"></a> 关联的资源

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

+ [Azure 容器注册表](/container-registry/)：注册在训练期间和部署模型时使用的 Docker 容器。 要最大程度地降低成本，ACR 在创建部署映像之前会“延迟加载”  。
+ [Azure 存储帐户](/storage/)，用作工作区的默认数据存储。  与 Azure 机器学习计算实例一起使用的 Jupyter 笔记本也存储在此处。
+ [Azure Application Insights](/azure-monitor/)：存储有关模型的监视信息。
+ [Azure Key Vault](/key-vault/)：存储计算目标使用的机密和工作区所需的其他敏感信息。

> [!NOTE]
> 除创建新版本以外，还可以使用现有的 Azure 服务。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 机器学习，请参阅：

+ [Azure 机器学习概述](overview-what-is-azure-ml.md)
+ [创建工作区](how-to-manage-workspace.md)
+ [管理工作区](how-to-manage-workspace.md)
+ [教程：开始使用 Python SDK 创建第一个 ML 试验](tutorial-1st-experiment-sdk-setup.md)
+ [教程：通过 R SDK 开始使用 Azure 机器学习](tutorial-1st-r-experiment.md)
+ [教程：使用自动化机器学习创建第一个分类模型](tutorial-first-experiment-automated-ml.md)（仅在[企业版](overview-what-is-azure-ml.md#sku)工作区可用）
+ [教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)（仅在[企业版](overview-what-is-azure-ml.md#sku)工作区可用）
