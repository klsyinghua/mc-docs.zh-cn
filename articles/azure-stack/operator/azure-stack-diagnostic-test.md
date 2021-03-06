---
title: 使用 Azure Stack 验证工具验证系统状态 | Microsoft Docs
description: 了解如何使用 Azure Stack 验证工具验证系统状态。
services: azure-stack
author: WenJason
manager: digimobile
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 06/26/2019
ms.date: 01/13/2020
ms.author: v-jay
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: f006c104b103722b2bf8be6b5a584f1ec79dcee1
ms.sourcegitcommit: 166549d64bbe28b28819d6046c93ee041f1d3bd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737814"
---
# <a name="validate-azure-stack-system-state"></a>验证 Azure Stack 系统状态

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 操作员必须能够按需确定系统的运行状况和状态，这一点至关重要。 Azure Stack 验证工具 (**Test-AzureStack**) 是一个 PowerShell cmdlet，可让你在系统上运行一系列测试来识别故障（如果有）。 联系 Azure 客户服务支持 (CSS) 解决问题时，他们通常要求你通过[特权终结点 (PEP)](azure-stack-privileged-endpoint.md) 来运行此工具。 使用现有的系统范围运行状况和状态信息，CSS 可以收集和分析详细的日志，专注于发生错误的区域，并与你一起解决问题。

## <a name="running-the-validation-tool-and-accessing-results"></a>运行验证工具并访问结果

如上所述，验证工具通过 PEP 运行。 每项测试在 PowerShell 窗口中返回 **PASS/FAIL**（通过/失败）状态。 下面概述了端到端的验证测试过程：

1. 访问 PEP。 运行以下命令建立 PEP 会话：

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > 若要访问 Azure Stack 开发工具包 (ASDK) 主机上的 PEP，请使用 AzS-ERCS01 作为 -ComputerName。

