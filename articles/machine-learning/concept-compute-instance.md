---
title: 什么是 Azure 机器学习计算实例？
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习计算实例 - 完全托管式基于云的工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 8bd31ad53f03eaeac4aa29ef7c1be1b632f5059d
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599099"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什么是 Azure 机器学习计算实例？

Azure 机器学习计算实例（预览版）是面向数据科学家的完全托管式基于云的工作站。 

计算实例可让客户轻松地开始进行 Azure 机器学习开发，并为 IT 管理员提供管理和企业就绪功能。  

可将计算实例用作云中的完全配置、完全托管的开发环境。

计算实例通常用作开发环境。  它们还可用作训练和推断的计算目标来实现开发和测试目的。  对于大型任务，具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选项。

> [!NOTE]
> 计算实例目前仅适用于区域为“美国中北部”或“英国南部”的工作区，即将支持其他区域。  
>如果你的工作区在任何其他区域，则可以继续创建并使用[笔记本 VM](concept-compute-instance.md#notebookvm)。 

## <a name="why-use-a-compute-instance"></a>为何使用计算实例？

计算实例是完全托管式基于云的工作站，已针对机器学习开发环境进行优化。 它提供以下优势：

|主要优点||
|----|----|
|工作效率|数据科学家可在其 Web 浏览器中使用集成的笔记本及以下工具来生成和部署模型：<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|无需自行管理且安全|减少安全保护工作，增强企业的安全要求合规性。 计算实例提供可靠的管理策略和安全网络配置，例如：<br/><br/>- 通过资源管理器模板或 Azure 机器学习 SDK 自动预配<br/>- [基于角色的访问控制 (RBAC)](/role-based-access-control/overview)<br/>- [虚拟网络支持](how-to-enable-virtual-network.md#compute-instance)<br/>- 用于启用/禁用 SSH 访问的 SSH 策略|
|预先经过配置或支持 ML|使用预配置的最新 ML 包、深度学习框架和 GPU 驱动程序完成设置任务，可节省时间。|
|完全可自定义|支持多种 Azure VM 类型，包括 GPU 和持久性低级自定义，例如，安装相应的包和驱动程序可以轻而易举地实现高级方案。 |

## <a name="contents"></a>工具和环境

使用 Azure 机器学习计算实例可以在工作区中的完全集成式笔记本体验中创作、训练和部署模型。


以下工具和环境安装在计算实例上： 

|常规工具和环境|详细信息|
|----|:----:|
|驱动程序|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 库||
|Azure CLI ||
|Azure 机器学习示例 ||
|Azure 机器学习 EDAT 引擎 ||            
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** 工具和环境|详细信息|
|----|:----:|
|RStudio Server Open Source Edition||
|R 内核||
|适用于 R 的 Azure 机器学习 SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 示例|

|**PYTHON** 工具和环境|详细信息|
|----|----|
|Anaconda Python||
|Jupyter 和扩展||
|Jupyterlab 和扩展||
|Visual Studio Code ||
[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>从 PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|其他 PyPI 包|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 包|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度学习包|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 包|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|                           
|Azure 机器学习 Python 和 R SDK 示例||

计算实例通常用作开发环境。  它们还可用作训练和推断的计算目标来实现开发和测试目的。  对于大型任务，具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选项。


## <a name="accessing-files"></a>访问文件

笔记本和 R 脚本存储在 Azure 文件共享中工作区的默认存储帐户内。  这些文件位于“用户文件”目录下。 通过此存储可以轻松地在计算实例之间共享笔记本。 停止或删除计算实例时，存储帐户还会安全保存笔记本。

工作区的 Azure 文件共享帐户作为驱动器装载到计算实例上。 此驱动器是 Jupyter、Jupyter Labs 和 RStudio 的默认工作目录。

可以从同一工作区中的所有计算实例访问文件共享中的文件。 对计算实例上的这些文件所做的任何更改将可靠地保存回到文件共享。

还可以将最新 Azure 机器学习示例克隆到工作区文件共享中“用户文件”目录下的文件夹内。

比写入到 VM 本身相比，在网络驱动器上写入小文件可能速度更慢。  若要写入许多小文件，请尝试直接在计算实例上使用某个目录，例如 `/tmp` 目录。 请注意，无法从工作区中的其他计算实例访问这些文件。

## <a name="managing-a-compute-instance"></a>管理计算实例

在 Azure 机器学习工作室中的工作区内选择“计算”，然后在顶部选择“计算实例”。  

![管理计算实例](./media/concept-compute-instance/manage-compute-instance.png)

可执行以下操作：

* 创建计算实例。 指定名称、Azure VM 类型（包括 GPU）、启用/禁用 SSH 访问，以及配置虚拟网络设置（可选）。 也可以直接从集成的笔记本、Azure 门户、资源管理器模板或 Azure 机器学习 SDK 创建实例。 创建计算实例时应用的“每个区域的专用核心数”配额是统一的，并与 Azure 机器学习计算群集配额共享。
* 刷新“计算实例”选项卡
* 启动、停止和重启计算实例
* 删除计算实例

对于工作区中的每个计算实例，可以：

* 访问该计算实例上的 Jupyter、JupyterLab、RStudio、VS Code URI
* 通过 SSH 连接到计算实例。 默认已禁用 SSH 访问，但可以在创建计算实例时启用。 SSH 访问是通过公钥/私钥机制实现的。 选项卡中将提供 IP 地址、用户名和端口号等 SSH 连接详细信息。
* 获取有关特定计算实例的详细信息，例如 IP 地址和区域。

使用 [RBAC](/role-based-access-control/overview) 可以控制工作区中的哪些用户可以创建、删除、启动、停止和重启计算实例。 充当工作区参与者和所有者角色的所有用户可以在整个工作区中创建、删除、启动、停止和重启计算实例。 但是，只有特定计算实例的创建者可在该计算实例上访问 Jupyter、JupyterLab 和 RStudio。 计算实例的创建者具有专用的计算实例，拥有 访问权限，且可通过 Jupyter 进入终端。 计算实例具有创建者用户的单用户登录名，所有操作将使用该用户的标识进行试验运行的 RBAC 控制和权限划分。 SSH 访问是通过公钥/私钥机制控制的。

也可以通过以下方式创建实例
* 直接从集成式笔记本体验
* 在 Azure 门户中配置
* 通过 Azure 资源管理器模板
* 使用 Azure 机器学习 SDK

创建计算实例时应用的“每个区域的专用核心数”配额是统一的，并与 Azure 机器学习训练群集配额共享。 

## <a name="compute-target"></a>计算目标

计算实例可用作类似于 Azure 机器学习计算训练群集的[训练计算目标](concept-compute-target.md#train)。 预配多 GPU VM 可通过 TensorFlow/PyTorch 估算器运行分布式训练作业。 还可以创建运行配置，并使用它在计算实例上运行试验。 可以使用计算实例作为测试/调试方案的本地推理部署目标。

## <a name="notebookvm"></a>Notebook VM 发生了什么情况？

计算实例即将取代 Notebook VM。  在计算实例尚不可用的区域，可以继续使用具有完整功能的 Notebook VM 和创建新的 Notebook VM。

可从计算实例访问存储在工作区文件共享中的任何笔记本文件，以及工作区数据存储中的数据。 但是，以前安装在 Notebook VM 上的任何自定义包都需要在计算实例上重新安装。 创建计算群集时适用的配额限制在创建计算实例时同样适用。 

在计算实例可用的区域，无法创建新的 Notebook VM。 不过，仍然可以访问和使用已创建的 Notebook VM 及其完整功能。 可以在现有 Notebook VM 所在的同一工作区中创建计算实例。 


## <a name="next-steps"></a>后续步骤

 * [教程：训练第一个 ML 模型](tutorial-1st-experiment-sdk-train.md)介绍了如何将计算实例与集成式笔记本配合使用。
