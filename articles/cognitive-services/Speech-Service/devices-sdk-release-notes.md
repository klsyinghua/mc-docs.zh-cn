---
title: 语音设备 SDK 文档
titleSuffix: Azure Cognitive Services
description: 发行说明提供了有关语音设备 SDK 的更新、增强功能、bug 修复和更改的日志。 本文随每个版本的语音设备 SDK 进行更新。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 11/13/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: f43c7a0511346b780e56ed3f218730c94dd5710d
ms.sourcegitcommit: 94e1c9621b8f81a7078f1412b3a73281d0a8668b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76123272"
---
# <a name="release-notes-speech-devices-sdk"></a>发行说明：语音设备 SDK

以下部分列出了最新版本中的更改。

## <a name="speech-devices-sdk-170"></a>语音设备 SDK 1.7.0：

- 现在支持 Linux ARM。
- 提供了 Roobo v2 的初始二进制文件 (Linux ARM64)。
- Windows 用户可以使用 `AudioConfig.fromDefaultMicrophoneInput()` 或 `AudioConfig.fromMicrophoneInput(deviceName)` 指定要使用的麦克风。
- 库大小已经过优化。
- 支持使用相同的语音/意向识别器对象进行多轮次识别。
- 修复停止识别时可能发生的偶尔挂起。
- 示例应用现在包含一个示例 participants.properties 文件，用于演示文件的格式。
- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.7.0 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。

## <a name="speech-devices-sdk-160"></a>语音设备 SDK 1.6.0：

- 在 Windows 和 Linux 上支持 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)，提供常见的[示例应用程序](https://aka.ms/sdsdk-download)
- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.6.0 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。

## <a name="speech-devices-sdk-151"></a>语音设备 SDK 1.5.1：

- 在示例应用中包括[对话听录](conversation-transcription-service.md)。
- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.5.1 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>语音设备 SDK 1.5.0：2019 年 5 月发布

- 语音设备 SDK 现为 GA，不再是受限的预览版。
- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.5.0 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。
- 新的关键字技术带来了显著的质量改进，请参阅“中断性变更”。
- 新的音频处理管道改进了远端域识别。

**重大更改**

- 由于新出现的关键字技术，所有关键字必须在改进的关键字门户中重新创建。 若要从设备中完全删除旧的关键字，请卸载旧应用。
  - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>语音设备 SDK 1.4.0：2019 年 4 月发布

- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.4.0 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>语音设备 SDK 1.3.1：2019 年 3 月发布

- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.3.1 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。
- 更新了关键字处理，请参阅“中断性变更”。
- 示例应用程序添加了选择语言的功能，适用于语音识别和翻译。

<!-- **Breaking changes** -->
<!-- create kws not available -->
<!-- - [Installing a keyword](https://docs.azure.cn/cognitive-services/speech-service/speech-devices-sdk-create-kws) has been simplified, it is now part of the app and does not need separate installation on the device. -->
<!-- - The keyword recognition has changed, and two events are supported. -->
<!--   - `RecognizingKeyword,` indicates the speech result contains (unverified) keyword text. -->
<!--   - `RecognizedKeyword`, indicates that keyword recognition completed recognizing the given keyword. -->

## <a name="speech-devices-sdk-110-2018-nov-release"></a>语音设备 SDK 1.1.0：2018 年 11 月版本

- 已将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到 1.1.0 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。
- 远场语音识别准确性已通过我们增强的音频处理算法得到提高。
- 示例应用程序添加了中文语音识别支持。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>语音设备 SDK 1.0.1：2018 年 10 月版本

- 将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到了 1.0.1 版。 有关详细信息，请参阅其[发行说明](https://docs.azure.cn/cognitive-services/speech-service/releasenotes)。
- 我们改进的音频处理算法将提高语音识别的准确性
- 修复了一个连续识别音频会话 bug。

**重大更改**

- 该版本中推出了大量重大更改。 有关 API 的详细信息，请查看[此页](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/docs/breaking_changes_1_0_0.md)。

<!-- - The KWS model files are not compatible with Speech Devices SDK 1.0.1. The existing keyword files will be deleted after the new keyword files are written to the device. -->

## <a name="speech-devices-sdk-050-2018-aug-release"></a>语音设备 SDK 0.5.0：2018 年 8 月版本

- 通过修复音频处理代码中的 Bug，改进了语音识别的准确性。
- 将[语音 SDK](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk) 组件更新到了 0.5.0 版。 有关详细信息，请参阅其[发行说明](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>语音设备 SDK 0.2.12733：2018 年 5 月版本

认知服务语音设备 SDK 的第一个公共预览版本。
