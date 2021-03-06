---
title: 模板中的用户定义函数
description: 介绍如何在 Azure 资源管理器模板中定义和使用用户定义函数。
ms.topic: conceptual
origin.date: 09/05/2019
ms.author: v-yeche
ms.date: 01/06/2020
ms.openlocfilehash: ddc94ecdfcf7b14e52a93ee296682100d88e11d9
ms.sourcegitcommit: 6fb55092f9e99cf7b27324c61f5fab7f579c37dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75631469"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure 资源管理器模板中的用户定义函数

在模板中，可以创建自己的函数。 这些函数可在模板中使用。 用户定义函数不同于模板中自动可用的[标准模板函数](template-functions.md)。 当有复杂的表达式在模板中重复使用时，请创建自己的函数。

本文介绍如何在 Azure 资源管理器模板中添加用户定义函数。

## <a name="define-the-function"></a>定义函数

函数需要一个命名空间值以避免命名与模板函数发生冲突。 下面的示例演示一个返回存储帐户名称的函数：

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>使用函数

以下示例演示如何调用函数。

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "China East",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>限制

定义用户函数时，存在一些限制：

* 该函数不能访问变量。
* 函数仅可使用函数中定义的参数。 在用户定义函数中使用 [parameters](template-functions-deployment.md#parameters) 函数时，只能使用该函数的参数。
* 该函数不能调用其他用户定义的函数。
* 该函数不能使用 [reference](template-functions-resource.md#reference) 函数或任何 [list](template-functions-resource.md#list) 函数。
* 该函数的参数不能具有默认值。

## <a name="next-steps"></a>后续步骤

* 若要了解用户定义函数的可用属性，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
* 有关可用模板函数的列表，请参阅 [Azure 资源管理器模板函数](template-functions.md)。

<!-- Update_Description: update meta properties, wording update, update link -->