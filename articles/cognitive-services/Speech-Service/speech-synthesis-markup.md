---
title: 语音合成标记语言 (SSML) - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音合成标记语言来控制文本到语音转换中的发音和韵律。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 07/05/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: efaca4e00a8d4c82b8904d5760b96ce8133e3c04
ms.sourcegitcommit: a7a199c76ef4475b54edd7d5a7edb7b91ea8dff7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2020
ms.locfileid: "76966555"
---
# <a name="speech-synthesis-markup-language-ssml"></a>语音合成标记语言 (SSML)

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，可让开发人员指定如何使用文本转语音服务将输入文本转换为合成语音。 与纯文本相比，SSML 可让开发人员微调音节、发音、语速、音量以及文本转语音输出的其他属性。 SSML 可自动处理正常的停顿（例如，在句号后面暂停片刻），或者在以问号结尾的句子中使用正确的音调。

SSML 的语音服务实现基于万维网联合会的[语音合成标记语言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、日语和韩语字符按两个字符计费。 有关详细信息，请参阅[定价](https://www.azure.cn/pricing/details/cognitive-services/)。

<!-- neural and custom voices not supported -->

## <a name="standard-voices"></a>标准语音

从标准语音中进行选择，在 10 种以上的语言和区域设置中，有 50 种以上的标准语音可用。 有关支持的语言、区域设置和标准语音的完整列表，请参阅[语言支持](language-support.md)。

若要详细了解标准语音，请参阅[文本转语音概述](text-to-speech.md)。

## <a name="special-characters"></a>特殊字符

使用 SSML 将文本转换为合成语音时，请记住，与 XML 一样，特殊字符（例如引号、撇号和括号）必须经过转义。 有关详细信息，请参阅[可扩展标记语言 (XML) 1.0：附录 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支持的 SSML 元素

每个 SSML 文档是使用 SSML 元素（或标记）创建的。 这些元素用于调整音节、韵律、音量等。 以下部分详细说明了每个元素的用法，以及该元素是必需的还是可选的。  

> [!IMPORTANT]
> 不要忘记将属性值括在双引号中。 适当格式的有效 XML 的标准要求将属性值括在双引号中。 例如，`<prosody volume="90">` 是适当格式的有效元素，而 `<prosody volume=90>` 则不是。 SSML 无法识别未括在引号中的属性值。

## <a name="create-an-ssml-document"></a>创建 SSML 文档

`speak` 是根元素，对于所有 SSML 文档都是**必需的**。 `speak` 元素包含重要信息，例如版本、语言和标记词汇定义。

**语法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| 版本 | 指示用于解释文档标记的 SSML 规范的版本。 当前版本为 1.0。 | 必须 |
| xml:lang | 指定根文档的语言。 该值可以包含小写的双字母语言代码（例如 **en**），或者语言代码加上大写的国家/地区代码（例如 **en-US**）。 | 必须 |
| xmlns | 指定文档的 URI，用于定义 SSML 文档的标记词汇（元素类型和属性名称）。 当前 URI 为 https://www.w3.org/2001/10/synthesis 。 | 必须 |

## <a name="choose-a-voice-for-text-to-speech"></a>选择文本转语音所用的语音

`voice` 元素是必需的。 它用于指定文本转语音所用的语音。

**语法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本转语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必须 |

**示例**

> [!NOTE]
> 本示例使用 `en-US-Jessa24kRUS` 语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多个语音

在 `speak` 元素中，可为文本转语音输出指定多种语音。 这些语音可以采用不同的语言。 对于每种语音，必须将文本包装在 `voice` 元素中。

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本转语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必须 |

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

<!-- ## Adjust speaking styles -->

<!-- > [!IMPORTANT] -->
<!-- > This feature will only work with neural voices. -->

## <a name="add-or-remove-a-breakpause"></a>添加或删除中断/暂停

使用元素 `break` 可在单词之间插入暂停（或中断），或者防止文本转语音服务自动添加暂停。

> [!NOTE]
> 如果某个单词或短语的合成语音听起来不自然，可以使用此元素来重写该单词或短语的默认文本转语音 (TTS) 行为。 将 `strength` 设置为 `none` 可防止文本转语音服务自动插入的韵律中断。

**语法**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| strength | 使用以下值之一指定暂停的相对持续时间：<ul><li>无</li><li>x-weak</li><li>weak</li><li>medium（默认值）</li><li>strong</li><li>x-strong</li></ul> | 可选 |
| time | 指定暂停的绝对持续时间，以秒或毫秒为单位。 例如，2s 和 500 是有效值 | 可选 |

| Strength | 说明 |
|----------|-------------|
| None，或者不提供任何值 | 0 毫秒 |
| x-weak | 250 毫秒 |
| weak | 500 毫秒 |
| 中 | 750 毫秒 |
| strong | 1000 毫秒 |
| x-strong | 1250 毫秒 |


**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和 `s` 元素分别用于表示段落和句子。 如果不指定这些元素，则文本转语音服务会自动确定 SSML 文档的结构。

`p` 元素可包含文本和以下元素：`audio`、`break`、`phoneme`、`prosody`、`say-as`、`sub`、`mstts:express-as` 和 `s`。

`s` 元素可包含文本和以下元素：`audio`、`break`、`phoneme`、`prosody`、`say-as`、`mstts:express-as` 和 `sub`。

**语法**

```XML
<p></p>
<s></s>
```

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改善发音

`ph` 元素用于 SSML 文档中的发音。 `ph` 元素只能包含文本，而不能包含其他元素。 始终提供人类可读的语音作为回退。

音标由音素构成，而这些音素由字母、数字或字符（有时是它们的组合）构成。 每个音素描述独特的语音。 这与拉丁音标不同，其中的任一字母可以表示多种语音。 想像一下单词“candy”和“cease”中字母“c”的不同发音，或者字母组合“th”在单词“thing”和“those”中的不同发音。

**语法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| alphabet | 指定在 `ph` 属性中合成字符串发音时要使用的音标。 指定音标的字符串必须以小写字母指定。 下面是可以指定的可能音标。<ul><li>ipa &ndash; 国际音标</li><li>sapi &ndash; 语音 API 音素集</li><li>ups &ndash; 通用音素集</li></ul>音标仅适用于元素中的音素。 有关详细信息，请参阅[音标参考](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 可选 |
| ph | 一个字符串，包含用于在 `phoneme` 元素中指定单词发音的音素。 如果指定的字符串包含无法识别的音素，则文本转语音 (TTS) 服务将拒绝整个 SSML 文档，并且不会生成文档中指定的任何语音输出。 | 如果使用音素，则此属性是必需的。 |

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>调整韵律

`prosody` 元素用于指定文本转语音输出的音节、调型、范围、速率、持续时间和音量的变化。 `prosody` 元素可包含文本和以下元素：`audio`、`break`、`p`、`phoneme`、`prosody`、`say-as`、`sub` 和 `s`。

由于韵律属性值可在较大范围内变化，因此，语音识别器会将分配的值解释为所选语音的建议实际韵律值。 文本转语音服务将限制或替代不支持的值。 例如，音节 1 MHz 或音量 120 就是不支持的值。

**语法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| 音节 | 指示文本的基线音节。 可将音节表述为：<ul><li>以某个数字后接“Hz”（赫兹）表示的绝对值。 例如 600Hz。</li><li>以前面带有“+”或“-”的数字，后接“Hz”或“st”（用于指定音节的变化量）表示的相对值。 例如：+80Hz 或 -2st。 “st”表示变化单位为半音，即，标准全音阶中的半调（半步）。</li><li>常量值：<ul><li>x-low</li><li>low</li><li>中</li><li>high</li><li>x-high</li><li>默认值</li></ul></li></ul>上获取。 | 可选 |
| contour | 调型以语音输出中位于指定时间处的目标数组形式表示语音内容的音节变化。 每个目标由参数对的集定义。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每参数集中的第一个值以文本持续时间百分比的形式指定音节变化的位置。 第二个值使用音节的相对值或枚举值指定音节的升高或降低量（请参阅 `pitch`）。 | 可选 |
| range  | 表示文本音节范围的值。 可以使用用于描述 `pitch` 的相同绝对值、相对值或枚举值表示 `range`。 | 可选 |
| rate  | 指示文本的讲出速率。 可将 `rate` 表述为：<ul><li>以充当默认值倍数的数字表示的相对值。 例如，如果值为 *1*，则速率不会变化。 如果值为 *0.5*，则速率会减慢一半。 如果值为 *3*，则速率为三倍。</li><li>常量值：<ul><li>x-slow</li><li>slow</li><li>中</li><li>fast</li><li>x-fast</li><li>默认值</li></ul></li></ul> | 可选 |
| duration  | 语音合成 (TTS) 服务读取文本时应该消逝的时长，以秒或毫秒为单位。 例如 *2s* 或 *1800ms*。 | 可选 |
| 卷  | 指示语音的音量级别。 可将音量表述为：<ul><li>以从 0.0 到 100.0（从最安静到最大声）的数字表示的绝对值。   例如 75。 默认值为 100.0。</li><li>以前面带有“+”或“-”的数字表示的相对值，指定音量的变化量。 例如 +10 或 -5.5。</li><li>常量值：<ul><li>silent</li><li>x-soft</li><li>soft</li><li>中</li><li>loud</li><li>x-loud</li><li>默认值</li></ul></li></ul> | 可选 |

### <a name="change-speaking-rate"></a>更改语速

可以在单词或句子级别对标准语音应用语速。 

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>更改音量

可以在单词或句子级别对标准语音应用音量变化。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>更改音高

可以在单词或句子级别对标准语音应用音节变化。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>更改音高升降曲线

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as 元素  

`say-as` 是一个可选元素，指示元素文本的内容类型（例如数字或日期）。 它为语音合成引擎提供有关如何朗读文本的指导。 

**语法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| interpret-as | 指示元素文本的内容类型。 有关类型列表，请参阅下表。 | 必须 |
| format | 为可能具有不明确格式的内容类型提供有关元素文本的精确格式设置的更多信息。 SSML 为使用它们的内容类型定义格式（请参阅下表）。 | 可选 |
| 详细信息 | 指示要朗读的详细信息的级别。 例如，此属性可以要求语音合成引擎朗读标点符号。 没有为 `detail` 定义标准值。 | 可选 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

下面是 `interpret-as` 和 `format` 属性支持的内容类型。 仅当 `interpret-as` 设置为日期和时间时，才包括 `format` 属性。

| interpret-as | format | 解释 |
|--------------|--------|----------------|
| address | | 此文本朗读为地址。 语音合成引擎将以下内容：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />朗读为：“I'm at 150th court north east redmond washington.” |
| cardinal, number | | 此文本朗读为基数。 语音合成引擎将以下内容：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />朗读为“There are three alternatives.”。 |
| characters, spell-out | | 此文本朗读为各个字符（拼读出来）。 语音合成引擎将以下内容：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />朗读为“T E S T.” |
| date  | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | 此文本朗读为日期。 `format` 属性指定日期的格式 (*d=day, m=month, and y=year*)。 语音合成引擎将以下内容：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />朗读为“Today is October nineteenth two thousand sixteen.” |
| digits, number_digit | | 此文本朗读为个体数字的序列。 语音合成引擎将以下内容：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />朗读为“1 2 3 4 5 6 7 8 9.” |
| fraction | | 此文本朗读为分数。 语音合成引擎将以下内容：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />朗读为“three eighths of an inch.” |
| 序号  | | 此文本朗读为基数。 语音合成引擎将以下内容：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />朗读为“Select the third option”。 |
| telephone  | | 此文本朗读为电话号码。 `format` 属性可以包含表示国家/地区代码的数字。 例如，“1”表示美国，“39”表示意大利。 语音合成引擎可以使用此信息来指导其电话号码的发音。 电话号码中也可能包含国家/地区代码，如果是，则它优先于 `format` 中的国家/地区代码。 语音合成引擎将以下内容：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />朗读为“My number is area code eight eight eight five five five one two one two.” |
| time | hms12, hms24 | 此文本朗读为时间。 `format` 属性指定时间是使用 12 小时制 (hms12) 还是 24 小时制 (hms24) 指定的。 请使用冒号分隔表示小时、分钟和秒的数字。 下面是有效的时间示例：12:35、1:14:32、08:15 和 02:50:45。 语音合成引擎将以下内容：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />朗读为“The train departs at four A M.” |

**使用情况**

`say-as` 元素只能包含文本。

**示例**

语音合成引擎将以下示例朗读为“Your first request was for one room on October nineteenth twenty ten with early arrival at twelve thirty five P M.”
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>添加录制的音频

`audio` 是一个可选元素，用于将 MP3 音频插入 SSML 文档。 如果音频文件不可用或不可播放，可在音频元素的正文中包含可讲述的纯文本或 SSML 标记。 此外，`audio` 元素可包含文本和以下元素：`audio`、`break`、`p`、`s`、`phoneme`、`prosody`、`say-as` 和 `sub`。

包含在 SSML 文档中的任何音频必须满足以下要求：

* MP3 必须托管在可通过 Internet 访问的 HTTPS 终结点上。 必须使用 HTTPS，托管 MP3 文件的域必须提供有效的受信任 SSL 证书。
* MP3 必须是有效的 MP3 文件 (MPEG v2)。
* 比特率必须是 48 kbps。
* 采样率必须是 16000 Hz。
* 单个响应中所有文本和音频文件的总时间不能超过 90 秒。
* MP3 不得包含任何客户特定的信息或其他敏感信息。

**语法**

```xml
<audio src="string"/></audio>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| src | 指定音频文件的位置/URL。 | 在 SSML 文档中使用音频元素时，此属性是必需的。 |

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>添加背景音频

使用 `mstts:backgroundaudio` 元素可将背景音频添加到 SSML 文档（或者使用文本转语音来混合音频文件）。 使用 `mstts:backgroundaudio` 可以在后台循环音频文件，在文本转语音的开头淡入，并在文本转语音的末尾淡出。

如果提供的背景音频短于文本转语音或淡出持续时间，则会循环该音频。 如果其长度超过文本转语音的持续时间，则它在完成淡出后将会停止。

每个 SSML 文档仅允许一个背景音频文件。 但是，可以在 `voice` 元素中散布 `audio` 标记，以将更多的音频添加到 SSML 文档。

**语法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| src | 指定背景音频文件的位置/URL。 | 如果在 SSML 文档中使用背景音频，则此属性是必需的。 |
| 卷 | 指定背景音频文件的音量。 **接受的值**：`0` 到 `100`（含）。 默认值为 `1`。 | 可选 |
| fadein | 指定背景音频淡入的持续时间，以毫秒为单位。 默认值为 `0`，即，不淡入。 **接受的值**：`0` 到 `10000`（含）。  | 可选 |
| fadeout | 指定背景音频淡出的持续时间，以毫秒为单位。 默认值为 `0`，即，不淡出。**接受的值**：`0` 到 `10000`（含）。  | 可选 |

**示例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>后续步骤

* [语言支持：语音、区域设置、语言](language-support.md)
