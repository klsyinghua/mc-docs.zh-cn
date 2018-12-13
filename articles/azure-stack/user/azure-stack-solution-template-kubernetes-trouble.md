---
title: 排查部署到 Kubernetes (K8) 以及向 Azure Stack 部署 Kubernetes (K8) 的问题 |Microsoft Docs
description: 了解如何排查部署到 Kubernetes (K8) 以及向 Azure Stack 部署 Kubernetes (K8) 的问题。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/11/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: waltero
ms.openlocfilehash: a1647df07b75591b606f30c23921f94d6c4dc1ce
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52656748"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>排查部署到 Kubernetes (K8) 以及向 Azure Stack 部署 Kubernetes (K8) 的问题

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。

以下文章讨论了如何排查 Kubernetes 群集问题。 可以按部署所需的元素查看部署警报以及部署的状态。 可能需要从 Azure Stack 或托管 Kubernetes 的 Linux VM 收集部署日志。 此外，可能还需要与 Azure Stack 管理员联系，以从管理终结点检索日志。

## <a name="overview-of-deployment"></a>部署概述

着手执行排查群集问题的步骤之前，可能需要查看 Azure Stack Kubernetes 群集部署过程。 该部署使用 Azure 资源管理器解决方案模板来为群集创建 VM 并安装 ACS 引擎。

### <a name="deployment-workflow"></a>部署工作流

下图显示了用于部署群集的一般过程。

