---
title: 示例 - 如果扩展不存在，则进行审核
description: 如果参数中定义的扩展未部署到虚拟机，此示例策略定义会进行审核。
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 01/23/2019
ms.date: 02/17/2020
ms.author: v-tawe
ms.openlocfilehash: 5b1aac5528312be4b3d7c884f24ef0551500bb2d
ms.sourcegitcommit: 0b07f1d36ac02da055874630d6edc31cb0a15269
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112179"
---
# <a name="sample---audit-if-extension-does-not-exist"></a>示例 - 如果扩展不存在，则进行审核

如果虚拟机未部署扩展，则此策略会进行审核。 指定扩展发布者和类型以检查是否已部署扩展。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板
```json
{
    "type": "Microsoft.Authorization/policyDefinitions",
    "name": "audit-vm-extension",
    "properties": {
        "displayName": "Audit if extension does not exist",
        "description": "This policy audits if a required extension doesn't exist.",
        "parameters": {
            "publisher": {
                "type": "String",
                "metadata": {
                    "description": "The publisher of the extension",
                    "displayName": "Extension Publisher"
                }
            },
            "type": {
                "type": "String",
                "metadata": {
                    "description": "The type of the extension",
                    "displayName": "Extension Type"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Compute/virtualMachines"
                    },
                    {
                        "field": "Microsoft.Compute/imagePublisher",
                        "in": [
                            "MicrosoftWindowsServer"
                        ]
                    },
                    {
                        "field": "Microsoft.Compute/imageOffer",
                        "in": [
                            "WindowsServer"
                        ]
                    }
                ]
            },
            "then": {
                "effect": "auditIfNotExists",
                "details": {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "existenceCondition": {
                        "allOf": [
                            {
                                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                                "equals": "[parameters('publisher')]"
                            },
                            {
                                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                                "equals": "[parameters('type')]"
                            }
                        ]
                    }
                }
            }
        }
    }
}
```
可将 [Azure 门户](#deploy-with-the-portal)与 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 配合使用来部署此模板。

## <a name="deploy-with-the-portal"></a>使用门户进行部署

[![将策略示例部署到 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.cn/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Faudit-vm-extension%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = New-AzPolicyDefinition -Name "audit-vm-extension" -DisplayName "Audit if extension does not exist" -description "This policy audits if a required extension doesn't exist." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -publisher <Extension Publisher> -type <Extension Type> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清理 PowerShell 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli
az policy definition create --name 'audit-vm-extension' --display-name 'Audit if extension does not exist' --description 'This policy audits if a required extension does not exist.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-vm-extension"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例
