---
title: 创建自定义语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 如果你已准备好上传数据，请转到自定义语音门户。 创建或选择一个自定义语音项目。 该项目必须与你打算用于语音训练的数据共享正确的语言/区域设置和性别属性。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 07/05/2019
ms.date: 09/23/2019
ms.author: v-tawe
ms.openlocfilehash: ccb5c1588c8bdbfd2619ace12e16a4d70a7dc593
ms.sourcegitcommit: b328fdef5f35155562f10817af44f2a4e975c3aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71267057"
---
# <a name="create-a-custom-voice"></a>创建自定义语音

[为自定义语音准备数据](how-to-custom-voice-prepare-data.md)中已介绍可用于训练自定义语音的不同数据类型，以及不同的格式要求。 准备好数据后，可以开始将其上传到[自定义语音门户](https://speech.microsoft.com/portal?projecttype=customvoice)；也可以通过自定义语音训练 API 上传。 本文介绍通过门户训练自定义语音的步骤。

> [!NOTE]
> 本页假设你已阅读[自定义语音入门](how-to-custom-voice.md)和[为自定义语音准备数据](how-to-custom-voice-prepare-data.md)，并已创建一个自定义语音项目。

检查自定义语音支持的语言：[支持自定义的语言](language-support.md#customization)。

## <a name="upload-your-datasets"></a>上传数据集

如果你已准备好上传数据，请转到[自定义语音门户](https://speech.microsoft.com/portal?projecttype=customvoice)。 创建或选择一个自定义语音项目。 该项目必须与你打算用于语音训练的数据共享正确的语言/区域设置和性别属性。 例如，如果你的音频录制内容是使用英式口音录制的，请选择 `en-GB`。

转到“数据”选项卡并单击“上传数据”。   在向导中，选择与准备好的数据匹配的适当数据类型。

上传的每个数据集必须符合所选数据类型的要求。 在上传数据之前，必须正确设置数据的格式。 这可以确保自定义语音服务准确处理数据。 转到[为自定义语音准备数据](how-to-custom-voice-prepare-data.md)，并确保数据的格式正确。

> [!NOTE]
> 免费订阅 (F0) 用户可以同时上传两个数据集。 标准订阅 (S0) 用户可以同时上传五个数据集。 如果达到限制，请先等待，直至至少其中一个数据集导入完毕。 然后重试。

> [!NOTE]
> 每个订阅允许导入的数据集的最大数目为 10 个（对于免费订阅 (F0) 用户）和 500 个（对于标准订阅 (S0) 用户）.zip 文件。

点击“上传”按钮后，系统会自动验证数据集。 数据验证包括针对音频文件执行一系列检查以验证文件格式、大小和采样率。 修复出现的任何错误，然后重新提交。 成功发起数据导入请求后，数据表中应会出现一个对应于刚刚上传的数据集的条目。

下表显示了已导入数据集的处理状态：

| 状态 | 含义 |
| ----- | ------- |
| 正在处理 | 已收到并正在处理数据集。 |
| 已成功 | 已验证数据集，现在可以使用它来生成语音模型。 |
| 已失败 | 数据集在处理过程中因多种原因（例如文件错误、数据问题或网络问题）而失败。 |

验证完成后，可以在“言语”列中看到每个数据集的已匹配言语总数。  如果所选数据类型需要进行长音频分段，此列只会反映已根据脚本或通过语音听录服务分段的言语。 可以进一步下载已验证的数据集，以查看已成功导入的言语的详细结果及其映射脚本。 提示：长音频分段可能需要一小时以上才能完成数据处理。

对于 en-US 和 zh-CN 数据集，可以下载一份报告，以检查每个录制内容的发音评分和噪音级别。 发音评分范围为 0 到 100。 评分低于 70 通常表示语音错误或脚本不匹配。 口音重可能会降低发音分数，影响生成的数字语音。

信噪比 (SNR) 高表明音频中的噪音少。 通过专业录音棚录音通常可以达到 50+ 的 SNR。 音频的 SNR 低于 20 可能导致生成的语音中出现明显的噪音。

考虑重写录制发音分数低或信噪比不佳的表述。 如果无法重新录制，可以从数据集中排除这些话语。

## <a name="build-your-custom-voice-model"></a>生成自定义语音模型

验证数据集后，可以使用它来生成自定义语音模型。

1.  导航到“文本转语音”>“自定义语音”>“训练”。 

2.  单击“训练模型”。 

3.  接下来，输入**名称**和**说明**以帮助识别此模型。

    请谨慎选择名称。 此处输入的名称将是在 SSML 输入过程中在请求中指定语音合成所需语音时使用的名称。 只允许字母、数字以及部分标点字符，例如“-”、“\_”和“,”。 请对不同的语音模型使用不同的名称。

    通常使用“说明”字段来记录创建模型时所使用的数据集的名称。 

4.  在“选择训练数据”页中，选择一个或多个用于训练的数据集。  提交言语之前请检查其数目。 对于 en-US 和 zh-CN 语音模型，可以从任意数目的言语着手。 对于其他区域设置，必须选择 2,000 个以上的言语才能训练语音。

    > [!NOTE]
    > 在训练中将会删除重复的音频名称。 确保所选的数据集不会在多个 .zip 文件中包含相同的音频名称。

    > [!TIP]
    > 为获得优质结果，必须使用同一讲话者的数据集。 如果提交用于训练的数据集包含的相异言语总数小于 6,000 个，你将通过“统计参数合成”技术训练语音模型。 如果训练数据的相异言语总数超过 6,000 个，则你将使用“串联合成”技术启动训练过程。 通常，串联技术可以生成更自然、更具保真度的语音结果。 

    <!-- [Contact the Custom Voice team](mailto:speechsupport@microsoft.com) if you want to train a model with the latest Neural TTS technology that can produce a digital voice equivalent to the publically available [neural voices](language-support.md#neural-voices). -->

5.  单击“训练”开始创建语音模型。 

“训练”表将显示对应于此新建模型的新条目。 该表还会显示以下状态：“正在处理”、“成功”、“失败”。

显示的状态反映了将数据集转换为语音模型的过程，如下所示。

| 状态 | 含义 |
| ----- | ------- |
| 正在处理 | 正在创建语音模型。 |
| 已成功 | 语音模型已创建并可部署。 |
| 已失败 | 语音模型在训练过程中由于多种原因（例如，察觉不到的数据问题或网络问题）而失败。 |

训练时间因处理的音频数据量而异。 通常情况下，处理数百个表述需要大约 30 分钟的时间，处理 20,000 个表述需要 40 小时。 模型训练成功后，可以开始对其进行测试。

> [!NOTE]
> 免费订阅 (F0) 用户可以同时训练一个语音字体。 标准订阅 (S0) 用户可以同时训练三个语音。 如果达到限制，请先等待，直至至少其中一种语音字体训练完毕，然后再试。

> [!NOTE]
> 允许为每个订阅训练的语音模型的最大数目为 10 个（对于免费订阅 (F0) 用户）和 100 个（对于标准订阅 (S0) 用户）模型。

## <a name="test-your-voice-model"></a>测试语音模型

成功生成语音字体以后，可以对其先测试后部署，然后就可以使用了。

1.  导航到“文本转语音”>“自定义语音”>“测试”。 

2.  单击“添加测试”。 

3.  选择要测试的一个或多个模型。

4.  提供语音中要朗读的文本。 如果选择一次性测试多个模型，则测试不同的模型时将使用相同的文本。

    > [!NOTE]
    > 文本的语言必须与语音字体的语言相同。 只能测试已成功训练的模型。 此步骤仅支持纯文本。

5.  单击**创建**。

提交测试请求后，将返回到测试页。 表中现在会有新请求的对应条目以及状态列。 可能需要数分钟来合成语音。 状态列显示“成功”后，你可以播放音频，或下载文本输入（txt 文件）和音频输出（.wav 文件），然后进一步试听后者以检查其质量。 

还可以在选择用于测试的每个模型的详细信息页中找到测试结果。 转到“训练”选项卡，然后单击模型名称进入模型详细信息页。 

## <a name="create-and-use-a-custom-voice-endpoint"></a>创建并使用自定义语音终结点

成功创建并测试语音模型以后，即可在自定义的文本转语音终结点中部署它。 然后即可在通过 REST API 发出文本转语音请求时使用此终结点来替代常用的终结点。 只能通过部署字体时所用的订阅来调用自定义终结点。

若要创建新的自定义语音终结点，请转到“文本转语音”>“自定义语音”>“部署”。  选择“添加终结点”，并输入自定义终结点的**名称**和**说明**。  然后选择要与此终结点关联的自定义语音模型。

单击“添加”按钮后，会在终结点表中看到新终结点的条目。  新终结点的实例化可能需要数分钟。 当部署状态为“成功”时，终结点便可供使用。 

> [!NOTE]
> 免费订阅 (F0) 用户只能部署一个模型。 标准订阅 (S0) 用户可以创建多达 50 个终结点，每个终结点具有自身的自定义语音。

> [!NOTE]
> 若要使用自定义语音，必须指定语音模型名称，直接使用 HTTP 请求中的自定义 URI，并使用同一订阅来通过 TTS 服务的身份验证。

部署终结点后，其名称将以链接的形式显示。 单击此链接可显示特定于该终结点的信息，例如终结点密钥、终结点 URL 和示例代码。

也可通过自定义语音门户对终结点进行联机测试。 若要测试终结点，请在“终结点详细信息”页中选择“检查终结点”。   此时会显示终结点测试页。 在文本框中输入要朗读的文本（采用纯文本或 [SSML 格式](speech-synthesis-markup.md)）。 若要收听以自定义语音字体朗读的文本，请选择“播放”。  此项测试功能会收取自定义语音合成使用费。

从功能上说，自定义终结点与用于文本转语音请求的标准终结点相同。 有关详细信息，请参阅 [REST API](rest-text-to-speech.md)。

## <a name="next-steps"></a>后续步骤

* [指南：录制语音样本](record-custom-voice-samples.md)
* [文本转语音 API 参考](rest-text-to-speech.md)