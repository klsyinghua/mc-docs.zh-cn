---
title: 如何为 Azure VPN 网关配置 OpenVPN 客户端 |Microsoft Docs
description: 为 Azure VPN 网关配置 OpenVPN 客户端的步骤
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 12/12/2019
ms.date: 02/10/2020
ms.author: v-jay
ms.openlocfilehash: 3728d65142d505cded79b0e7fdd4a20f1814b8bb
ms.sourcegitcommit: 925c2a0f6c9193c67046b0e67628d15eec5205c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068556"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>为 Azure VPN 网关配置 OpenVPN 客户端

本文帮助你配置 **OpenVPN ® 协议**客户端。

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

验证你已完成为 VPN 网关配置 OpenVPN 的步骤。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN](vpn-gateway-howto-openvpn.md)。

## <a name="windows"></a>Windows 客户端

1. 从官方 [OpenVPN 网站](https://openvpn.net/index.php/open-source/downloads.html)下载并安装 OpenVPN 客户端（版本 2.4 或更高版本）。
2. 下载网关的 VPN 配置文件。 可通过 Azure 门户中的“点到站点配置”选项卡或 PowerShell 中的“New-AzVpnClientConfiguration”来完成此操作。
3. 解压缩该配置文件。 接下来，使用记事本打开 OpenVPN 文件夹中的 *vpnconfig.ovpn* 配置文件。
4. [导出](vpn-gateway-certificates-point-to-site.md#clientexport)创建的 P2S 客户端证书，并将其上传到网关上的 P2S 配置。
5. 从 *.pfx* 中提取私钥和 base64 指纹。 有多种方法可执行此操作。 其中一种方法是在计算机上使用 OpenSSL。 *profileinfo.txt* 文件包含 CA 和客户端证书的私钥与指纹。 请务必使用客户端证书的指纹。

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. 在记事本中打开 *profileinfo.txt*。 若要获取客户端（子）证书的指纹，请选择并复制子证书的“-----BEGIN CERTIFICATE-----”与“-----END CERTIFICATE-----”之间的文本（包括这两行）。 查看 subject=/ 行可以识别子证书。
7. 切换到执行步骤 3 时在记事本中打开的 *vpnconfig.ovpn* 文件。 找到下面所示的节，并替换“cert”与“/cert”之间的所有内容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在记事本中打开 *profileinfo.txt*。 若要获取私钥，请选择并复制“-----BEGIN PRIVATE KEY-----”与“-----END PRIVATE KEY-----”之间的文本（包括这两行）。
9. 返回到记事本中的 vpnconfig.ovpn 文件，并找到此节。 粘贴私钥，替换“key”与“/key”之间的所有内容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
11. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
12. 右键单击系统托盘中的 OpenVPN 图标，然后单击“连接”。

## <a name="mac"></a>Mac 客户端

1. 下载并安装 OpenVPN 客户端，如 [TunnelBlick](https://tunnelblick.net/downloads.html)。 
2. 下载网关的 VPN 配置文件。 可通过 Azure 门户中的“点到站点配置”选项卡，或使用 PowerShell 中的“New-AzVpnClientConfiguration”来完成此操作。
3. 解压缩该配置文件。 在某个文本编辑器中打开 OpenVPN 文件夹中的 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以直接打开 .cer 文件并在证书标头之间复制 base64 密钥。 有关如何导出证书以获取已编码的公钥的详细信息，请参阅[导出公钥](vpn-gateway-certificates-point-to-site.md#cer)
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的详细信息，请参阅[导出私钥](https://openvpn.net/community-resources/how-to/#pki)。
6. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
7. 双击配置文件以在 Tunnelblick 中创建配置文件。
8. 启动应用程序文件夹中的 Tunnelblick。
9. 单击系统托盘中的 Tunnelblick 图标，然后单击“连接”。

> [!IMPORTANT]
>仅 iOS 11.0 及更高版本和 MacOS 10.13 及更高版本支持 OpenVPN 协议。
>
## <a name="iOS"></a>iOS 客户端

1. 从 App store 中安装 OpenVPN 客户端（版本 2.4 或更高版本）。
2. 下载网关的 VPN 配置文件。 可通过 Azure 门户中的“点到站点配置”选项卡，或使用 PowerShell 中的“New-AzVpnClientConfiguration”来完成此操作。
3. 解压缩该配置文件。 在某个文本编辑器中打开 OpenVPN 文件夹中的 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以直接打开 .cer 文件并在证书标头之间复制 base64 密钥。 有关如何导出证书以获取已编码的公钥的详细信息，请参阅[导出公钥](vpn-gateway-certificates-point-to-site.md#cer)
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的详细信息，请参阅[导出私钥](https://openvpn.net/community-resources/how-to/#pki)。
6. 不要更改任何其他字段。
7. 将配置文件 (.ovpn) 通过电子邮件发送到你的电子邮件帐户，该帐户是在 iPhone 上的邮件应用中配置的。 
8. 在 iPhone 上的邮件应用中打开电子邮件，并点击附加的文件

    ![打开电子邮件](./media/vpn-gateway-howto-openvpn-clients/ios2.png)

9. 如果没有看到“复制到 OpenVPN”  选项，请点击“更多”  。

    ![复制到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios3.png)

10. 点击“复制到 OpenVPN”  

    ![复制到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios4.png)

11. 在“导入配置文件”页面中点击“添加”  

    ![复制到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios5.png)

12. 在“导入的配置文件”页面中点击“添加”  

    ![复制到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios6.png)

13. 启动 OpenVPN 应用，并将“配置文件”  页面中的开关向右滑动以进行连接

    ![连接](./media/vpn-gateway-howto-openvpn-clients/ios8.png)


## <a name="linux"></a>Linux 客户端

1. 打开新的终端会话。 可以同时按“Ctrl + Alt + T”打开新会话。
2. 输入以下命令以安装所需的组件：

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. 下载网关的 VPN 配置文件。 可以通过 Azure 门户中的“点到站点配置”选项卡完成此操作。
4. [导出](/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport)创建的 P2S 客户端证书，并将其上传到网关上的 P2S 配置。 
5. 从 .pfx 中提取私钥和 base64 指纹。 有多种方法可执行此操作。 其中一种方法是在计算机上使用 OpenSSL。

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* 文件将包含 CA 和客户端证书的私钥与指纹。 请务必使用客户端证书的指纹。

6. 在文本编辑器中打开 *profileinfo.txt*。 若要获取客户端（子）证书的指纹，请选择并复制子证书的“-----BEGIN CERTIFICATE-----”与“-----END CERTIFICATE-----”之间的文本（包括这两行）。 查看 subject=/ 行可以识别子证书。

7. 打开 *vpnconfig.ovpn* 文件并找到下面所示的节。 替换“cert”与“/cert”之间的所有内容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在文本编辑器中打开 profileinfo.txt。 若要获取私钥，请选择并复制“-----BEGIN PRIVATE KEY-----”与“-----END PRIVATE KEY-----”之间的文本（包括这两行）。

9. 在文本编辑器中打开 vpnconfig.ovpn 文件，并找到此节。 粘贴私钥，替换“key”与“/key”之间的所有内容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
11. 若要使用命令行进行连接，请键入以下命令：
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. 要使用 GUI 进行连接，请转到系统设置。
13. 单击 + 添加新的 VPN 连接  。
14. 在“添加 VPN”下，选择“从文件导入...”  
15. 浏览到配置文件，然后双击或选择“打开”。 
16. 单击“添加 VPN”窗口上的“添加”   。
  
    ![从文件导入](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. 可以通过在“网络设置”页面上或在系统托盘中的网络图标下打开 VPN 进行连接   。

## <a name="next-steps"></a>后续步骤

如果希望 VPN 客户端能够访问另一个 VNet 中的资源，请遵照 [VNet 到 VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 文章中的说明设置 VNet 到 VNet 连接。 确保在网关和连接中启用 BGP，否则流量不会流动。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
