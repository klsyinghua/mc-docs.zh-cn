---
title: Swagger 文档 - 语音服务
titleSuffix: Azure Cognitive Services
description: Swagger 文档可用于自动生成适用于多种编程语言的 SDK。 Swagger 支持服务中的所有操作
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
origin.date: 07/05/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: 30143a6e260285c995780bd60c25f30ca429d8b0
ms.sourcegitcommit: 94e1c9621b8f81a7078f1412b3a73281d0a8668b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76123342"
---
# <a name="swagger-documentation"></a>Swagger 文档

语音服务提供了一个 Swagger 规范，用于与少量 REST API 交互，这些 REST API 用于导入数据、创建模型、测试模型准确性、创建自定义终结点、排列批量听录以及管理订阅。 可使用这些 API 以编程方式完成通过自定义语音门户提供的大部分操作。

> [!NOTE]
> 支持将语音转文本和文本转语音操作作为 REST API，而后者记录在 Swagger 规范中。

## <a name="generating-code-from-the-swagger-specification"></a>从 Swagger 规范生成代码

[Swagger 规范](https://chinaeast2.cris.azure.cn/swagger/ui/index)包含可快速测试各种路径的选项。 但有时需要为所有路径生成代码，从而创建可基于未来的解决方案的单个调用库。 让我们看看生成 Python 库的过程。

你需要将 Swagger 设置为与语音服务订阅相同的区域。 可在 Azure 门户中的语音服务资源下确认区域。 有关受支持的区域的完整列表，请参阅[区域](regions.md)。

1. 转到 https://editor.swagger.io
2. 单击“文件”，然后单击“导入”  
3. 输入 Swagger URL，包括语音服务订阅的区域 `https://<your-region>.cris.azure.cn/docs/v2.0/swagger`
4.  单击“生成客户端”，然后选择 Python
5. 保存客户端库

可以使用通过 [GitHub 上的语音服务示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)生成的 Python 库。

## <a name="reference-docs"></a>参考文档

* [REST (Swagger)：批量听录和自定义](https://chinaeast2.cris.azure.cn/swagger/ui/index)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)

## <a name="next-steps"></a>后续步骤

* [GitHub 上的语音服务示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。
* [获取语音服务订阅密钥以进行试用](get-started.md)
