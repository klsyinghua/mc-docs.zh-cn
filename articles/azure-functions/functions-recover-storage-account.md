---
title: 如何排查 Azure Functions 运行时无法访问的问题。
description: 了解如何排查存储帐户无效的问题。
author: alexkarcher-msft
ms.topic: article
ms.date: 01/13/2020
ms.author: v-junlch
ms.openlocfilehash: dc2727b02b0894d70597e02f3fea19492325cba3
ms.sourcegitcommit: 48d51745ca18de7fa05b77501b4a9bf16cea2068
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76116845"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>如何排查“Azure Functions 运行时无法访问”的问题


## <a name="error-text"></a>错误文本
本文旨在排查 Functions 门户中显示的以下错误。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>摘要
当 Azure Functions 运行时无法启动时，会出现此问题。 出现此错误最常见的原因是，函数应用无法访问其存储帐户。 [在此处阅读有关存储帐户要求的详细信息](/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>故障排除
我们将通过四种最常见的错误事例来说明如何识别以及如何解决每种事例。

1. 存储帐户已删除
1. 存储帐户应用程序设置已删除
1. 存储帐户凭据无效
1. 无法访问存储帐户
1. 每日执行配额已满
1. 应用受防火墙保护


## <a name="storage-account-deleted"></a>存储帐户已删除

每个函数应用都需要存储帐户才能运行。 如果该帐户已删除，则函数将无法使用。

### <a name="how-to-find-your-storage-account"></a>如何查找存储帐户

首先在“应用程序设置”中查找存储帐户名称。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 将包含在连接字符串中包装的存储帐户名称。 请阅读[此处应用程序设置参考](/azure-functions/functions-app-settings#azurewebjobsstorage)中的更多细节。

在 Azure 门户中搜索存储帐户，确定它是否仍然存在。 如果它已被删除，则需要重新创建存储帐户，并替换存储连接字符串。 函数代码会丢失，需要再次重新部署它。

## <a name="storage-account-application-settings-deleted"></a>存储帐户应用程序设置已删除

在上一步中，如果没有存储帐户连接字符串，则可能该字符串已被删除或覆盖。 使用部署槽位或 Azure 资源管理器脚本来设置应用程序设置时，通常会删除应用程序设置。

### <a name="required-application-settings"></a>必需的应用程序设置

* 必须
    * [`AzureWebJobsStorage`](/azure-functions/functions-app-settings#azurewebjobsstorage)
* 消耗计划函数需要
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](/azure-functions/functions-app-settings)

[在此处阅读有关这些应用程序设置的信息](/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指南

* 对于这些设置中的任何设置，不要选中“插槽设置”。 当交换部署槽位时，函数会会中断。
* 在自动部署过程中，请勿修改这些设置。
* 必须在创建时提供这些设置并使其生效。 不包含这些设置的自动部署会导致应用无法运行，即使事后添加了这些设置也是如此。

## <a name="storage-account-credentials-invalid"></a>存储帐户凭据无效

如果重新生成存储密钥，则必须更新上面的“存储帐户”连接字符串。 [在此处阅读有关存储密钥管理的详细信息](/storage/common/storage-create-storage-account)。

## <a name="storage-account-inaccessible"></a>无法访问存储帐户

Function App 必须能够访问存储帐户。 阻止 Functions 访问存储帐户的常见问题是：

* Function App 在部署到应用服务环境时，没有正确的网络规则来允许在存储帐户中传入和传出流量
* 存储帐户防火墙已启用，但未配置为允许在 Functions 中传入和传出流量。 [在此处阅读有关存储帐户防火墙配置的详细信息](/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>每日执行配额已满

如果你配置了每日执行配额，则会暂时禁用 Function App，并且许多门户控件将不可用。 

* 若要进行验证，请在门户中打开“平台功能”>“Function App 设置”。 如果超过配额，则将看到以下消息：
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* 删除配额并重启应用可解决此问题。

## <a name="app-is-behind-a-firewall"></a>应用受防火墙保护

如果函数应用托管在[内部负载均衡的应用服务环境](../app-service/environment/create-ilb-ase.md)中，并配置为阻止入站 Internet 流量，或者将[入站 IP 限制](functions-networking-options.md#inbound-ip-restrictions)配置为阻止 Internet 访问，则无法访问函数运行时。 Azure 门户直接调用正在运行的应用以提取函数列表，还会对 KUDU 终结点进行 HTTP 调用。 `Platform Features` 选项卡下的平台级别设置依然可用。

* 若要验证 ASE 配置，请导航到 ASE 所在子网的 NSG，并验证入站规则以允许来自要访问应用程序的计算机的公共 IP 的流量。 还可以通过连接到运行应用的虚拟网络的计算机或虚拟网络中运行的虚拟机使用门户。 [在此处阅读有关入站规则配置的详细信息](/app-service/environment/network-info#network-security-groups)

## <a name="next-steps"></a>后续步骤

现在，Function App 已恢复，请查看快速入门和开发人员参考，以便重新启动并运行！

* [创建第一个 Azure 函数](functions-create-first-azure-function.md)  
  使用 Azure Functions 快速入门立即投入并创建第一个函数。 
* [Azure Functions 开发人员参考](functions-reference.md)  
  提供有关 Azure Functions 运行时的更多技术信息，并为编码函数及定义触发器和绑定提供参考。
* [测试 Azure Functions](functions-test-a-function.md)  
  介绍可用于测试函数的各种工具和技巧。
* [如何缩放 Azure Functions](functions-scale.md)  
  讨论 Azure Functions 提供的服务计划（包括使用托管计划）以及如何选择合适的计划。 
* [详细了解 Azure 应用服务](../app-service/overview.md)  
  Azure Functions 利用 Azure 应用服务执行核心功能，例如部署、环境变量和诊断。 

<!-- Update_Description: wording update -->