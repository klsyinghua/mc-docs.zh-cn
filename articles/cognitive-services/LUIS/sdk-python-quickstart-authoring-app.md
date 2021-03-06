---
title: 快速入门：适用于 Python 的语言理解 (LUIS) 创作客户端库
titleSuffix: Azure Cognitive Services
description: 通过本快速入门开始使用用于 Python 的 LUIS 客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。
services: cognitive-services
author: lingliw
manager: digimobile
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
origin.date: 11/22/2019
ms.date: 12/04/2019
ms.author: v-lingwu
ms.openlocfilehash: 0531066ad000574587a675992dc9f39406767ecf
ms.sourcegitcommit: e0b57f74aeb9022ccd16dc6836e0db2f40a7de39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75854225"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>快速入门：适用于 Python 的语言理解 (LUIS) 创作客户端库

适用于 Python 的语言理解 (LUIS) 创作客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用语言理解 (LUIS) 可在用户的自然语言对话文本中应用自定义的机器学习智能，以预测整体含义并提炼出相关的详细信息。 

可以使用适用于 Python 的语言理解 (LUIS) 创作客户端库执行以下操作：

* 创建应用。
* 添加意向、实体和示例话语。
* 添加短语列表等特征。
* 训练和发布应用。

[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [创作包 (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>必备条件

* 语言理解 (LUIS) 门户帐户：[免费创建一个](https://luis.azure.cn)。
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="get-your-language-understanding-luis-starter-key"></a>获取语言理解 (LUIS) 初学者密钥

通过创建 LUIS 创作资源，获取[初学者密钥](luis-how-to-azure-subscription.md#starter-key)。 保留密钥和密钥区域，以便用于下一步。

### <a name="create-an-environment-variable"></a>创建环境变量

使用密钥和密钥区域，创建两个用于身份验证的环境变量：

* `LUIS_AUTHORING_KEY` - 用于验证请求的资源密钥。
* `LUIS_REGION` - 与密钥关联的区域。 例如，`chinaeast`。

使用操作系统的说明。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

添加环境变量后，请重启控制台窗口。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key> 
export LUIS_REGION=<replace-with-your-luis-region>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
***

### <a name="install-the-python-library-for-luis"></a>安装适用于 LUIS 的 Python 库

在应用程序目录中，使用以下命令安装适用于 Python 的语言理解 (LUIS) 创作客户端库：

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 对象，其中包含创作密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [创建](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)、[删除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-)、[发布](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* 示例言语 - [按批添加](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)、[按 ID 删除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* 特征 - 管理[短语列表](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* 模型 - 管理[意向](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* 训练 - [训练](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-)应用和轮询[训练状态](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [版本](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - 使用克隆、导出和删除操作进行管理


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的语言理解 (LUIS) 创作客户端库来执行以下操作：

* [创建应用](#create-a-luis-app)
* [添加实体](#create-entities-for-the-app)
* [添加意向](#create-intent-for-the-app)
* [添加示例表述](#add-example-utterance-to-intent)
* [训练应用](#train-the-app)
* [发布应用](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建新的 Python 应用程序。 然后导入以下库。 

```
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
```

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

```
key_var_name = 'LUIS_AUTHORING_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
authoring_key = os.environ[key_var_name]

region_var_name = 'LUIS_REGION'
if not region_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(region_var_name))
region = os.environ[region_var_name]
endpoint = "https://{}.api.cognitive.azure.cn".format(region)
```

## <a name="authenticate-the-client"></a>验证客户端

使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 对象。

```
# Instantiate a LUIS client
client = LUISAuthoringClient(endpoint, CognitiveServicesCredentials(authoring_key))
```

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 创建一个 LUIS 应用，用于包含保存意向、实体和示例言语的自然语言处理 (NLP) 模型。 

1. 创建 [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) 对象的 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) 方法，用于创建应用。 名称和语言区域性是必需的属性。 

```
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
```


## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。  用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。  意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。   

将 [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) 方法与唯一意向的名称配合使用，然后传递应用 ID、版本 ID 和新的意向名称。 

```
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
```

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[geographyV2](luis-reference-prebuilt-geographyv2.md) 和 [ordinal](luis-reference-prebuilt-ordinal.md)。 

此 **add_entities** 方法创建一个包含两个角色的 `Location` 简单实体、一个 `Class` 简单实体和一个 `Flight` 复合实体，并添加多个预生成实体。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型。 

```
def add_entities(app_id, app_version):

    locationEntityId = client.model.add_entity(app_id, app_version, "Location")
    print("locationEntityId {} added.".format(locationEntityId)) 

    originRoleId = client.model.create_entity_role(app_id, app_version, locationEntityId, "Origin")
    print("originRoleId {} added.".format(originRoleId)) 

    destinationRoleId = client.model.create_entity_role(app_id, app_version, locationEntityId, "Destination")
    print("destinationRoleId {} added.".format(destinationRoleId)) 

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId)) 

    client.model.add_prebuilt(app_id, app_version, prebuilt_extractor_names=["number", "datetimeV2", "geographyV2", "ordinal"])

    compositeEntityId = client.model.add_composite_entity(app_id, app_version, name="Flight",
                                      children=["Location", "Class", "number", "datetimeV2", "geographyV2", "ordinal"])
    print("compositeEntityId {} added.".format(compositeEntityId))
```

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。 

通过创建 [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。 

结合应用 ID、版本 ID 和示例列表调用 [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)。 该调用将以结果列表做出响应。 需要检查每个示例的结果，以确保该示例已成功添加到模型中。 

```
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Location", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Location", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Location", "London"),
                            ("Location", "Seattle"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
```
    
## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 已训练模型可在[容器](luis-container-howto.md)中用于过渡槽或生产槽。 

[train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法需要应用 ID 和版本 ID。 

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法以确定训练何时成功或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

```
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
```

## <a name="publish-a-language-understanding-app"></a>发布语言理解应用

使用 [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

```
def publish_app(app_id, app_version):
    response = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + response.endpoint_url)
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>后续步骤

* [什么是语言理解 (LUIS) API？](what-is-luis.md)
* [新增功能](whats-new.md)
* [意向](luis-concept-intent.md)、[实体](luis-concept-entity-types.md)、[示例言语](luis-concept-utterance.md)和[预生成实体](luis-reference-prebuilt-entities.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py) 上找到此示例的源代码。
