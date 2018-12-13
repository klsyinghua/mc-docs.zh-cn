---
title: Azure Functions SendGrid 绑定
description: Azure Functions SendGrid 绑定参考。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 11/29/2017
ms.date: 11/22/2018
ms.author: v-junlch
ms.openlocfilehash: b9e6f40b3f720fc65bc45011bfe1ecca2750ccb1
ms.sourcegitcommit: bfd0b25b0c51050e51531fedb4fca8c023b1bf5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52673253"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 绑定

本文介绍如何使用 Azure Functions 中的 [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) 绑定发送电子邮件。 Azure Functions 支持 SendGrid 的输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 包 2.x 版本中提供了 SendGrid 绑定。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 包 3.x 版本中提供了 SendGrid 绑定。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>示例

参阅语言特定的示例：

- [C#](#c-example)
- [C# 脚本 (.csx)](#c-script-example)
- [JavaScript](#javascript-example)

### <a name="c-example"></a>C# 示例

以下示例演示使用服务总线队列触发器和 SendGrid 输出绑定的 [C# 函数](functions-dotnet-class-library.md)。

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

如果在应用设置中指定了名为“AzureWebJobsSendGridApiKey”的 API 密钥，则可以不设置特性的 `ApiKey` 属性。

### <a name="c-script-example"></a>C# 脚本示例

以下示例演示 *function.json* 文件中的一个 SendGrid 输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。

下面是 function.json 文件中的绑定数据：

```json 
{
    "bindings": [
        {
            "name": "message",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Logging;

public static void Run(ILogger log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>JavaScript 示例

以下示例演示 *function.json* 文件中的一个 SendGrid 输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。

下面是 function.json 文件中的绑定数据：

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) 特性。

有关可以配置的特性属性的信息，请参阅[配置](#configuration)。 下面是某个方法签名中的 `SendGrid` 特性示例：

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

有关完整示例，请参阅 [C# 示例](#c-example)。

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `SendGrid` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型**|| 必需 - 必须设置为 `sendGrid`。|
|direction|| 必需 - 必须设置为 `out`。|
|**name**|| 必需 - 在请求或请求正文的函数代码中使用的变量名称。 只有一个返回值时，此值为 ```$return```。 |
|**apiKey**|**ApiKey**| 包含 API 密钥的应用设置的名称。 如果未设置，默认应用设置名称为“AzureWebJobsSendGridApiKey”。|
|**to**|**To**| 收件人的电子邮件地址。 |
|**from**|**From**| 发件人的电子邮件地址。 |
|**subject**|**主题**| 电子邮件主题。 |
|**text**|**文本**| 电子邮件内容。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的 2.x 版本设置。 有关版本 2.x 中的全局配置设置的详细信息，请参阅 [Azure Functions 版本 2.x 的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|属性  |默认 | 说明 |
|---------|---------|---------| 
|from|不适用|所有函数的发件人电子邮件地址。| 


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

<!-- Update_Description: wording update -->