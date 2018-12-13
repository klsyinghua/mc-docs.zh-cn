---
title: Azure Stack Syslog 转发
description: 了解如何通过 Syslog 转发将 Azure Stack 与监视解决方案集成
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 08/14/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: ad31948691b4da3999fa4be9d54b60a936b8d320
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52660058"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Azure Stack 数据中心集成 - Syslog 转发

本文介绍如何使用 Syslog 将 Azure Stack 基础结构与已经部署在数据中心的外部安全解决方案集成。 例如，安全信息和事件管理 (SIEM) 系统。 Syslog 通道会公开由 Azure Stack 基础结构的所有组件提供的审核、警报和安全日志。 使用 Syslog 转发可以与安全监视解决方案集成，并且/或者可以检索所有审核、警报和安全日志，将其以存储方式保留。 

从 1805 更新开始，Azure Stack 有了一个集成的 Syslog 客户端，该客户端在配置后可以通过通用事件格式 (CEF) 的有效负载发出 Syslog 消息。 

> [!IMPORTANT] 
> Syslog 转发为预览版。 在生产环境中不应该依赖此功能。  

下图显示了参与 Syslog 集成的主要组件。

![Syslog 转发图](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>配置 Syslog 转发

Azure Stack 中的 Syslog 客户端支持以下配置：

1. **基于 TCP 的 Syslog，支持相互身份验证（客户端和服务器）和 TLS 1.2 加密：** 在此配置中，Syslog 服务器和 Syslog 客户端都可以通过证书验证彼此的标识。 消息通过 TLS 1.2 加密的通道发送。

2. **基于 TCP 的 Syslog，支持服务器身份验证和 TLS 1.2 加密：** 在此配置中，Syslog 客户端可以通过证书验证 Syslog 服务器的标识。 消息通过 TLS 1.2 加密的通道发送。

3. **基于 TCP 的 Syslog，不加密：** 在此配置中，Syslog 客户端和 Syslog 服务器均不验证彼此的标识。 消息通过 TCP 以明文形式发送。

4. **基于 UDP 的 Syslog，不加密：** 在此配置中，Syslog 客户端和 Syslog 服务器均不验证彼此的标识。 消息通过 UDP 以明文形式发送。

> [!IMPORTANT]
> 强烈建议将支持身份验证和加密（配置 1，或者至少为配置 2）的 TCP 用于生产环境，防范人为干预攻击和消息窃听。

### <a name="cmdlets-to-configure-syslog-forwarding"></a>用于配置 Syslog 转发的 cmdlet
配置 Syslog 转发需要访问特权终结点 (PEP)。 已将两个 PowerShell cmdlet 添加到 PEP 来配置 Syslog 转发：


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>cmdlet 参数

*Set-SyslogServer* cmdlet 的参数：

| 参数 | 说明 | 类型 | 必须 |
|---------|---------|---------|---------|
|*ServerName* | Syslog 服务器的 FQDN 或 IP 地址 | String | 是|
|*NoEncryption*| 强制客户端以明文形式发送 Syslog 消息 | 标志 | 否|
|*SkipCertificateCheck*| 在 TLS 初次握手期间跳过对 Syslog 服务器所提供证书的验证 | 标志 | 否|
|*SkipCNCheck*| 在 TLS 初次握手期间跳过对 Syslog 服务器所提供证书的“公用名称”值的验证 | 标志 | 否|
|*UseUDP*| 使用 Syslog 时将 UDP 用作传输协议 |标志 | 否|
|*Remove*| 从客户端删除服务器的配置并停止 Syslog 转发| 标志 | 否|

*Set-SyslogClient* cmdlet 的参数：
| 参数 | 说明 | 类型 |
|---------|---------| ---------|
| *pfxBinary* | pfx 文件，其中包含的证书可供客户端用作对 Syslog 服务器进行身份验证的标识  | Byte[] |
| *CertPassword* |  密码，用于导入与 pfx 文件关联的私钥 | SecureString |
|*RemoveCertificate* | 从客户端删除证书 | 标志|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>使用 TCP、相互身份验证和 TLS 1.2 加密配置 Syslog 转发

在此配置中，Azure Stack 中的 Syslog 客户端使用 TLS 1.2 加密将消息通过 TCP 转发到 Syslog 服务器。 在初次握手期间，客户端会验证服务器是否提供了有效且可信的证书；类似地，客户端也会向服务器提供证书来证明其身份。 此配置是最安全的，因为它对客户端和服务器的标识都进行了完整的验证，且消息也是通过加密的通道发送。 

> [!IMPORTANT]
> 强烈建议将此配置用于生产环境。 

若要使用 TCP、相互身份验证和 TLS 1.2 加密配置 Syslog 转发，请运行下述两个 cmdlet：

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

客户端证书的根必须与部署 Azure Stack 期间提供的根相同。 它还必须包含私钥。

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>使用 TCP、服务器身份验证和 TLS 1.2 加密配置 Syslog 转发

在此配置中，Azure Stack 中的 Syslog 客户端使用 TLS 1.2 加密将消息通过 TCP 转发到 Syslog 服务器。 在初次握手期间，客户端也会验证服务器是否提供了有效且可信的证书。 这样可以防止客户端将消息发送至不受信任的目标。
使用身份验证和加密的 TCP 是默认的配置，代表我们建议的用于生产环境的最低安全级别。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

如果需要使用自签名证书和/或不受信任的证书来测试 Syslog 服务器与 Azure Stack 客户端的集成，可以使用这些标记来跳过在初次握手期间由客户端执行的服务器验证。

```powershell
#Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck 
 
#Skip entirely the server certificate validation
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```

> [!IMPORTANT]
> 建议不要对生产环境使用 -SkipCertificateCheck 标记。 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>使用 TCP 在不加密的情况下配置 Syslog 转发

在此配置中，Azure Stack 中的 Syslog 客户端在不加密的情况下将消息通过 TCP 转发到 Syslog 服务器。 客户端既不验证服务器的标识，也不将自己的标识提供给服务器进行验证。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```

> [!IMPORTANT]
> 建议不要将此配置用于生产环境。 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>使用 UDP 在不加密的情况下配置 Syslog 转发

在此配置中，Azure Stack 中的 Syslog 客户端在不加密的情况下将消息通过 UDP 转发到 Syslog 服务器。 客户端既不验证服务器的标识，也不将自己的标识提供给服务器进行验证。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```

不加密的 UDP 是最容易配置的，但不能防范人为干预攻击和消息窃听。 

> [!IMPORTANT]
> 建议不要将此配置用于生产环境。 


## <a name="removing-syslog-forwarding-configuration"></a>删除 Syslog 转发配置

若要完全删除 Syslog 服务器配置并停止 Syslog 转发，请执行以下操作：

**从客户端删除 Syslog 服务器配置**

```PowerShell  
Set-SyslogServer -Remove
```

**从客户端删除客户端证书**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>验证 Syslog 设置

如果已成功地将 Syslog 客户端连接到 Syslog 服务器，则很快就可以开始接收事件。 如果看不到任何事件，请运行以下 cmdlet，对 Syslog 客户端的配置进行验证：

**验证 Syslog 客户端中的服务器配置**

```PowerShell  
Get-SyslogServer
```

**验证 Syslog 客户端中的证书设置**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog 消息架构

Azure Stack 基础结构的 Syslog 转发在发送消息时采用通用事件格式 (CEF)。
每条 Syslog 消息的结构基于以下架构： 

```Syslog
<Time> <Host> <CEF payload>
```

CEF 有效负载基于下面的结构，但每个字段的映射因消息类型（Windows 事件、创建的警报、关闭的警报）而异。

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>Windows 事件的 CEF 映射

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows 事件的严重性表：
| CEF 严重性值 | Windows 事件级别 | 数字值 |
|--------------------|---------------------| ----------------|
|0|Undefined|值：0。 指示所有级别的日志|
|10 个|关键|值：1。 指示严重警报的日志|
|8|错误| 值：2。 指示错误的日志|
|5|警告|值：3。 指示警告的日志|
|2|信息|值：4。 指示信息性消息的日志|
|0|详细|值：5。 指示所有级别的日志|

Azure Stack 中 Windows 事件的自定义扩展表：
| 自定义扩展名称 | Windows 事件示例 | 
|-----------------------|---------|
|MasChannel | 系统|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| 用户的组策略设置已成功处理。 自从上次成功处理组策略后，尚未检测到任何更改。|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |审核成功|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| 创建进程|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>已创建警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

警报严重性表：
| 严重性 | 级别 |
|----------|-------|
|0|Undefined|
|10 个|关键|
|5|警告|

Azure Stack 中已创建警报的自定义扩展表：
| 自定义扩展名称 | 示例 | 
|-----------------------|---------|
|MasEventDescription|说明：已为 \<TestDomain\> 创建用户帐户 \<TestUser\>。 它是潜在的安全风险。 -- 补救措施：联系支持部门。 解决此问题需要客户协助。 不要试图在没有他们协助的情况下解决此问题。 在提交支持请求之前，请根据 https://aka.ms/azurestacklogfiles 中的指南启动日志文件收集过程 |

### <a name="cef-mapping-for-alerts-closed"></a>已关闭警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

以下示例显示一条包含 CEF 有效负载的 Syslog 消息：
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)