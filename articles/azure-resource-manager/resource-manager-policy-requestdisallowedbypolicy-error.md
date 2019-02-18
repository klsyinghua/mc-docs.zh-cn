---
title: Azure 资源策略的 RequestDisallowedByPolicy 错误 | Microsoft Docs
description: 说明 RequestDisallowedByPolicy 错误的原因。
services: azure-resource-manager
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
origin.date: 10/31/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 2edf6094fa4118d20612b5576be6a598c119b99f
ms.sourcegitcommit: cdcb4c34aaae9b9d981dec534007121b860f0774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56306302"
---
<!--Verified Successfully-->
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 资源策略的 RequestDisallowedByPolicy 错误

本文说明了 RequestDisallowedByPolicy 错误的原因，它还提供了此错误的解决方案。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

部署过程中，可能会收到阻止创建资源的 RequestDisallowedByPolicy 错误。 以下示例显示错误：

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>故障排除

若要检索有关阻止部署的策略的详细信息，请使用以下方法之一：

### <a name="powershell"></a>PowerShell

在 PowerShell 中，提供该策略标识符作为 `Id` 参数，检索有关阻止部署的策略的详细信息。

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，提供策略定义的名称：

```azurecli
az policy definition list --query [*].name  #get all the name collection with Azure CLI
az policy definition show --name {region_Policy_Assignment}
```

## <a name="solution"></a>解决方案

为了安全性和符合性，订阅管理员可能会分配限制资源部署方式的策略。 例如，订阅可能具有阻止创建公共 IP 地址、网络安全组、用户定义的路由或路由表的策略。 “症状”部分中的错误消息显示策略的名称。
要解决此问题，请查看资源策略，并确定如何部署符合这些策略的资源。

有关详细信息，请参阅以下文章：

- [什么是 Azure Policy？](../azure-policy/azure-policy-introduction.md)
- [创建和管理策略以强制实施符合性](../azure-policy/create-manage-policy.md)

<!--Update_Description: new articles on resource manager policy requestdisallowedbypolicy error -->
<!--ms.date: 02/18/2019-->