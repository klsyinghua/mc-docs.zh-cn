---
title: 短语列表 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用 `PhraseListGrammar` 对象为语音服务提供短语列表，以便改进语音转文本的识别结果。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 07/05/2019
ms.date: 01/27/2020
ms.author: v-tawe
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2e0a58f623c85a5aca64fa282f2e656ff1f59977
ms.sourcegitcommit: 94e1c9621b8f81a7078f1412b3a73281d0a8668b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76123364"
---
# <a name="phrase-lists-for-speech-to-text"></a>语音转文本的短语列表

可以通过提供带短语列表的语音服务，改进语音识别的准确度。 短语列表用于确定音频数据中的已知短语，例如某个人的姓名或特定位置。

例如，如果命令为“Move to”，可能的目标为可以说出来的“Ward”，则可添加条目“Move to Ward”。 添加短语以后，在识别该音频时，将其识别成“Move to Ward”而不是“Move toward”的可能性就会增加。

可以向短语列表添加单个单词或完整短语。 在识别过程中，如果音频中包含与整个短语完全匹配的单独短语，则使用短语列表中的一个条目。 如果找不到与该短语的完全匹配项，则不会帮助识别。

>[!Note]
> 目前，短语列表仅支持英语版语音转文本。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表

以下示例演示了如何使用 `PhraseListGrammar` 对象构建短语列表。

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 语音服务用于匹配语音的短语列表的最大数目为 1024 个短语。

也可通过调用 clear() 来清理与 `PhraseListGrammar` 关联的短语。

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> 对 `PhraseListGrammar` 对象的更改会在下次识别时生效，或者会在重新连接到语音服务后生效。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
