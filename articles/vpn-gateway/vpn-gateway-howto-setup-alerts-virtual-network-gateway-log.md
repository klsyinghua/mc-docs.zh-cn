---
title: 针对来自 Azure VPN 网关的诊断日志事件设置警报
description: 针对 VPN 网关诊断日志事件配置警报的步骤
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 06/12/2019
ms.date: 08/12/2019
ms.author: v-jay
ms.openlocfilehash: 5bd201d38d89547a315f4f7edca33b41c12027ca
ms.sourcegitcommit: 193f49f19c361ac6f49c59045c34da5797ed60ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68732437"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>针对来自 VPN 网关的诊断日志事件设置警报

本文将帮助你使用 Azure Log Analytics 基于来自 Azure VPN 网关的诊断日志事件设置警报。 

Azure 中提供以下日志：

|***名称*** | ***说明*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含网关配置事件、主要更改和维护事件的诊断日志 |
|TunnelDiagnosticLog | 包含隧道状态更改事件。 在适用的情况下，隧道连接/断开连接事件将汇总有关状态更改的原因 |
|RouteDiagnosticLog | 记录网关上的静态路由和 BGP 事件发生的更改 |
|IKEDiagnosticLog | 记录网关上的 IKE 控制消息和事件 |
|P2SDiagnosticLog | 记录网关上的点到站点控制消息和事件 |

## <a name="setup"></a>设置警报

以下示例步骤针对涉及站点到站点 VPN 隧道的断开连接事件创建警报：


1. 在 Azure 门户中的“所有服务”下搜索 **Log Analytics**，然后选择“Log Analytics 工作区”。  

   ![转到 Log Analytics 工作区的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "创建")

2. 在“Log Analytics”页上选择“创建”。  

   ![包含“创建”按钮的“Log Analytics”页](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "选择")

3. 选择“新建”并填写详细信息。 

   ![用于创建 Log Analytics 工作区的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "选择")

4. 在“监视” > “诊断设置”边栏选项卡上找到你的 VPN 网关。  

   ![用于在“诊断设置”中查找 VPN 网关的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "选择")

5. 若要启用诊断，请双击该网关，然后选择“启用诊断”。 

   ![用于启用诊断的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "选择")

6. 填写详细信息，并确保选中“发送到 Log Analytics”和“TunnelDiagnosticLog”。   选择在步骤 3 中创建的 Log Analytics 工作区。

   ![选中的复选框](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "选择")

7. 转到虚拟网络网关资源的概述，从“监视”选项卡中选择“警报”。   然后创建新的警报规则，或者编辑现有的警报规则。

   ![用于创建新警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "选择")

   ![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "选择")
8. 选择 Log Analytics 工作区和资源。

   ![工作区和资源的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "选择")

9. 在“添加条件”下，选择“自定义日志搜索”作为信号逻辑。  

   ![自定义日志搜索的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "选择")

10. 在“搜索查询”文本框中输入以下查询。  适当替换 <> 中的值。

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    将阈值设置为 0，然后选择“完成”。 

    ![输入查询并选择阈值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "选择")

11. 在“创建规则”页上的“操作组”部分下，选择“新建”。    填写详细信息，然后选择“确定”。 

    ![新操作组的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "选择")

12. 在“创建规则”页上，填写“自定义操作”的详细信息，并确保“操作组名称”部分中显示的名称正确。    选择“创建警报规则”以创建规则。 

    ![用于创建规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "选择")

## <a name="next-steps"></a>后续步骤

若要针对隧道指标配置警报，请参阅[针对 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。