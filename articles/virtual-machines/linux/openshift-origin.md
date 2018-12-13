---
title: 在 Azure 中部署 OpenShift Origin | Azure
description: 在 Azure 中部署 OpenShift Origin。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/08/2018
ms.openlocfilehash: 3e0a3c195fc0c0859b0cdf274137e1a32c0cb8bb
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52664363"
---
# <a name="deploy-openshift-origin-in-azure"></a>在 Azure 中部署 OpenShift Origin

可使用两种方式之一在 Azure 中部署 OpenShift Origin：

- 可以手动部署所有必要的 Azure 基础结构组件，然后按照 OpenShift Origin [文档](https://docs.openshift.org/3.6/welcome/index.html)进行操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-origin)，该模板可简化 OpenShift Origin 群集的部署。

## <a name="deploy-by-using-the-openshift-origin-template"></a>使用 OpenShift Origin 模板进行部署

对 `aadClientId` 参数使用前面创建的服务主体中的 `appId` 值。

以下示例使用所有必需的输入创建一个名为 azuredeploy.parameters.json 的参数文件。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>使用 Azure CLI 进行部署

> [!NOTE] 
> 以下命令需要 Azure CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI 2.0](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

以下示例使用名为 myOpenShiftCluster 的部署将 OpenShift 群集和所有相关资源部署到名为 myResourceGroup 的资源组。 它使用名为 azuredeploy.parameters.json 的本地参数文件直接从 GitHub 存储库引用模板。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

该部署至少需要 25 分钟才能完成，具体取决于部署的节点总数。 部署完成后，终端中会列显 OpenShift 控制台的 URL，以及 OpenShift 主设备的 DNS 名称。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.chinacloudapi.cn:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.chinacloudapi.cn -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，可在浏览器中使用 `OpenShift Console Uri` 连接到OpenShift 控制台。 或者，可使用以下命令连接到 OpenShift 主设备：

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.chinacloudapi.cn
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#delete) 命令将其删除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-post-deployment.md)
- [OpenShift 部署故障排除](./openshift-troubleshooting.md)
- [OpenShift Origin 入门](https://docs.openshift.org/latest/getting_started/index.html)
<!--Update_Description: wording meta properties -->