2. 进入 PEP 后，运行：

   ```powershell
   Test-AzureStack
   ```

   有关详细信息，请参阅[参数注意事项](azure-stack-diagnostic-test.md#parameter-considerations)和[用例](azure-stack-diagnostic-test.md#use-case-examples)。

3. 如果有任何测试报告了“失败”  ，请运行 `Get-AzureStackLog`。 有关集成系统的说明，请参阅[在 Azure Stack 集成系统上运行 Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)；有关 ASDK 的说明，请参阅[在 ASDK 系统上运行 Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system)。

   该 cmdlet 收集 Test-AzureStack 生成的日志。 如果测试报告“WARN”（警告），建议你不要改为收集日志并联系 CSS。 

4. 如果 CSS 已指示你运行验证工具，CSS 代表会请求你提供收集的日志，以便继续排查问题。

## <a name="tests-available"></a>可用的测试

使用验证工具可以运行一系列的系统级测试和基本云方案，以洞察当前状态，并修复系统中的问题。

### <a name="cloud-infrastructure-tests"></a>云基础结构测试

这些低影响性测试在基础结构级别进行，提供有关各个系统组件和功能的信息。 目前，这些测试划分为以下类别：

| 测试类别                                        | -Include 和 -Ignore 的参数 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack ACS 摘要                              | AzsAcsSummary                     |
| Azure Stack Active Directory 摘要                 | AzsAdSummary                      |
| Azure Stack 警报摘要                            | AzsAlertSummary                   |
| Azure Stack 应用程序崩溃摘要                | AzsApplicationCrashSummary        |
| Azure Stack 备份共享可访问性摘要       | AzsBackupShareAccessibility       |
| Azure Stack BMC 摘要                              | AzsStampBMCSummary                |
| Azure Stack 云托管基础结构摘要     | AzsHostingInfraSummary            |
| Azure Stack 云托管基础结构利用率 | AzsHostingInfraUtilization        |
| Azure Stack 控制平面摘要                    | AzsControlPlane                   |
| Azure Stack Defender 摘要                         | AzsDefenderSummary                |
| Azure Stack 托管基础结构固件摘要  | AzsHostingInfraFWSummary          |
| Azure Stack 基础结构容量                  | AzsInfraCapacity                  |
| Azure Stack 基础结构性能               | AzsInfraPerformance               |
| Azure Stack 基础结构角色摘要              | AzsInfraRoleSummary               |
| Azure Stack 网络基础结构                            | AzsNetworkInfra                   |
| Azure Stack 门户和 API 摘要                   | AzsPortalAPISummary               |
| Azure Stack 缩放单元 VM 事件                     | AzsScaleUnitEvents                |
| Azure Stack 缩放单元 VM 资源                  | AzsScaleUnitResources             |
| Azure Stack 方案                                | AzsScenarios                      |
| Azure Stack SDN 验证摘要                   | AzsSDNValidation                  |
| Azure Stack Service Fabric 角色摘要              | AzsSFRoleSummary                  |
| Azure Stack 存储数据平面                       | AzsStorageDataPlane               |
| Azure Stack 存储服务摘要                 | AzsStorageSvcsSummary             |
| Azure Stack SQL 存储摘要                        | AzsStoreSummary                   |
| Azure Stack 更新摘要                           | AzsInfraUpdateSummary             |
| Azure Stack VM 位置摘要                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>云方案测试

除了上述基础结构测试以外，还可以运行云方案测试，以检查各基础结构组件的功能。 由于这些测试涉及到资源部署，因此需要云管理员凭据才能运行这些测试。

> [!NOTE]
> 目前不能使用 Active Directory 联合身份验证服务 (AD FS) 凭据运行云方案测试。

验证工具可测试以下云方案：
- 资源组创建
- 计划创建
- 套餐创建
- 存储帐户创建
- 虚拟机 (VM) 创建
- Blob 存储操作
- 队列存储操作
- 表存储操作

## <a name="parameter-considerations"></a>参数注意事项

- **List** 参数可用于显示所有可用的测试类别。

- **Include** 和 **Ignore** 参数可用于包含或排除测试类别。 有关这些参数的详细信息，请参阅以下部分。

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- 在云方案测试期间，会部署一个租户 VM。 可以使用 **DoNotDeployTenantVm** 来禁用此 VM 部署。

- 如[用例](azure-stack-diagnostic-test.md#use-case-examples)部分所述，需要提供 **ServiceAdminCredential** 参数才能运行云方案测试。

- 如[用例](azure-stack-diagnostic-test.md#use-case-examples)部分所述，在测试基础结构备份设置时，需使用 **BackupSharePath** 和 **BackupShareCredential**。

- **DetailedResults** 可用于获取每个测试以及整个运行的通过/失败/警告信息。 如果未指定此参数，未发生失败时，**Test-AzureStack** 将返回 **$true**，否则返回 **$false**。
- **TimeoutSeconds** 可用于设置每个组完成的特定时间。

- 验证工具还支持常用的 PowerShell 参数：Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer 和 OutVariable。 有关详细信息，请参阅[有关通用参数](https://go.microsoft.com/fwlink/?LinkID=113216)。  

## <a name="use-case-examples"></a>用例

### <a name="run-validation-without-cloud-scenarios"></a>在不测试云方案的情况下运行验证

在不使用 **ServiceAdminCredential** 参数的情况下运行验证工具可以跳过云方案测试： 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>在测试云方案的情况下运行验证

默认情况下，在验证工具中提供 **ServiceAdminCredentials** 参数会运行云方案测试： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

如果你只想运行云方案而不运行其余的测试，可以使用 **Include** 参数实现此目的： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

必须以 UPN 格式键入云管理员的用户名：serviceadmin@contoso.partner.onmschina.cn (Azure AD)。 出现提示时，键入云管理员帐户的密码。

### <a name="groups"></a>组

为了改善操作员体验，已启用 **Group** 参数以同时运行多个测试类别。 目前定义了三个组：**Default**、**UpdateReadiness** 和 **SecretRotationReadiness**。

- **默认**：被视为 **Test-AzureStack** 的标准运行。 如果未选择其他组，则默认会运行此组。
- **UpdateReadiness**：检查是否可以更新 Azure Stack 实例。 当 **UpdateReadiness** 组运行时，警告将作为错误显示在控制台输出中，应将其视为更新的阻碍。 从 Azure Stack 1910 版开始，以下类别属于 **UpdateReadiness** 组：

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness**：检查 Azure Stack 实例是否处于可以运行机密轮换的状态。 当 **SecretRotationReadiness** 组运行时，警告将作为错误显示在控制台输出中，应将其视为机密轮换的阻碍。 以下类别属于 SecretRotationReadiness 组：

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Group 参数示例

在使用 **Group** 安装更新或修补程序之前，以下示例会运行 **Test-AzureStack** 来测试系统就绪状态。 在开始安装更新或修补程序之前，请运行 **Test-AzureStack** 来检查 Azure Stack 的状态：

```powershell
Test-AzureStack -Group UpdateReadiness
```

如果 Azure Stack 运行 1811 之前的版本，请使用以下 PowerShell 命令来运行 **Test-AzureStack**：

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>运行验证工具以测试基础结构备份设置

在配置基础结构备份之前，可以使用 **AzsBackupShareAccessibility** 测试来测试备份共享路径和凭据。 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

配置备份之后，可以运行 **AzsBackupShareAccessibility** 来验证是否可以从 ERCS 访问共享： 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

若要使用已配置的备份共享测试新凭据，请运行： 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>运行验证工具以测试网络基础结构

此测试绕过 Azure Stack 软件定义网络 (SDN) 检查网络基础结构的连接。 它演示如何从公共 VIP 连接到配置的 DNS 转发器、NTP 服务器和身份验证终结点。 这包括使用 Azure AD 作为标识提供者时与 Azure 的连接，或者在使用 AD FS 作为标识提供者时与联合服务器的连接。

包括调试参数以获取命令的详细输出：

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Stack 诊断工具和问题日志记录，请参阅 [Azure Stack 诊断工具](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)。

若要了解有关故障排除的详细信息，请参阅 [Azure Stack 故障排除](azure-stack-troubleshooting.md)。
