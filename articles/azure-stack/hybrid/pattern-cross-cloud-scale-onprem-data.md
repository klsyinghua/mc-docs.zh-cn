---
title: 在 Azure 和 Azure Stack Hub 中生成使用本地数据的、可跨云缩放的应用的模式。
description: 了解如何使用 Azure 和 Azure Stack Hub 生成使用本地数据的可跨云缩放的应用。
author: WenJason
ms.service: azure-stack
ms.topic: article
origin.date: 11/05/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: cb37e4bfe05bef533cb94a03721ac9534666d7d0
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020560"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>跨云缩放（本地数据）模式

了解如何生成跨 Azure 和 Azure Stack Hub 的混合应用程序。 此模式还演示如何使用单个本地数据源来实现合规性。

## <a name="context-and-problem"></a>上下文和问题

许多组织收集并存储大量的客户敏感数据。 企业规章或政府政策经常阻止它们将敏感数据存储在公有云中。 这些组织还希望利用公有云的可伸缩性。 公有云可以处理季节性的流量高峰，使客户能够在需要时将费用花费在他们真正需要的硬件上。

## <a name="solution"></a>解决方案

此解决方案利用私有云的合规性优势，并将这些优势与公有云的可伸缩性相结合。 Azure 和 Azure Stack Hub 混合云为开发人员提供一致的体验。 这种一致性使他们能够将其技能运用到公有云和本地环境。

可以参考解决方案部署指南将相同的 Web 应用程序部署到公有云和私有云。 还可以访问私有云中托管的、无法通过 Internet 路由的网络。 Web 应用程序的负载将受到监视。 当流量大幅增加时，某个程序会处理 DNS 记录，以将流量重定向到公有云。 当流量不再高发时，将会更新 DNS 记录，以将流量定向回到私有云。

