---
title: 按需收集 Azure Stack 诊断日志 | Microsoft Docs
description: 了解如何使用“帮助和支持”或特权终结点 (PEP) 在 Azure Stack 中按需收集诊断日志。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/07/2019
ms.date: 01/13/2020
ms.author: v-jay
ms.reviewer: shisab
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: ae0463fb5bc562f424e2f52a0ee90aa68f5fe7a1
ms.sourcegitcommit: 166549d64bbe28b28819d6046c93ee041f1d3bd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737878"
---
# <a name="collect-azure-stack-diagnostic-logs-on-demand"></a>按需收集 Azure Stack 诊断日志

*适用于：Azure Stack 集成系统*

在故障排除过程中，Azure 客户支持服务 (CSS) 可能需要分析诊断日志。 从 1907 版开始，Azure Stack 操作员可以通过“帮助和支持”将诊断日志上传到 Azure 中的 Blob 容器。  相对于以前使用 PowerShell 的方法，建议使用“帮助和支持”  ，因为它更简单。 但是，如果门户不可用，操作员可以继续使用 **Get-AzureStackLog** 通过特权终结点 (PEP) 收集日志，就像在以前的版本中一样。 本主题介绍如何通过这两种方法按需收集诊断日志。

>[!Note]
>作为按需收集日志的备用方案，可以通过启用[诊断日志自动收集](azure-stack-configure-automatic-diagnostic-log-collection.md)功能来简化故障排除过程。 如果需要调查系统运行状况，请自动上传日志供 CSS 分析。 

## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>使用“帮助和支持”按需收集诊断日志

为了排查问题，CSS 可能会请求 Azure Stack 操作员根据需要收集上周特定时间范围的诊断日志。 在这种情况下，CSS 会为操作员提供一个 SAS URL 来上传收集的内容。 使用以下步骤，通过 CSS 提供的 SAS URL 配置按需日志收集：

