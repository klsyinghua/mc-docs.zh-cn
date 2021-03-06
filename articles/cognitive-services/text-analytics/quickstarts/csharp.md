---
title: 快速入门：使用用于 .NET 的 Azure SDK 和 C# 调用文本分析服务
titleSuffix: Azure Cognitive Services
description: 方便你开始使用文本分析服务和 C# 的信息和代码示例。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
origin.date: 05/28/2019
ms.date: 06/10/2019
ms.author: v-junlch
ms.openlocfilehash: 3d77019249ea91d53d60621fbb6e63039807ad4c
ms.sourcegitcommit: 259c97c9322da7add9de9f955eac275d743c9424
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66830074"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>快速入门：使用 .NET SDK 和 C# 调用文本分析服务
<a name="HOLTop"></a>

本快速入门介绍如何使用用于 .NET 的 Azure SDK 和 C# 来分析语言。 虽然[文本分析](https://www.azure.cn/zh-cn/home/features/cognitive-services/text-analytics/) REST API 与大多数编程语言兼容，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。

> [!NOTE]
> 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) 上获得。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017 或更高版本]
* 文本分析 [SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

还需要在注册期间为你生成的[终结点和访问密钥](../How-tos/text-analytics-how-to-access-key.md)。

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>创建 Visual Studio 解决方案并安装 SDK

1. 创建新的控制台应用 (.NET Core) 项目。 [访问 Visual Studio](https://visualstudio.microsoft.com/vs/)。
1. 右键单击解决方案，并选择“管理解决方案的 NuGet 包”  。
1. 选择“浏览”按钮  。搜索“Microsoft.Azure.CognitiveServices.Language.TextAnalytics”。 

## <a name="authenticate-your-credentials"></a>对凭据进行验证

1. 向 main 类文件（默认为 Program.cs）添加以下 `using` 语句。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. 创建用于存储凭据的新 `ApiKeyServiceClientCredentials` 类，并为每个请求添加此类凭据。

    ```csharp
    /// <summary>
    /// Allows authentication to the API by using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. 更新 `Program` 类。 为文本分析订阅密钥添加一个常量成员，为服务终结点添加另一个。 记住使用适合文本分析订阅的 Azure 区域。

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://chinaeast2.api.cognitive.azure.cn";
    ```
> [!Tip]
> 若要在生产系统中增强机密的安全性，建议使用 [Azure Key Vault](/key-vault/quick-create-net)。
>

## <a name="create-a-text-analytics-client"></a>创建文本分析客户端

在项目的 `Main` 函数中，调用需要调用的示例方法。 传递所定义的 `Endpoint` 和 `SubscriptionKey` 参数。

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

以下部分介绍如何调用每项服务功能。

## <a name="perform-sentiment-analysis"></a>执行情绪分析

1. 创建新函数 `SentimentAnalysisExample()`，该函数使用以前创建的客户端。
2. 生成 `MultiLanguageInput` 对象的列表，其中包含要分析的文档。

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. 在同一函数中，调用 `client.SentimentAsync()` 并获取结果。 然后循环访问这些结果。 输出每个文档的 ID 和情绪分数。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>输出

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="perform-language-detection"></a>执行语言检测

1. 创建新函数 `DetectLanguageExample()`，该函数使用以前创建的客户端。
2. 生成 `LanguageInput` 对象的列表，其中包含你的文档。

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. 在同一函数中，调用 `client.DetectLanguageAsync()` 并获取结果。 然后循环访问这些结果。 输出每个文档的 ID 和第一种返回的语言。

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>输出

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="perform-entity-recognition"></a>执行实体识别

1. 创建新函数 `RecognizeEntitiesExample()`，该函数使用以前创建的客户端。
2. 生成 `MultiLanguageBatchInput` 对象的列表，其中包含你的文档。

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. 在同一函数中，调用 `client.EntitiesAsync()` 并获取结果。 然后循环访问这些结果。 输出每个文档的 ID。 对于每个检测到的实体，输出其维基百科名称、类型和子类型（如果存在），以及其在原始文本中的位置。

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>输出

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="perform-key-phrase-extraction"></a>执行关键短语提取操作

1. 创建新函数 `KeyPhraseExtractionExample()`，该函数使用以前创建的客户端。
2. 生成 `MultiLanguageBatchInput` 对象的列表，其中包含你的文档。

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. 在同一函数中，调用 `client.KeyPhrasesAsync()` 并获取结果。 然后循环访问这些结果。 输出每个文档的 ID 以及任何检测到的关键短语。

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>输出

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)


* [文本分析概述](../overview.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)

