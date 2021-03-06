---
title: 语音转文本 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音转文本功能，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本输入。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 12/11/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: b5d90105ff9507d713cedbca3d9b729809098ac1
ms.sourcegitcommit: e99166b2db4b292a7044e5f26b3e3547fef22ee9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76269946"
---
# <a name="what-is-speech-to-text"></a>什么是语音转文本？

使用语音服务提供的语音转文本（也称为语音识别）功能，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本即命令输入。 此服务由 Microsoft 对 Cortana 和 Office 产品使用的同一识别技术提供支持。 它可与<a href="./speech-translation.md" target="_blank">翻译<span class="docon docon-navigate-external x-hidden-focus"></span></a>和<a href="./text-to-speech.md" target="_blank">文本转语音<span class="docon docon-navigate-external x-hidden-focus"></span></a>服务产品无缝地协同工作。 有关可用语音转文本语言的完整列表，请参阅[支持的语言](language-support.md#speech-to-text)。

语音转文本服务默认使用通用语言模型。 此模型已使用 Microsoft 自有的数据训练，部署在云中。 它非常适合用于对话和听写方案。 使用语音转文本在独特的环境中进行识别和听录时，可以创建并训练自定义的声学、语言和发音模型。 自定义有助于解决环境干扰或特定于行业的词汇的问题。

## <a name="get-started-with-speech-to-text"></a>语音转文本入门

语音转文本服务通过[语音 SDK](speech-sdk.md) 提供。 有几种常见方案可作为快速入门，以各种语言和平台提供：

 - [快速入门：识别来自麦克风输入的语音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入门：从文件中识别语音](quickstarts/speech-to-text-from-file.md)
 - [快速入门：识别存储在 Blob 存储中的语音](quickstarts/from-blob.md)

如果你偏向于使用语音转文本 REST 服务，请参阅 [REST API](rest-speech-to-text.md)。

## <a name="tutorials-and-sample-code"></a>教程和示例代码

<!-- After you've had a chance to use the Speech service, try our tutorial that teaches you how to recognize intents from speech using the Speech SDK and LUIS. -->

<!-- - [Tutorial: Recognize intents from speech with the Speech SDK and LUIS, using C#](how-to-recognize-intents-from-speech-csharp.md) -->

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。

- [语音转文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

<!-- ## Customization -->

## <a name="next-steps"></a>后续步骤

- [获取语音服务订阅密钥以进行试用](get-started.md)
- [获取语音 SDK](speech-sdk.md)