1. 打开“帮助和支持概览”，单击“立即收集日志”。   
1. 从过去七天选择 1-4 小时滑动窗口。 
1. 选择本地时区。
1. 输入 CSS 提供的 SAS URL。

   ![按需日志收集的屏幕截图](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>如果启用了诊断日志自动收集，“帮助和支持”会显示何时在进行日志收集。  如果单击“立即收集日志”以收集特定时间的日志，而此时系统正在进行自动日志收集，则当自动日志收集完成后，按需收集就会开始。  

## <a name="use-the-privileged-endpoint-pep-to-collect-diagnostic-logs"></a>使用特权终结点 (PEP) 收集诊断日志

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->



### <a name="run-get-azurestacklog-on-azure-stack-integrated-systems"></a>在 Azure Stack 集成系统上运行 Get-AzureStackLog

若要在集成系统上运行 Get-AzureStackLog，需访问特权终结点 (PEP)。 下面是一个可以通过 PEP 来运行的示例脚本，用于在集成系统上收集日志：

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>在 Azure Stack 开发工具包 (ASDK) 系统上运行 Get-AzureStackLog

使用以下步骤在 ASDK 主机上运行 `Get-AzureStackLog`。

1. 以 **AzureStack\CloudAdmin** 身份登录到 ASDK 主机。
2. 以管理员身份打开一个新的 PowerShell 窗口。
3. 运行 **Get-AzureStackLog** PowerShell cmdlet。

#### <a name="examples"></a>示例

* 收集所有角色的所有日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出过去 8 小时的日志文件：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出 8 小时前到 2 小时前这个时间段的日志文件：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* 收集日志并将其存储在指定的 Azure 存储 blob 容器中。 此操作的常规语法如下所示：

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  例如：

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.chinacloudapi.cn/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > 此过程用于上传日志。 即使没有可以访问的 SMB 共享，或者无法访问 Internet，也可在 Azure Stack 上创建一个 Blob 存储帐户来传输日志，然后使用客户端检索这些日志。  

  若要为存储帐户生成 SAS 令牌，需要以下权限：

  * 对 Blob 存储服务的访问权限。
  * 对容器资源类型的访问权限。

  若要生成要用于 `-OutputSasUri` 参数的 SAS URI 值，请执行以下步骤：

  1. 按照[本文中](/storage/common/storage-quickstart-create-account)的步骤创建存储帐户。
  2. 打开 Azure 存储资源管理器的实例。
  3. 连接到在步骤 1 中创建的存储帐户。
  4. 在**存储服务**中导航到 **Blob 容器**。
  5. 选择“新建容器”  。
  6. 右键单击新容器，然后单击“获取共享访问签名”。 
  7. 根据需求，选择有效的**开始时间**和**结束时间**。
  8. 根据所需的权限，选择“读取”  、“写入”  和“列表”  。
  9. 选择“创建”  。
  10. 你将获得共享访问签名。 复制 URL 部分，并将其提供给 `-OutputSasUri` 参数。

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK 系统和集成系统的参数考虑事项

* 参数 **OutputSharePath** 和 **OutputShareCredential** 用于将日志存储在用户指定的位置。

* 可以使用 **FromDate** 和 **ToDate** 参数来收集特定时间段的日志。 如果未指定这些参数，则默认收集过去四小时的日志。

* 使用 **FilterByNode** 参数按计算机名筛选日志。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* 使用 **FilterByLogType** 参数按类型筛选日志。 可以选择按文件、共享或 WindowsEvent 进行筛选。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* 可以使用 **TimeOutInMinutes** 参数设置日志收集的超时。 它默认设置为 150（2.5 小时）。
* 转储文件日志收集默认情况下处于禁用状态。 若要启用它，请使用 **IncludeDumpFile** 开关参数。
* 目前，可以使用 **FilterByRole** 参数按以下角色筛选日志收集：

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |计算                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |域                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |存储                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |网关                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations-on-diagnostic-logs"></a>有关诊断日志的其他注意事项

* 此命令需要一些时间来运行，具体取决于日志收集的角色。 影响因素还包括指定用于日志收集的时限，以及 Azure Stack 环境中的节点数。
* 当日志收集运行时，请查看在 **OutputSharePath** 参数（在命令中指定）中创建的新文件夹。
* 每个角色的日志位于单个 zip 文件中。 根据所收集日志的大小，一个角色的日志可能会拆分成多个 zip 文件。 对于此类角色，如果需要将所有日志文件解压缩到单个文件夹中，请使用可以批量解压缩的工具。 选择角色的所有压缩文件，然后选择“解压缩到此处”。  该角色的所有日志文件会解压缩到单个合并的文件夹中。
* 在压缩的日志文件所在的文件夹中，还会创建名为 **Get-AzureStackLog_Output.log** 的文件。 此文件是一个命令输出日志，可以用来排查日志收集过程中的问题。 有时，日志文件包含 `PS>TerminatingError` 条目，除非运行日志收集后缺少预期的日志文件，否则可以放心忽略这些条目。
* 调查某个特定的故障时，可能需要多个组件中的日志。

  * 所有基础结构 VM 的系统和事件日志收集在 **VirtualMachines** 角色中。
  * 所有主机的系统和事件日志收集在 **BareMetal** 角色中。
  * 故障转移群集和 Hyper-V 事件日志收集在“存储”  角色中。
  * ACS 日志收集在“存储”角色  和 **ACS** 角色中。

> [!NOTE]
> 会对收集的日志强制实施大小和保留时间限制，因为必须确保对存储空间进行有效的利用，以免该空间充斥着日志。 但是，在诊断问题时，有时可能需要某些日志，但这些日志因为这些限制而不再存在了。 因此，**强烈建议**每隔 8 到 12 小时就将日志卸载到外部存储空间（Azure 中的存储帐户、其他本地存储设备，等等）并在该处保留 1 - 3 月，具体取决于你的要求。 还应确保该存储位置已加密。

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

可以使用 **Invoke-AzureStackOnDemandLog** cmdlet 为某些角色生成按需日志（请参阅本部分末尾的列表）。 默认情况下，执行 **Get-AzureStackLog** cmdlet 时收到的日志捆绑包中不存在此 cmdlet 生成的日志。 此外，建议你仅在 Azure 支持团队请求时才收集这些日志。

目前，可以使用 `-FilterByRole` 参数按以下角色筛选日志收集：

* OEM
* NC
* SLB
* 网关

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>收集按需诊断日志的示例

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>使用 PEP 收集诊断日志的工作原理

可以通过 Azure Stack 诊断工具轻松高效地进行日志收集。 下图显示了诊断工具的工作原理：

![Azure Stack 诊断工具工作流图](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>跟踪收集器

跟踪收集器默认启用，可以在后台持续运行，以便从 Azure Stack 组件服务收集所有 Windows 事件跟踪 (ETW) 日志。 ETW 日志存储在一个常用的本地共享中，其时间限制为五天。 一旦达到此限制，就会在创建新文件时删除最旧的文件。 每个文件默认允许的最大大小为 200 MB。 每 2 分钟进行一次大小检查，如果当前文件 >= 200 MB，则会保存该文件并生成新文件。 按事件会话生成的文件的总大小也存在 8 GB 的限制。

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

可以使用 PowerShell cmdlet Get-AzureStackLog 从 Azure Stack 环境中的所有组件收集日志。 此工具将日志以 zip 文件形式保存在用户定义的位置。 如果 Azure Stack 技术支持团队需要日志来排查问题，他们可能要求你运行 Get-AzureStackLog。

> [!CAUTION]
> 这些日志文件可能包含个人身份信息 (PII)。 在公开发布任何日志文件之前，请考虑到这一因素。

下面是一些收集的示例日志类型：

* **Azure Stack 部署日志**
* **Windows 事件日志**
* **Panther 日志**
* **群集日志**
* **存储诊断日志**
* **ETW 日志**

这些文件由跟踪收集器收集并保存在共享中。 然后，可以根据需要使用 Get-AzureStackLog 来收集它们。


