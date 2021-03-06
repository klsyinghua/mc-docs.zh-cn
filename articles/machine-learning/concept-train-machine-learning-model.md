---
title: 模型训练方法
titleSuffix: Azure Machine Learning
description: 了解可用于通过 Azure 机器学习训练模型的不同方法。 评估器提供了一种简单的方法来使用常用框架，如 Scikit-learn、TensorFlow、Keras、PyTorch 和 Chainer。 通过机器学习管道，你可以轻松地计划无人参与的运行，使用异类计算环境，以及重复使用工作流的某些部分。 运行配置提供对训练进程运行于的计算目标的精细控制。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 92809e5362df7dcc7971bf62c1c4253c12fef19b
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599057"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure 机器学习训练模型

Azure 机器学习提供了多种用于训练模型的方法，其中包括使用 SDK 的代码优先解决方案以及低代码解决方案（例如自动化机器学习和可视化设计器）。 使用以下列表确定适合你的训练方法：

+ [适用于 Python 的 Azure 机器学习 SDK](#python-sdk)：Python SDK 提供了多种用于训练模型的方法，每一种方法都具有不同的功能。

    | 训练方法 | 说明 |
    | ----- | ----- |
    | [运行配置](#run-configuration) | 用于训练模型的常规方法是使用训练脚本并运行配置  。 运行配置提供了配置用于训练模型的训练环境所需的信息。 你可以执行运行配置、训练脚本和计算目标（训练环境）以及运行训练作业。 |
    | [自动化机器学习](#automated-machine-learning) | 通过自动化机器学习，无需具备大量的数据科学知识或编程知识即可训练模型  。 对于拥有数据科学和编程背景的人员，它提供了一种方法，可通过自动化算法选择和超参数优化来节省时间和资源。 使用自动化机器学习时，无需考虑定义运行配置。 |
    | [评估器](#estimators) | 利用评估器类，可以基于常用机器学习框架轻松地训练模型  。 有适用于 Scikit-learn、PyTorch、TensorFlow 和 Chainer 的评估器类     。 还有一个常规评估器，可用于尚无专用评估器类的框架。 使用评估器时，你无需考虑定义运行配置。 |
    | [机器学习管道](#machine-learning-pipeline) | 管道不是一种特殊的训练方法，而是一种使用模块化可重用步骤来定义工作流的方法，它可以将训练包含在工作流中** **。 机器学习管道支持通过使用自动化机器学习、评估器和运行配置来训练模型。 由于管道并非专门用于训练，因此使用管道而不使用其他训练方法的原因更多样化。 通常，有以下情况时可以使用管道：<br>* 需要计划无人参与的过程，例如长时间运行的训练作业或数据准备  。<br>* 使用跨异类计算资源和存储位置协调的多个步骤  。<br>* 将管道用作针对特定方案的可重用模板，如重新训练或批量评分  。<br>* 跟踪工作流的数据源、输入和输出并对其进行版本控制  。<br>* 工作流由单独执行特定步骤的不同团队实现  。 然后，可以在管道中将步骤联接在一起以实现工作流。 |

+ **设计器**：Azure 机器学习设计器（预览版）为机器学习提供了一个简单的入口点，用于构建概念证明或提供给缺乏编码经验的用户。 借助设计器，可以使用基于 Web 的拖放式 UI 来训练模型。 可以在设计过程中使用 Python 代码，也可以在不编写任何代码的情况下训练模型。

+ **CLI**：机器学习 CLI 提供用于执行 Azure 机器学习中常见任务的命令，常用于编写任务脚本和自动化任务** **。 例如，创建训练脚本或管道后，即可使用 CLI 按计划开始训练，或者在更新用于训练的数据文件后开始训练。 对于训练模型，它提供了用于提交训练作业的命令。 它可以使用运行配置或管道提交作业。

这些训练方法中的每一种都可以使用不同类型的计算资源进行训练。 这些资源统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。

## <a name="python-sdk"></a>Python SDK

借助适用于 Python 的 Azure 机器学习 SDK，你可以使用 Azure 机器学习构建和运行机器学习工作流。 可以通过交互式 Python 会话、Jupyter Notebook、Visual Studio Code 或其他 IDE 与服务进行交互。

* [什么是适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安装/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [配置 Azure 机器学习的开发环境](how-to-configure-environment.md)

### <a name="run-configuration"></a>运行配置

可以使用 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 定义 Azure 机器学习的常规训练作业。 然后使用运行配置和训练脚本，针对计算目标训练模型。

可以从本地计算机的运行配置开始，根据需要切换到基于云的计算目标的运行配置。 更改计算目标时，仅更改你使用的运行配置。 运行还会记录有关训练作业的信息，例如输入、输出和日志。

* [什么是运行配置？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教程：训练第一个 ML 模型](tutorial-1st-experiment-sdk-train.md)
* [示例：Jupyter Notebook 训练模型示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [如何：设置并使用模型训练的计算目标](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自动化机器学习

定义迭代、超参数设置、特征化和其他设置。 在训练过程中，Azure 机器学习并行尝试不同的算法和参数。 训练达到定义的退出条件后即停止。 使用评估器时，你无需考虑定义运行配置。

> [!TIP]
> 除了 Python SDK，还可以通过 [Azure 机器学习工作室](https://ml.azure.com)使用自动化 ML。

* [什么是自动化机器学习？](concept-automated-ml.md)
* [教程：使用自动化机器学习创建第一个分类模型](tutorial-first-experiment-automated-ml.md)
* [教程：使用自动化机器学习预测出租车费](tutorial-auto-train-models.md)
* [示例：Jupyter Notebook 自动化机器学习示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [如何：用 Python 配置自动化 ML 试验](how-to-configure-auto-train.md)
* [如何：自动训练时序预测模型](how-to-auto-train-forecast.md)
* [如何：使用 [Azure 机器学习工作室](how-to-create-portal-experiments.md)创建、探索和部署自动化机器学习试验

### <a name="estimators"></a>评估器

借助评估器，你可以轻松地使用常用 ML 框架训练模型。 如果使用 Scikit-learn、PyTorch、TensorFlow 或 Chainer，应考虑使将评估器用于训练     。 还有一个常规评估器，可用于尚无专用评估器类的框架。 使用评估器时，你无需考虑定义运行配置。

* [什么是评估器？](concept-azure-machine-learning-architecture.md#estimators)
* [教程：使用 Azure 机器学习通过 MNIST 数据和 scikit-learn 训练映像分类模型](tutorial-train-models-with-aml.md)
* [示例：使用评估器的 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [如何：在训练期间创建评估器](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>机器学习管道

机器学习管道可以使用前面提到的训练方法（运行配置、评估器和自动化机器学习）。 管道不仅可创建工作流，因而不仅可用于模型训练。 在管道中，你可以使用自动化机器学习、评估器或运行配置来训练模型。

* [什么是 Azure 机器学习中的 ML 管道？](concept-ml-pipelines.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
* [教程：使用 Azure 机器学习管道进行批量评分](tutorial-pipeline-batch-scoring-classification.md)
* [示例：Jupyter Notebook 机器学习管道示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [示例：使用自动化机器学习的管道](https://aka.ms/pl-automl)
* [示例：使用评估器的管道](https://aka.ms/pl-estimator)

## <a name="azure-machine-learning-designer"></a>Azure 机器学习设计器

通过设计器，可以在 Web 浏览器中使用拖放式界面训练模型。

+ [什么是设计器？](concept-designer.md)
+ [教程：预测汽车价格](tutorial-designer-automobile-price-train-score.md)
+ [回归：预测价格](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分类：预测收入](how-to-designer-sample-classification-predict-income.md)
+ [分类：预测客户流失、购买欲和追加销售](how-to-designer-sample-classification-churn.md)
+ [使用自定义 R 脚本进行分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
+ [文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

机器学习 CLI 是 Azure CLI 的扩展。 它提供了很多可用于 Azure 机器学习的跨平台 CLI 命令。 通常，使用 CLI 自动执行任务，如训练机器学习模型。

* [使用 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>后续步骤

了解如何[设置训练环境](how-to-set-up-training-targets.md)。
