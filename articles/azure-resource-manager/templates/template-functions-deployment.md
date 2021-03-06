---
title: 模板函数 - 部署
description: 介绍可在 Azure Resource Manager 模板中使用的用于检索部署信息的函数。
ms.topic: conceptual
origin.date: 11/27/2019
ms.author: v-yeche
ms.date: 01/06/2020
ms.openlocfilehash: 2894d2342138830df3749c9331623bdd772af9cc
ms.sourcegitcommit: 6fb55092f9e99cf7b27324c61f5fab7f579c37dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75631320"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>用于 Azure Resource Manager 模板的部署函数 

资源管理器提供了以下函数，用于获取与当前部署相关的值：

* [部署](#deployment)
* [环境](#environment)
* [parameters](#parameters)
* [variables](#variables)

若要从资源、资源组或订阅获取值，请参阅 [Resource functions](template-functions-resource.md)（资源函数）。

<a name="deployment"></a>

## <a name="deployment"></a>部署

`deployment()`

返回有关当前部署操作的信息。

### <a name="return-value"></a>返回值

此函数返回部署期间传递的对象。 根据部署对象是作为链接还是内联对象传递，所返回对象中的属性将有所不同。 如果部署对象是以内联形式传递的（例如使用 Azure PowerShell 中的 **-TemplateFile** 参数指向本地文件时），所返回的对象采用以下格式：

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

如果对象是以链接形式传递的（例如使用 **-TemplateUri** 参数指向远程文件时），所返回的对象采用以下格式： 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

[部署到 Azure 订阅](deploy-to-subscription.md)而不是资源组时，返回对象包含 `location` 属性。 部署本地模板或外部模板时包含 location 属性。

### <a name="remarks"></a>备注

如何根据父模板的 URI，使用 deployment() 链接到另一个模板。

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

如果从门户中的部署历史记录重新部署模板，则该模板将部署为本地文件。 部署函数不返回 `templateLink` 属性。 如果模板依赖于 `templateLink` 来构建指向另一个模板的链接，请不要使用门户进行重新部署， 而是使用最初部署模板时使用的命令。

### <a name="example"></a>示例

下面的[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)返回部署对象：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

前面的示例返回以下对象：

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

对于使用部署功能的订阅级别模板，请参阅[订阅部署功能](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)。 它使用 `az deployment create` 或 `New-AzDeployment` 命令进行部署。

## <a name="environment"></a>环境

`environment()`

返回有关用于部署的 Azure 环境的信息。

### <a name="return-value"></a>返回值

此函数返回当前 Azure 环境的属性。 以下示例显示了全局 Azure 的属性。 主权云可能会返回略有不同的属性。

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>示例

以下示例模板返回环境对象。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

前面的示例在部署到全局 Azure 时返回以下对象：

<!--Generate the report on 12/04/2019-->
<!--Verified successfully-->

```json
{
    "name":"AzureChinaCloud",
    "gallery":"https://gallery.chinacloudapi.cn",
    "graph":"https://graph.chinacloudapi.cn",
    "portal":"https://portal.azure.cn",
    "graphAudience":"https://graph.chinacloudapi.cn",
    "activeDirectoryDataLake":null,
    "batch":"https://batch.chinacloudapi.cn",
    "media":"https://rest.media.chinacloudapi.cn",
    "sqlManagement":"https://management.core.chinacloudapi.cn:8443",
    "vmImageAliasDoc":"https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
    "resourceManager":"https://management.chinacloudapi.cn",
    "authentication":{
        "loginEndpoint":"https://login.chinacloudapi.cn",
        "audiences":[
            "https://management.core.chinacloudapi.cn/",
            "https://management.chinacloudapi.cn/"
        ],
        "tenant":"common",
        "identityProvider":"AAD"
    },
    "suffixes":{
        "acrLoginServer":".azurecr.cn",
        "azureDatalakeAnalyticsCatalogAndJob":null,
        "azureDatalakeStoreFileSystem":null,
        "azureFrontDoorEndpointSuffix":"",
        "keyvaultDns":".vault.azure.cn",
        "sqlServerHostname":".database.chinacloudapi.cn",
        "storage":"core.chinacloudapi.cn"
    }
}
```
<!--Generate the report on 12/04/2019-->

## <a name="parameters"></a>参数

`parameters(parameterName)`

返回一个参数值。 指定的参数名称必须已在模板的 parameters 节中定义。

### <a name="parameters"></a>parameters

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |string |要返回的参数名称。 |

### <a name="return-value"></a>返回值

指定的参数的值。

### <a name="remarks"></a>备注

通常，使用参数设置资源值。 以下示例将 Web 站点的名称设置为在部署过程中传递的参数值。

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)演示了 parameters 函数的简化用法。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| stringOutput | String | 选项 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 选项 1 |

如需详细了解如何使用参数，请参阅 [Azure 资源管理器模板中的参数](template-parameters.md)。

<a name="variables"></a>

## <a name="variables"></a>variables

`variables(variableName)`

返回变量的值。 指定的变量名称必须已在模板的 variables 节中定义。

### <a name="parameters"></a>parameters

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| variableName |是 |String |要返回的变量名称。 |

### <a name="return-value"></a>返回值

指定的变量的值。

### <a name="remarks"></a>备注

通常，使用变量通过只构造一次复杂值来简化模板。 以下示例构造存储帐户的唯一名称。

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)返回了不同的变量值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

如需详细了解如何使用变量，请参阅 [Azure 资源管理器模板中的变量](template-variables.md)。

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 若要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](create-multiple-instances.md)。
* 要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

<!-- Update_Description: update meta properties, wording update, update link -->
