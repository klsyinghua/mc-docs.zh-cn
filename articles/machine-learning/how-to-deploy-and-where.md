---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 了解部署 Azure 机器学习模型（包括 Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列）的方式和位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/17/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4e35878927712b99db325806953f8c011f1f68a1
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599403"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure 机器学习部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何将机器学习模型作为 Web 服务部署在 Azure 云或 Azure IoT Edge 设备中。

无论在哪里[部署模型](#target)，工作流都是类似的：

1. 注册模型。
1. 准备部署。 （指定资产、使用情况、计算目标。）
1. 将模型部署到计算目标。
1. 测试已部署的模型（也称为“Web 服务”）。

有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习管理、部署和监视模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 模型。 如果没有已训练的模型，则可以使用](https://aka.ms/azml-deploy-cloud)此教程[中提供的模型和依赖项文件。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-vscode-tools.md)。

## <a name="connect-to-your-workspace"></a>连接到工作区

下面的代码演示如何使用缓存到本地开发环境中的信息连接到 Azure 机器学习工作区：

+ **使用 SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  若要详细了解如何使用 SDK 连接到工作区，请参阅[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 文档。

+ **使用 CLI**

   使用 CLI 时，请使用 `-w` 或 `--workspace-name` 参数指定命令的工作区。

+ **使用 VS Code**

   使用 VS Code 时，可以使用图形界面选择工作区。 有关详细信息，请参阅 VS Code 扩展文档中的[部署和管理模型](how-to-vscode-tools.md#deploy-and-manage-models)。

## <a id="registermodel"></a> 注册模型

已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如，如果有一个存储在多个文件中的模型，则可以在工作区中将这些文件注册为单个模型。 注册这些文件后，可以下载或部署已注册的模型，并接收注册的所有文件。

> [!TIP]
> 注册模型时，请提供云位置（来自训练运行）或本地目录的路径。 此路径仅用于在注册过程中查找要上传的文件。 它不需要与入口脚本中使用的路径匹配。 有关详细信息，请参阅[在入口脚本中查找模型文件](#locate-model-files-in-your-entry-script)。

机器学习模型会注册到 Azure 机器学习工作区中。 模型可以来自 Azure 机器学习或其他位置。 以下示例演示如何注册模型。

### <a name="register-a-model-from-an-experiment-run"></a>在实验运行中注册模型

本节中的代码片段演示如何在训练运行中注册模型：

> [!IMPORTANT]
> 若要使用这些代码段，需要先执行一个训练运行，并且需要有权访问 `Run` 对象（SDK 示例）或运行 ID 值（CLI 示例）。 若要详细了解如何训练模型，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

+ **使用 SDK**

  使用 SDK 训练模型时，可以接收 [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) 对象或 [AutoMLRun](https://docs.microsoft.com/azure/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 对象，具体取决于模型的训练方式。 每个对象都可用于注册通过实验运行创建的模型。

  + 通过 `azureml.core.Run` 对象注册模型：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。 有关详细信息，请参阅 [Run.register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) 文档。

  + 通过 `azureml.train.automl.run.AutoMLRun` 对象注册模型：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    在此示例中，未指定 `metric` 和 `iteration` 参数，因此将注册具有最佳主要指标的迭代。 不会使用模型名称，而是使用从运行返回的 `model_id` 值。

    有关详细信息，请参阅 [AutoMLRun.register_model]/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 文档。

+ **使用 CLI**

  ```azurecli
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `--asset-path` 设置为包含文件的文件夹的路径。

+ **使用 VS Code**

  通过使用 [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) 扩展，可使用任何模型文件或文件夹注册模型。

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

可以通过提供模型的本地路径来注册模型。 可以提供文件夹或单个文件的路径。 可以使用此方法来注册使用 Azure 机器学习训练并下载的模型。 也可以使用此方法来注册在 Azure 机器学习之外训练的模型。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **使用 SDK 和 ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。

+ **使用 CLI**

  ```azurecli
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  若要在模型注册中包含多个文件，请将 `-p` 设置为包含文件的文件夹的路径。

**时间估计**：大约 10 秒。

有关详细信息，请参阅关于[模型类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的文档。

若要详细了解如何使用在 Azure 机器学习之外训练的模型，请参阅[如何部署现有模型](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>选择计算目标

可以使用以下计算目标（或计算资源）来托管 Web 服务部署：

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>准备部署

要部署模型，需备齐以下项：

* **入口脚本**。 此脚本接受请求、使用模型为请求评分并返回结果。

    > [!IMPORTANT]
    > * 入口脚本特定于你的模型。 它必须能够识别传入请求数据的格式、模型所需数据的格式以及返回给客户端的数据的格式。
    >
    >   如果请求数据的格式对模型不可用，则该脚本可以将其转换为可接受的格式。 在将响应返回给客户端之前，它还可以对响应进行转换。
    >
    > * Web 服务或 IoT Edge 部署不会通过 Azure 机器学习 SDK 访问数据存储或数据集。 如果部署的模型需要访问存储在部署外的数据，例如 Azure 存储帐户中的数据，则必须使用相关的 SDK 开发自定义代码解决方案。 例如，[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。
    >
    >   可能适用于该方案的另一种方法是[批量预测](how-to-run-batch-predictions.md)，它在评分期间提供对数据存储的访问。

* 依赖项，如运行入口脚本或模型所需的帮助程序脚本或 Python/Conda 包  。

* 托管已部署模型的计算目标的部署配置  。 此配置描述运行模型所需的内存和 CPU 要求等因素。

这些项封装到推理配置和部署配置中   。 推理配置引用入口脚本和其他依赖项。 使用 SDK 执行部署时，以编程方式定义这些配置。 使用 CLI 时，可在 JSON 文件中定义它们。

### <a id="script"></a> 1.定义入口脚本和依赖项

入口脚本接收提交到已部署 Web 服务的数据，并将此数据传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 该脚本特定于你的模型  。 它必须能够识别模型需要和返回的数据。

该脚本包含两个用于加载和运行模型的函数：

* `init()`：此函数通常将模型载入全局对象。 此函数只能在 Web 服务的 Docker 容器启动时运行一次。

* `run(input_data)`：此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化。 也可以处理原始二进制数据。 可以先转换数据，然后再将数据发送到模型或返回给客户端。

#### <a name="locate-model-files-in-your-entry-script"></a>在入口脚本中查找模型文件

可以通过两种方法在入口脚本中查找模型：
* `AZUREML_MODEL_DIR`：一个包含模型位置路径的环境变量。
* `Model.get_model_path`：一个 API，使用注册的模型名称返回指向模型文件的路径。

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 是在服务部署过程中创建的环境变量。 可以使用此环境变量来查找部署的模型的位置。

下表描述了 AZUREML_MODEL_DIR 的值，它的值取决于部署的模型数：

| 部署 | 环境变量值 |
| ----- | ----- |
| 单个模型 | 包含模型的文件夹的路径。 |
| 多个模型 | 包含所有模型的文件夹的路径。 各个模型按名称和版本放置在此文件夹中 (`$MODEL_NAME/$VERSION`) |

若要获取某个模型中文件的路径，请将环境变量与要查找的文件名组合在一起。
在注册和部署过程中会保留模型文件的文件名。 

**单个模型示例**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**多个模型示例**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

注册模型时，请提供用于在注册表中管理该模型的模型名称。 将此名称与 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 方法结合使用，以检索本地文件系统上一个或多个模型文件的路径。 如果注册文件夹或文件集合，此 API 会返回包含这些文件的目录的路径。

注册模型时，请为其指定一个名称。 该名称对应于模型的放置位置（本地位置或在服务部署过程中指定的位置）。

> [!IMPORTANT]
> 如果使用自动化机器学习来训练模型，则会将 `model_id` 值用作模型名称。 若要通过示例了解如何注册和部署使用自动化机器学习训练的模型，请参阅 GitHub 上的 [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)。

下面的示例将返回名为 `sklearn_mnist_model.pkl` 的单个文件的路径（该文件使用名称 `sklearn_mnist` 注册）：

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>（可选）自动生成架构

若要为 Web 服务自动生成架构，请在一个已定义的类型对象的构造函数中提供输入和/或输出的示例。 该类型和示例用于自动创建架构。 Azure 机器学习随后会在部署过程中创建 Web 服务的 [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 规范。

当前支持以下类型：

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python 对象

若要使用架构生成，请在 Conda 环境文件中包含 `inference-schema` 包。 若要详细了解此包，请参阅 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)。

##### <a name="example-dependencies-file"></a>依赖项文件示例

以下 YAML 是用于推理的 Conda 依赖项文件的一个示例。 请注意，必须将版本为 1.0.45 或更高版本的 azureml-defaults 指示为 pip 依赖项，因为它包含将模型托管为 Web 服务所需的功能。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 如果你的依赖项可通过 Conda 和 pip（通过 PyPi）使用，Microsoft 建议使用 Conda 版本，因为 Conda 包通常附带预生成的二进制文件，能让安装更可靠。
>
> 有关详细信息，请参阅[了解 Conda 和 Pip](https://www.anaconda.com/understanding-conda-and-pip/)。
>
> 若要检查是否能通过 Conda 使用依赖项，请使用 `conda search <package-name>` 命令，或使用 [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) 和 [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) 处的包索引。

如果要使用自动生成架构功能，则入口脚本必须导入 `inference-schema` 包。

定义 `input_sample` 和 `output_sample` 变量中的输入和输出示例格式，它们表示 Web 服务的请求和响应格式。 在 `run()` 函数的输入和输出函数修饰器中使用这些示例。 以下 scikit-learn 示例使用架构生成功能。

##### <a name="example-entry-script"></a>入口脚本示例

以下示例演示如何接受和返回 JSON 数据：

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

以下示例演示如何使用数据帧将输入数据定义为 `<key: value>` 字典。 此方法支持使用 Power BI 中已部署的 Web 服务。 （[详细了解如何使用 Power BI 中的 Web 服务](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。）

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

若要查看更多示例，请参阅以下脚本：

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>二进制数据

如果模型接受二进制数据（如映像），则必须修改用于部署的 `score.py` 文件以接受原始 HTTP 请求。 若要接受原始数据，请在入口脚本中使用 `AMLRequest` 类，并向 `run()` 函数添加 `@rawhttp` 修饰器。

下面是接受二进制数据的 `score.py` 的示例：

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享 (CORS)

通过跨源资源共享 (CORS) 可以从其他域请求网页上的资源。 CORS 通过 HTTP 标头工作，这些标头通过客户端请求发送并随服务响应返回。 若要详细了解 CORS 和有效标头，请参阅维基百科上的[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

若要配置模型部署以支持 CORS，请在入口脚本中使用 `AMLResponse` 类。 使用此类，可设置响应对象的标头。

以下示例在入口脚本中设置响应的 `Access-Control-Allow-Origin` 标头：

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2.定义 InferenceConfig

推理配置描述如何配置模型以便进行预测。 此配置不属于入口脚本。 它引用入口脚本，并用于查找部署所需的所有资源。 此配置稍后在部署模型时使用。

推理配置使用 Azure 机器学习环境来定义部署所需的软件依赖项。 利用环境，你可以创建、管理和重复使用训练和部署所需的软件依赖项。 下面的示例演示如何从工作区加载环境，并将其与推理配置结合使用：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig

myenv = Environment.get(workspace=ws, name="myenv", version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=myenv)
```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

你也可以在不使用环境的情况下直接指定依赖项。 下面的示例演示如何创建从 Conda 文件加载软件依赖项的推理配置：

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息，请参阅 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 类文档。

若要详细了解如何将自定义 Docker 映像与推理配置结合使用，请参阅[如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 示例

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

以下命令演示如何使用 CLI 部署模型：

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

此实例中的配置指定以下设置：

* 该模型需要使用 Python。
* 入口脚本，用于处理发送到部署的服务的 Web 请求[](#script)。
* 用于描述推理所需的 Python 包的 Conda 文件。

若要详细了解如何将自定义 Docker 映像与推理配置结合使用，请参阅[如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定义部署配置

在部署模型之前，必须定义部署配置。 部署配置特定于将托管 Web 服务的计算目标  。 例如，在本地部署模型时，必须指定服务接受请求的端口。 该部署配置不属于入口脚本。 它用于定义将托管模型和入口脚本的计算目标的特征。

例如，如果没有与工作区关联的 Azure Kubernetes 服务 (AKS) 实例，则可能还需要创建计算资源。

下表提供了为每个计算目标创建部署配置的示例：

| 计算目标 | 部署配置示例 |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器实例 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes 服务 | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

可从 `azureml.core.webservice` 导入的本地、Azure 容器实例和 AKS Web 服务的类：

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>分析

在将模型部署为服务之前，可能需要对其进行分析，以确定最佳的 CPU 和内存要求。 可以使用 SDK 或 CLI 来分析模型。 以下示例演示如何使用 SDK 分析模型。

> [!IMPORTANT]
> 在进行分析时，你提供的推理配置无法引用 Azure 机器学习环境。 而应改为使用 `InferenceConfig` 对象的 `conda_file` 参数定义软件依赖项。

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

此代码显示类似于以下输出的结果：

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

模型分析结果将作为 `Run` 对象发出。

若要详细了解如何在 CLI 中使用分析功能，请参阅 [az ml 模型分析](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

有关详细信息，请参阅以下文档：

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profile()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [推理配置文件架构](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>部署到目标

部署使用推理配置部署配置来部署模型。 不管计算目标如何，部署过程都是类似的。 部署到 AKS 的过程略有不同，因为必须提供对 AKS 群集的引用。

### <a name="securing-deployments-with-ssl"></a>使用 SSL 保护部署

若要详细了解如何保护 Web 服务部署，请参阅[使用 SSL 保护 Web 服务](how-to-secure-web-service.md#enable)。

### <a id="local"></a> 本地部署

若要在本地部署模型，需要在本地计算机上安装 Docker。

#### <a name="using-the-sdk"></a>使用 SDK
zzs
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关详细信息，请参阅关于 [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 的文档。

#### <a name="using-the-cli"></a>使用 CLI

要使用 CLI 部署模型，请使用以下命令。 将 `mymodel:1` 替换为注册的模型的名称和版本：

```azurecli
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

有关详细信息，请参阅 [az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)文档。

### <a id="notebookvm"></a> 计算实例 Web 服务（开发/测试）

请参阅[将模型部署到 Azure 机器学习计算实例](how-to-deploy-local-container-notebook-vm.md)。

### <a id="aci"></a> Azure 容器实例（开发/测试）

请参阅[部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)。

### <a id="aks"></a> Azure Kubernetes 服务（开发/测试和生产）

请参阅[部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)。

## <a name="consume-web-services"></a>使用 Web 服务

每个部署的 Web 服务都提供有一个 REST 终结点，因此可以使用任何编程语言创建客户端应用程序。
如果已为服务启用基于密钥的身份验证，则需要提供服务密钥，将其作为请求标头中的令牌。
如果已为服务启用基于令牌的身份验证，则需要提供 Azure 机器学习 JSON Web 令牌 (JWT)，将其作为请求标头中的持有者令牌。 

主要区别在于，密钥是静态的且能手动重新生成，而令牌需要在到期时刷新   。 Azure 容器实例和 Azure Kubernetes 服务部署的 Web 服务支持基于密钥的身份验证，而基于令牌的身份验证仅能用于 Azure Kubernetes 服务部署  。 请参阅身份验证[操作说明](how-to-setup-authentication.md#web-service-authentication)，了解更多信息和特定代码示例。

> [!TIP]
> 部署服务后，可以检索架构 JSON 文档。 使用部署的 Web 服务中的 [swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)（例如 `service.swagger_uri`）获取本地 Web 服务的 Swagger 文件的 URI。

### <a name="request-response-consumption"></a>使用“请求-响应”

下面是如何在 Python 中调用服务的示例：
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

有关详细信息，请参阅[创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)。

### <a name="web-service-schema-openapi-specification"></a>Web 服务架构（OpenAPI 规范）

如果在部署中使用了自动生成架构，则可以通过使用 [swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)获取服务的 OpenAPI 规范的地址。 （例如 `print(service.swagger_uri)`。）使用 GET 请求，或在浏览器中打开 URI 以检索规范。

以下 JSON 文档是为部署生成的架构（OpenAPI 规范）示例：

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

有关详细信息，请参阅 [OpenAPI 规范](https://swagger.io/specification/)。

若要了解可根据规范创建客户端库的实用工具，请参阅 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)。

### <a id="azuremlcompute"></a> 批量推理
Azure 机器学习计算目标由 Azure 机器学习创建和管理。 它们可用于 Azure 机器学习管道中的批量预测。

若要查看使用 Azure 机器学习计算进行批量推理的演练，请参阅[如何运行批量预测](tutorial-pipeline-batch-scoring-classification.md)。

### <a id="iotedge"></a> IoT Edge 推理
对部署到边缘的支持处于预览阶段。 有关详细信息，请参阅[将 Azure 机器学习部署为 IoT Edge 模块](/iot-edge/tutorial-deploy-machine-learning)。


## <a id="update"></a> 更新 Web 服务

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>持续部署模型

可以通过使用 [Azure DevOps](https://azure.microsoft.com/services/devops/) 的机器学习扩展来持续部署模型。 如果在 Azure 机器学习工作区中注册了新的机器学习模型，则可以使用 Azure DevOps 的机器学习扩展来触发部署管道。

1. 注册 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)，它能将应用程序持续集成和交付到任何平台或云。 （请注意，Azure Pipelines 不同于[机器学习管道](concept-ml-pipelines.md#compare)。）

1. [创建 Azure DevOps 项目。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安装 [Azure Pipelines 的机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)。

1. 使用服务连接设置与 Azure 机器学习工作区的服务主体连接，以便访问你的项目。 转到项目设置，选择“服务连接”，然后选择“Azure 资源管理器”   ：

    [![选择 Azure 资源管理器](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. 在“范围级别”列表中选择“AzureMLWorkspace”，然后输入其余值   ：

    ![选择 AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. 若要使用 Azure Pipelines 持续部署机器学习模型，请在管道下选择“发布”  。 添加新项目，然后选择之前创建的“AzureML 模型”项目以及服务连接  。 选择模型和版本以触发部署：

    [![选择 AzureML 模型](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 对模型项目启用模型触发器。 如果开启触发器，则每次在工作区中注册该模型的指定版本（即最新版本）时，都将触发 Azure DevOps 发布管道。

    [![启用模型触发器](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

若要查看更多示例项目和示例，请参阅 GitHub 中的以下示例存储库：

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>下载模型
如果要下载模型以便在自己的执行环境中使用它，可以使用以下 SDK/CLI 命令：

SDK：
```python
model_path = Model(ws,'mymodel').download()
```

CLI：
```azurecli
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>（预览）无代码模型部署

无代码模型部署目前处于预览阶段，支持以下机器学习框架：

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel 格式
需要以 SavedModel 格式注册 Tensorflow 模型，才能进行无代码模型部署  。

请访问[此链接](https://www.tensorflow.org/guide/saved_model)以了解如何创建 SavedModel。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX 模型

任何 ONNX 推理图都支持 ONNX 模型注册和部署。 当前不支持预处理和后续处理步骤。

下面是有关如何注册和部署 MNIST ONNX 模型的示例：

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit-learn 模型

所有内置 scikit-learn 模型类型都支持无代码模型部署。

下面的示例演示如何在不使用额外代码的情况下注册和部署 sklearn 模型：

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

注意：这些依赖项包含在预生成的 sklearn 推理容器中：

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>包模型

在某些情况下，可能需要在不部署模型的情况下创建 Docker 映像（例如要[部署到 Azure 应用服务](how-to-deploy-app-service.md)）。 或者，你可能希望下载映像并在本地 Docker 安装上运行它。 甚至可能需要下载用于生成映像的文件、对其进行检查和修改并手动生成映像。

这些工作都可以通过打包模型来完成。 此方法能对将模型作为 Web 服务托管所需的全部资产进行打包，让你能下载完整生成的 Docker 映像或生成该映像所需的文件。 可以通过两种方式使用模型打包：

**下载已打包的模型：** 下载包含模型以及将其作为 Web 服务托管所需的其他文件的 Docker 映像。

**生成 Dockerfile：** 下载生成 Docker 映像所需的 Dockerfile、模型、入口脚本和其他资产。 然后可以先检查这些文件或进行修改，再在本地生成映像。

这两个包都可用于获取本地 Docker 映像。

> [!TIP]
> 创建包类似于部署模型。 使用注册的模型和推理配置。

> [!IMPORTANT]
> 若要下载完整生成的映像或要在本地生成映像，需要在开发环境中安装 [Docker](https://www.docker.com)。

### <a name="download-a-packaged-model"></a>下载已打包的模型

下面的示例生成一个映像，该映像已在工作区的 Azure 容器注册表中注册：

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

创建包后，可以使用 `package.pull()` 将映像拉取到本地 Docker 环境。 此命令的输出将显示映像的名称。 例如： 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`。 

下载模型后，使用 `docker images` 命令列出本地映像：

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

若要启动基于此映像的本地容器，请使用以下命令从 Shell 或命令行启动已命名容器。 使用 `docker images` 命令返回的映像 ID 替换 `<imageid>` 值。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

此命令启动名为 `myimage` 的映像的最新版本。 它将本地端口 6789 映射到 Web 服务正在侦听的容器中的端口 (5001)。 还将名称 `mycontainer` 分配给容器，使容器更易于停止。 启动容器后，可以将请求提交到 `http://localhost:6789/score`。

### <a name="generate-a-dockerfile-and-dependencies"></a>生成 Dockerfile 和依赖项

下面的示例演示如何下载在本地生成映像所需的 Dockerfile、模型和其他资产。 `generate_dockerfile=True` 参数表示需要的是文件，而不是完整生成的映像。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

此代码将生成映像所需的文件下载到 `imagefiles` 目录。 已保存的文件中包含的 Dockerfile 引用存储在 Azure 容器注册表中的基础映像。 在本地 Docker 安装上生成映像时，需要使用地址、用户名和密码完成注册表身份验证。 采取以下步骤，通过本地 Docker 安装生成映像：

1. 在 Shell 或命令行会话中使用以下命令，使用 Azure 容器注册表对 Docker 进行身份验证。 将 `<address>`、`<username>` 和 `<password>` 替换为 `package.get_container_registry()` 检索到的值。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 若要生成映像，请使用以下命令。 将 `<imagefiles>` 替换为 `package.save()` 保存文件的目录的路径。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    此命令将映像名设置为 `myimage`。

若要验证是否已生成映像，请使用 `docker images` 命令。 在列表中应该能看到 `myimage` 映像：

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

若要启动基于此映像的新容器，请使用以下命令：

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

此命令启动名为 `myimage` 的映像的最新版本。 它将本地端口 6789 映射到 Web 服务正在侦听的容器中的端口 (5001)。 还将名称 `mycontainer` 分配给容器，使容器更易于停止。 启动容器后，可以将请求提交到 `http://localhost:6789/score`。

### <a name="example-client-to-test-the-local-container"></a>测试本地容器的示例客户端

以下代码是可与容器结合使用的 Python 客户端示例：

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

若要通过示例了解采用其他编程语言的客户端，请参阅[使用部署为 Web 服务的模型](how-to-consume-web-service.md)。

### <a name="stop-the-docker-container"></a>停止 Docker 容器

若要停止容器，请在不同的 Shell 或命令行中使用以下命令：

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>清理资源

若要删除已部署的 Web 服务，请使用 `service.delete()`。
若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息，请参阅关于 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) 和 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) 的文档。

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [排除部署故障](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
