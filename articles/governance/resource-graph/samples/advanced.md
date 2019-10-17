---
title: 高级查询示例
description: 使用 Azure 资源图来运行一些高级查询，包括 VMSS 容量、列出所有使用的标记以及使用正则表达式匹配虚拟机。
author: DCtheGeek
ms.author: v-yiso
origin.date: 08/29/2019
ms.date: 10/21/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b767f04dd75da9f08b5fef6ba9359f8c6a4974b2
ms.sourcegitcommit: b83f604eb98a4b696b0a3ef3db2435f6bf99f411
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72292558"
---
# <a name="advanced-resource-graph-queries"></a>高级资源图表查询

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 [Azure 数据资源管理器](../../../data-explorer/data-explorer-overview.md)，建议查看基础知识，以了解如何撰写所需资源的请求。

我们将逐步介绍以下高级查询：

> [!div class="checklist"]
> - [获取虚拟机规模集容量和大小](#vmss-capacity)
> - [列出所有标记名称](#list-all-tags)
> - [由正则表达式匹配的虚拟机](#vm-regex)
> - [使用 DisplayNames 包括租户和订阅名称](#displaynames)

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。
## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在运行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="vmss-capacity"></a>获取虚拟机规模集容量和大小

此查询将查找虚拟机规模集资源，并获取各种详细信息，包括规模集的虚拟机大小和容量。 此查询使用 `toint()` 函数将容量强制转换为数字以供排序。 最后会将列重命名为自定义命名属性。

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"> </a>列出所有标记名称

此查询以标记开头，并生成一个 JSON 对象，列出所有唯一标记名称及其对应的类型。

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>由正则表达式匹配的虚拟机

此查询查找与某个[正则表达式](https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference)（称为 _regex_）匹配的虚拟机。
可以使用 **matches regex \@** 定义要匹配的正则表达式，即 `^Contoso(.*)[0-9]+$`。 该 regex 定义说明如下：

- `^` - 匹配项必须以该字符串的开头开头。
- `Contoso` - 区分大小写的字符串。
- `(.*)` - 子表达式匹配项：
  - `.` - 匹配任何单一字符（换行符除外）。
  - `*` - 匹配上一个元素零次或多次。
- `[0-9]` - 数字 0 到 9 的字符组匹配项。
- `+` - 匹配上一个元素一次或多次。
- `$` - 上一个元素的匹配项必须出现在该字符串的末尾。

按名称进行匹配后，查询将对名称进行投影并按名称升序排序。

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>使用 DisplayNames 包括租户和订阅名称

此查询使用新的 **Include** 参数和选项 _DisplayNames_ 将 **subscriptionDisplayName** 和 **tenantDisplayName** 添加到结果中。 此参数仅可用于 Azure CLI 和 Azure PowerShell。

```azurecli
az graph query -q "limit 1" --include displayNames
```

```azurepowershell
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> 如果查询未使用 **project** 指定返回的属性，则 **subscriptionDisplayName** 和 **tenantDisplayName** 将自动包括在结果中。
> 如果查询确实使用了 **project**，则每个 _DisplayName_ 字段必须显式包含在 **project** 中，否则它们将不会在结果中返回，即使使用了 **Include** 参数也是如此。

## <a name="next-steps"></a>后续步骤

- 查看[初学者查询](starter.md)的示例
- 了解有关[查询语言](../concepts/query-language.md)的详细信息
- 了解如何[浏览资源](../concepts/explore-resources.md)