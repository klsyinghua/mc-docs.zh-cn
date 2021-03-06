---
title: 在设备上安装证书 - Azure IoT Edge | Microsoft Docs
description: 创建测试证书，并了解如何将其安装在 Azure IoT Edge 设备上以准备生产部署。
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 12/03/2019
ms.date: 01/20/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 023e421a78d9f80236a36a5cfc229cbeab2bcbc8
ms.sourcegitcommit: a890a9cca495d332c9f3f53ff3a5259fd5f0c275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75859792"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>在 IoT Edge 设备上安装生产证书

所有 IoT Edge 设备使用证书在运行时与设备上运行的任何模块之间创建安全连接。
充当网关的 IoT Edge 设备也使用相同的证书连接到其下游设备。 

首次安装 IoT Edge 和预配设备时，将在设备上设置临时证书，使你能够测试服务。
这些临时证书将在 90 天后过期，重启计算机可以重置这些证书。
准备好将设备转移到生产方案或者要创建网关方案时，需要提供自己的证书。
本文演示在 IoT Edge 设备上安装证书的步骤。

若要详细了解不同类型的证书及其在 IoT Edge 方案中的角色，请参阅[了解 Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

>[!NOTE]
>整篇文章中使用的术语“根 CA”是指 IoT 解决方案证书链的最顶层颁发机构公共证书。 不需要使用联合证书颁发机构的证书根，也不需要使用组织证书颁发机构的根。 在许多情况下，它实际上是中间 CA 公共证书。 

## <a name="prerequisites"></a>必备条件 

* [Windows](how-to-install-iot-edge-windows.md) 或 [Linux](how-to-install-iot-edge-linux.md) 上运行的 IoT Edge 设备。
* 有一个根证书颁发机构 (CA) 证书，该证书是自签名证书，或者从 Baltimore、Verisign、DigiCert 或 GlobalSign 等可信商业证书颁发机构购买的证书。

如果你没有根证书颁发机构，但想要试用需要生产证书的 IoT Edge 功能（例如网关方案），可以[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。

## <a name="create-production-certificates"></a>创建生产证书

应使用自己的证书颁发机构创建以下文件：
* 根 CA
* 设备 CA 证书
* 设备 CA 私钥

本文中所谓的“根 CA”并非组织的最顶层证书颁发机构。  它是 IoT Edge 方案的最顶层证书颁发机构，IoT Edge 中心模块、用户模块和任何下游设备使用该颁发机构来建立彼此之间的信任。 

若要查看这些证书的示例，请查看[管理用于示例和教程的测试 CA 证书](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)中用于创建演示证书的脚本。 

## <a name="install-certificates-on-the-device"></a>在设备上安装证书

在 IoT Edge 设备上安装证书链，并将 IoT Edge 运行时配置为引用新证书。 

例如，如果已使用示例脚本[创建演示证书](how-to-create-test-certificates.md)，则需要复制到 IoT Edge 设备的三个文件如下：

* 设备 CA 证书：`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 设备 CA 私钥：`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA：`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 将三个证书和密钥文件复制到 IoT Edge 设备。

   可以使用 [Azure Key Vault](/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。  如果在 IoT Edge 设备本身上生成了证书，则可以跳过此步骤，并使用工作目录的路径。

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows： `C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 将 config.yaml 文件中的 **certificate** 属性设置为 IoT Edge 设备上的证书和密钥文件的文件 URI。 删除 certificate 属性前面的 `#` 字符，以取消注释四个代码行。 请记住，yaml 中的缩进为两个空格。 例如：

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. 在 Linux 设备上，确保用户 **iotedge** 对保存证书的目录拥有读取权限。 

## <a name="next-steps"></a>后续步骤

在生产环境中部署解决方案之前，必须先在 IoT Edge 设备上安装证书。 详细了解如何[准备在生产环境中部署 IoT Edge 解决方案](production-checklist.md)。