---
title: 用于容器的 Azure Monitor 常见问题解答 | Microsoft Docs
description: 用于容器的 Azure Monitor 是用于监视 Azure 中的 AKS群集和容器实例的运行状况的解决方案。 本文将解答一些常见问题。
ms.topic: conceptual
author: mgoedtel
ms.author: v-lingwu
origin.date: 10/15/2019
ms.date: 11/30/2019
ms.openlocfilehash: 169ef6f669712419c59d852f235d77cbc3b783f3
ms.sourcegitcommit: 13431cf4d69142ed7feb8d12d967a502bf9ff346
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599932"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答

本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>我看不到在查询 ContainerLog 表时填充的图像和名称属性值。

对于代理版本 ciprod12042019 和更高版本，默认情况下，不会为每个日志行填充这两个属性，这样是为了最大程度地减少收集日志数据时产生的成本。 有两个可用于查询表的选项，其中包含这些属性及其值：

### <a name="option-1"></a>选项 1 

联接其他表以在结果中包含这些属性值。

通过在 ContainerID 属性上进行联接，将查询修改为包含 ```ContainerInventory``` 表中的 Image 和 ImageTag 属性。 通过在 ContainerID 属性上进行联接，可以包含 KubepodInventory 表的 ContaineName 字段中的 Name 属性（与以前在 ```ContainerLog``` 表中显示的相同）。建议使用此选项。

下面的示例是一个示例详细查询，说明如何使用联接获取这些字段值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>方法 2

为每个容器日志行的这些属性重新启用收集。

如果第一个选项因涉及到查询更改而不方便，则可通过在代理配置映射中启用设置 ```log_collection_settings.enrich_container_logs``` 来重新启用收集这些字段的功能，如[数据收集配置设置](./container-insights-agent-config.md)中所述。

> [!NOTE]
> 对于包含 50 个以上节点的大型群集，不建议使用第二个选项，因为它将从群集中的每个节点生成 API 服务器调用以执行此扩充。 此选项还会增加收集的每个日志行的数据大小。

## <a name="can-i-view-metrics-collected-in-grafana"></a>能否查看在 Grafana 中收集的指标？

用于容器的 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的度量值。 我们提供了一个模板。你可以从 Grafana 的[仪表板存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下载该模板来开始操作，并可通过该模板来了解如何从受监视的群集查询更多数据，以便在自定义 Grafana 仪表板中进行可视化。 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以使用用于容器的 Azure Monitor 监视 AKS-engine 群集？

用于容器的 Azure Monitor 支持监视部署到 Azure 上托管的 AKS-engine（以前称为 ACS-engine）群集的容器工作负荷。 有关为此方案启用监视所需步骤的进一步详细信息和概述，请参阅[将用于容器的 Azure Monitor 用于 AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为何 Log Analytics 工作区中不显示数据？

如果在每天的某个时间，在 Log Analytics 工作区中看不到任何数据，则可能已达到 500 MB 的默认限制或为了控制每天要收集的数据量而指定的每日上限。 如果当天的限制已达到，则数据收集将停止，并且仅在第二天恢复。 若要查看数据使用情况，并根据预期的使用模式更新为不同的定价层，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定的容器状态是什么？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解决“缺少订阅注册”  错误？

如果收到“缺少 Microsoft.OperationsManagement 的订阅注册”  错误，则可以通过在定义了工作区的订阅中注册资源提供程序 **Microsoft.OperationsManagement** 来解决该错误。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支持启用了 RBAC 的 AKS 群集？

容器监视解决方案不支持 RBAC，但用于容器的 Azure Monitor 支持 RBAC。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[用于容器的 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 页。 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何将 omsagent 更新为最新发布的版本？

若要了解如何升级代理，请参阅[代理管理](container-insights-manage-agent.md)。

## <a name="how-do-i-enable-multi-line-logging"></a>如何启用多行日志记录？

当前，用于容器的 Azure Monitor 不支持多行日志记录，但有可用的变通方法。 你可以将所有服务配置为以 JSON 格式进行写入，然后 Docker/Moby 会在单个行上写入它们。

例如，可以将你的日志包装为一个 JSON 对象，如示例 node.js 应用程序的以下示例中所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

在你查询时，此数据将类似于用于日志的 Azure Monitor 中的以下示例：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细信息，请查看以下 [GitHub 链接](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>如何解决启用实时日志时遇到的 Azure AD 错误？ 

你可能会看到以下错误：**在请求中指定的回复 URL 与为应用程序“<应用程序 ID\>”配置的回复 URL 不匹配**。

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为何在载入后不能升级群集？

如果为 AKS 群集启用用于容器的 Azure Monitor 后，删除了该群集将其数据发送到的 Log Analytics 工作区，则尝试升级该群集时，该操作将会失败。 若要解决这一问题，必须禁用监视，然后重新启用该监视，同时引用订阅中的另一个有效工作区。 当你尝试再次升级群集时，该升级操作应进行处理并成功完成。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>需要为代理打开哪些端口和域，或将哪些端口和域加入允许列表？

有关 Azure 云、Azure 美国政府云和 Azure 中国云的容器化代理所需的代理和防火墙配置信息，请参阅[网络防火墙要求](container-insights-onboard.md#network-firewall-requirements)。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 