[![使用本地数据模式进行跨云缩放](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 说明 |
|----------|-----------|-------------|
| Azure | Azure 应用服务 | [Azure 应用服务](/app-service/)可让你生成和托管 Web 应用、RESTful API 应用和 Azure Functions。 相关应用全都以所选的编程语言编写，无需管理基础结构。 |
| | Azure 虚拟网络| [Azure 虚拟网络 (VNet)](/virtual-network/virtual-networks-overview) 是 Azure 中专用网络的基本构建块。 VNet 允许多种类型的 Azure 资源（例如虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 该解决方案还演示了其他网络组件的用法：<br>- 应用程序和网关子网<br>- 本地网络网关<br>- 充当站点到站点 VPN 网关连接的虚拟网络网关<br>- 公共 IP 地址<br>- 点到站点 VPN 连接<br>- 用于托管 DNS 域和提供名称解析的 Azure DNS |
| | Azure 流量管理器 | [Azure 流量管理器](/traffic-manager/traffic-manager-overview)是基于 DNS 的流量负载均衡器。 使用它可以控制用户流量在不同数据中心内的服务终结点上的分布。 |
| | Azure Application Insights | [Application Insights](/azure-monitor/app/app-insights-overview) 是可扩展的应用程序性能管理服务，可让 Web 开发人员在多个平台上生成和管理应用。|
| | Azure Functions | [Azure Functions](/azure-functions/) 用于在无服务器环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 |
| | Azure 自动缩放 | [自动缩放](/azure-monitor/platform/autoscale-overview)是云服务、虚拟机和 Web 应用的内置功能。 当需求有变化时，应用程序可通过此功能来发挥最佳性能。 应用将会根据流量高峰进行调整，在指标有变化时发出通知，并可按需缩放。 |
| Azure Stack Hub | IaaS 计算 | Azure Stack Hub 可让你使用 Azure 支持的相同应用程序模型、自助门户和 API。 Azure Stack Hub IaaS 允许使用各种开源技术来实现一致的混合云部署。 例如，解决方案示例使用 Windows Server VM 来部署 SQL Server。|
| | Azure 应用服务 | 与 Azure Web 应用一样，该解决方案使用 [Azure Stack Hub 上的 Azure 应用服务](/azure-stack/operator/azure-stack-app-service-overview)来托管 Web 应用。 |
| | 网络 | Azure Stack Hub 虚拟网络的工作原理与 Azure 虚拟网络完全相同。 它使用许多相同的网络组件，包括自定义主机名。 
| Azure DevOps Services | 注册 | 快速设置用于生成、测试和部署的持续集成。 有关详细信息，请参阅[注册和登录到 Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)。 |
| | Azure Pipelines | 使用 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops) 实现持续集成/持续交付。 Azure Pipelines 可让你管理托管的生成和发布代理以及定义。 |
| | 代码存储库 | 利用多个代码存储库来简化开发管道。 使用 GitHub、Bitbucket、Dropbox、OneDrive 和 Azure Repos 中现有的代码存储库。 |

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此解决方案时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性 

Azure 和 Azure Stack Hub 的独特性适合用于支持当今全球分布式业务的需求。

**无障碍的混合云**

Microsoft 在统一的解决方案中为本地资产与 Azure Stack Hub 和 Azure 提供无可比拟的集成。 这种集成消除了管理多点解决方案和混合云提供程序的不便。 通过跨云缩放，按几下鼠标就能发挥 Azure 的威力。 出现云流量突发时，只需将 Azure Stack Hub 连接到 Azure，数据和应用程序即可按需在 Azure 中提供。

- 消除了构建和维护辅助灾难恢复站点的需求。
- 不再需要使用磁带备份，可在 Azure 中将备份数据存储长达 99 年，因而可以节省时间与成本。
- 将运行中的 Hyper-V、物理（预览版）和 VMware（预览版）工作负荷轻松迁移到 Azure，以利用云的经济效益和弹性。
- 针对 Azure 中本地资产的副本运行计算密集型报告或分析，且不影响生产工作负荷。
- 根据需要使用更大的计算模板将流量突发到云中，并在 Azure 中运行本地工作负荷。 混合解决方案可在需要时提供所需的强大功能。
- 只需按几下鼠标，即可在 Azure 中创建多层开发环境 - 甚至可将实时生产数据复制到开发/测试环境，使数据保持近实时的同步。

**通过 Azure Stack Hub 进行跨云缩放的经济效益**

云突发的主要优势是经济实惠。 只有在需要这些额外的资源时，才需要支付费用。 无需付费购买不必要的额外容量，也不必尝试预测需求高峰和波动。

**降低云中的高需求负载**

跨云缩放可用于分担处理负载。 通过将基本应用程序移到公有云来分散负载，为业务关键型应用程序释放本地资源。 应用程序可应用到私有云，仅在有必要满足需求时才突发到公有云。


### <a name="availability"></a>可用性

全局部署本身面临着挑战，例如不稳定的连接和不同区域的政府法规。 开发人员只能开发一个应用，然后根据不同的原因和要求部署该应用。  将应用程序部署到 Azure 公有云，然后在本地部署其他实例或组件。 可以使用 Azure 管理所有实例之间的流量。

### <a name="manageability"></a>可管理性

**单个一致的开发方法**

Azure 和 Azure Stack Hub 可让你在整个组织中使用一套一致的开发工具。 这种一致性可让你更轻松地实现持续集成和持续开发 (CI/CD)。 部署在 Azure 或 Azure Stack Hub 中的许多应用和服务可以互换，并可在任一位置无缝运行。

混合 CI/CD 管道有助于：
- 根据代码存储库中提交的代码启动新的生成。
- 自动将新生成的代码部署到 Azure 进行用户验收测试。
- 代码通过测试后，可自动部署到 Azure Stack Hub。

**单个一致的标识管理解决方案**

Azure Stack Hub 可与 Azure Active Directory 和 Active Directory 联合身份验证服务 (ADFS) 配合工作。 在联网场景中，Azure Stack Hub 可与 Azure Active Directory 配合工作。 对于未建立连接的环境，可以使用 ADFS 作为离线解决方案。 服务主体用于向应用程序授予访问权限，使其能够通过 Azure 资源管理器部署或配置资源。 

### <a name="security"></a>安全性

**确保合规性和数据主权**

Azure Stack Hub 可让你在多个国家/地区运行相同的服务，就像使用公有云一样。 在每个国家/地区的数据中心部署相同的应用程序可以符合数据主权要求。 此功能可确保个人数据保留在每个国家/地区境内。

**Azure Stack Hub - 安全态势**

没有稳定、持续的服务流程，就没有良好的安全态势。 出于此原因，Microsoft 投资购买了一个业务流程引擎，它可以在整个基础结构中无缝应用修补程序和更新。

由于与 Azure Stack Hub OEM 合作伙伴建立了合作关系，Microsoft 可将相同的安全态势延伸到 OEM 特定的组件。 例如，硬件生命周期主机及其上运行的软件。 这种合作关系可确保 Azure Stack Hub 在整个基础结构上具有一致且稳固的安全态势。 而客户又可以生成并保护其应用程序工作负荷。

**通过 PowerShell、CLI 和 Azure 门户使用服务主体**

若要为资源提供对脚本或应用的访问权限，请设置应用的标识，并使用应用自身的凭据进行身份验证。 此标识称作服务主体，可让你：

- 为应用标识分配不同于你自己的权限的权限，并将这些权限限制为用于解决该应用的确切需求。
- 执行无人参与的脚本时，使用证书进行身份验证。 

有关创建服务主体以及将证书用作凭据的详细信息，请参阅[使用应用标识访问资源](/operator/azure-stack-create-service-principals)。

## <a name="when-to-use-this-pattern"></a>何时使用此模式 

- 我的组织正在使用 DevOps 方法，或打算在不久的将来使用某种方法。
- 我想要跨 Azure Stack Hub 实施方案和公有云实施 CI/CD 做法。
- 我想要跨云和本地环境合并 CI/CD 管道。
- 我希望能够使用云或本地服务无缝开发应用程序。
- 我想要跨云和本地应用程序利用一致的开发人员技能。
- 我正在使用 Azure，但我的开发人员在本地 Azure Stack Hub 云中工作。
- 我的本地应用程序在季节性、周期性或无法预测的波动期间遇到需求高峰。
- 我希望使用云无缝缩放本地组件。
- 我想要实现云中可伸缩性，但又希望应用程序尽可能地在本地运行。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，其中详细介绍了最佳做法，并解答了其他问题。
- 此模式使用 Azure Stack 产品系列，包括 Azure Stack Hub。 请参阅 [Azure Stack 产品和解决方案系列](/azure-stack)详细了解产品和解决方案的整个阵容。

准备好测试解决方案示例时，请继续阅读[跨云缩放（本地数据）解决方案部署指南](solution-deployment-guide-cross-cloud-scaling-onprem-data.md)。 该部署指南逐步说明了如何部署和测试 Azure Stack 的组件。