![部署 Kubernetes 过程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 从市场项收集输入参数。

    输入设置 Kubernetes 群集所需的值，包括：
    -  **用户名** 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。
    -  **SSH 公钥** 用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权的密钥
    -  **服务主体** 供 Kubernetes Azure 云提供商使用的 ID。 创建服务主体时标识为应用程序 ID 的客户端 ID。 
    -  **客户端机密** 创建服务主体时创建的密钥。

2. 创建部署 VM 和自定义脚本扩展。
    -  使用市场 Linux 映像 **Ubuntu Server 16.04 LTS** 创建部署 Linux VM。
    -  从市场下载客户脚本扩展并执行该扩展。 该脚本是“用于 Linux 2.0 的自定义脚本”。
    -  运行 DVM 自定义脚本。 脚本：
        1. 从 Azure 资源管理器元数据终结点获取库终结点。
        2. 从 Azure 资源管理器元数据终结点获取 Active Directory 资源 ID。
        3. 为 ACS 引擎加载 API 模型。
        4. 将 ACS 引擎部署到 Kubernetes 群集，然后将 Azure Stack 云配置文件保存到 `/etc/kubernetes/azurestackcloud.json`。
3. 创建主 VM。

    下载客户脚本扩展并执行该扩展。

4. 运行主脚本。

    脚本：
    - 安装 etcd、Docker 和 Kubernetes 资源（如 kubelet）。 etcd 是一种分布式键值存储，用于跨一组计算机存储数据。 Docker 支持基本功能操作系统级别虚拟化（称为容器）。 Kubelet 是在每个 Kubernetes 节点上运行的节点代理。
    - 设置 etcd 服务。
    - 设置 Kubelet 服务。
    - 启动 kubelet。 这包括以下步骤：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
5. 创建代理 VM。

    下载客户脚本扩展并执行该扩展。

6. 运行代理脚本。 该代理自定义脚本：
    - 安装 etcd。
    - 设置 Kubelet 服务。
    - 加入 Kubernetes 群集。

## <a name="steps-for-troubleshooting"></a>故障排除步骤

可以在支持 Kubernetes 群集的 VM 上收集日志。 还可以查看部署日志。 可能还需要与 Azure Stack 管理员联系，以便验证你正在使用的 Azure Stack 版本并从与你的部署相关的 Azure Stack 获取日志。

1. 从 Kubernetes 群集中的主节点查看[部署状态](#review-deployment-status)并[检索日志](#get-logs-from-a-vm)。
2. 需要使用 Azure Stack 的最新版本。 如果不确定你的 Azure Stack 版本是否为最新版本，请联系 Azure Stack 管理员。 Kubernetes 群集市场项 0.3.0 需要 Azure Stack 版本 1808 或更高版本。
3.  查看 VM 创建文件。 你可能遇到了以下问题：  
    - 公钥可能无效。 查看已创建的密钥。  
    - 创建 VM 可能触发了内部错误或触发了创建错误。 许多因素（包括 Azure Stack 订阅的容量限制）都可能导致错误。
    - VM 的完全限定的域名 (FDQN) 是否以重复的前缀开头？
4.  如果 VM **正常**，请评估 DVM。 如果 DVM 有一条错误消息：

    - 公钥可能无效。 查看已创建的密钥。  
     - 你需要与 Azure Stack 管理员联系，以使用特权终结点检索 Azure Stack 的日志。 有关详细信息，请参阅 [Azure Stack 诊断工具](/azure-stack/azure-stack-diagnostics)。
5. 如果对你的部署有疑问，可以在 [Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/zh-CN/home)中发布问题或查看是否已经有人已回答了该问题。 

## <a name="review-deployment-status"></a>查看部署状态

可在部署 Kubernetes 群集时查看部署状态，以便调查任何问题。

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。
2. 选择“资源组”，然后选择在部署 Kubernetes 群集时使用的资源组名称。
3. 依次选择“部署”、“部署名称”。

    ![故障排除](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  查看故障排除窗口。 每个已部署的资源都提供以下信息。
    
    | 属性 | 说明 |
    | ----     | ----        |
    | 资源 | 资源的名称。 |
    | 类型 | 资源提供程序和资源的类型。 |
    | 状态 | 项状态。 |
    | 时间戳 | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，例如操作中涉及的资源提供程序、资源终结点和资源名称。 |

    每个项都将有绿色或红色的状态图标。

## <a name="get-logs-from-a-vm"></a>从 VM 获取日志

你将需要连接到群集的主 VM，打开 bash 提示符，并运行脚本来生成日志。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 

### <a name="prerequisites"></a>先决条件

用于管理 Azure Stack 的计算机上需要有 bash 提示符。 使用 bash 可运行访问日志的脚本。 在 Windows 计算机上，可以使用随 Git 一起安装的 bash 提示符。 若要获取最新版本的 Git，请参阅 [Git 下载](https://git-scm.com/downloads)。

### <a name="get-logs"></a>获取日志

1. 打开 bash 提示符。 如果是在 Windows 计算机上使用 Git，可以从以下路径打开 bash 提示符：`c:\programfiles\git\bin\bash.exe`。
2. 运行以下 bash 命令：

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > 在 Windows 上不需要运行 `sudo`，只需使用 `chmod 744 getkuberneteslogs.sh`。

3. 在同一会话中，使用已更新为与你的环境匹配的参数运行以下命令。

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    检查参数并基于你的环境设置值。
    | 参数           | 说明                                                                                                      | 示例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i、--identity-file | 用于连接 kubernetes 主 VM 的 RSA 私钥文件。 该密钥必须以 `-----BEGIN RSA PRIVATE KEY-----` 开头 | C:\data\privatekey.pem                                                        |
    | -h、--host          | Kubernetes 群集主 VM 的公共 IP 或完全限定的域名 (FQDN)。 该 VM 名称以 `k8s-master-` 开头。                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u、--user          | Kubernetes 群集主 VM 的用户名。 在配置市场项时设置此名称。                                                                    | azureuser                                                                     |
    | -d、--vmdhost       | DVM 的公共 IP 或 FQDN。 该 VM 名称以 `vmd-` 开头。                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   当添加参数值时，它可能如下所示：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    如果运行成功，则会创建日志。

    ![生成的日志](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. 检索由该命令创建的文件夹中的日志。 该命令将创建一个新文件夹并为其加盖时间戳。
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)。

[向市场添加 Kubernetes（适用于 Azure Stack 操作员）](..\azure-stack-solution-template-kubernetes-cluster-add.md)
