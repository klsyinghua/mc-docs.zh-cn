---
title: 在 Linux 上模拟 Azure IoT Edge | Microsoft Docs
description: 本快速入门介绍如何将预生成的代码远程部署到 IoT Edge 设备。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: dfbe931bbe5887e9c0545558c4d2b2565718dd0a
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52657579"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>快速入门：将第一个 IoT Edge 模块部署到 Linux x64 设备

Azure IoT Edge 使你可在设备上执行分析和数据处理，而无需推送所有数据到云。 IoT Edge 教程演示如何部署不同类型的模块，但是首先需要一个用于测试的设备。 

此快速入门介绍如何：

1. 创建 IoT 中心。
2. 将 IoT Edge 设备注册到 IoT 中心。
3. 启动 IoT Edge 运行时。
4. 以远程方式将模块部署到 IoT Edge 设备。

![教程体系结构][2]

本快速入门可以将 Linux 计算机或虚拟机变成 IoT Edge 设备。 然后即可将模块从 Azure 门户部署到设备。 在本快速入门中部署的模块为模拟传感器，可以生成温度、湿度和压强数据。 其他 Azure IoT Edge 教程均以本教程中通过部署模块（这些模块通过分析模拟数据来获得业务见解）执行的操作为基础。 

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户][lnk-account]。

## <a name="prerequisites"></a>先决条件

本快速入门使用 Linux 计算机作为 IoT Edge 设备。 如果没有用于测试的计算机，请按[在 Azure 门户中创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)中的说明操作。 
* 不需按步骤来安装并运行 Web 服务器。 连接到虚拟机以后，即可停止。  
* 在新的资源组中创建虚拟机。在为本快速入门创建其余的 Azure 资源时，可以使用该虚拟机。 为其提供可识别的名称，例如 *IoTEdgeResources*。 
* 不需要很大的虚拟机来测试 IoT Edge。 **B1ms** 之类的大小已足够。 

## <a name="create-an-iot-hub"></a>创建 IoT 中心

在 Azure 门户中创建 IoT 中心，启动快速入门。
![创建 IoT 中心][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用新创建的 IoT 中心注册 IoT Edge 设备。
![注册设备][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>安装和启动 IoT Edge 运行时

在设备上安装并启动 Azure IoT Edge 运行时。 
![注册设备][5]

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 每次某个 Edge 设备在启动后通过启动 IoT Edge 代理来启动此设备时，**IoT Edge 安全守护程序**就会启动。 **IoT Edge 代理**协助部署和监视 IoT Edge 设备（包括 IoT Edge 中心）的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。 

在为本快速入门准备的 Linux 计算机或 VM 中完成以下步骤。 

### <a name="register-your-device-to-use-the-software-repository"></a>注册设备，以便使用软件存储库

运行 IoT Edge 运行时所需的包在软件存储库中托管。 将 IoT Edge 设备配置为访问此存储库。 

此部分的步骤适用于运行 **Ubuntu 16.04** 的设备。 若要访问其他版本的 Linux 上的软件存储库，请参阅[在 Linux (x64) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)或[在 Linux (ARM32v7/armhf) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux-arm.md)。

1. 在要用作 IoT Edge 设备的计算机上，安装存储库配置。

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. 安装用于访问存储库的公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>安装容器运行时

IoT Edge 运行时是一组容器，而部署到 IoT Edge 设备的逻辑则以容器形式打包。 通过安装容器运行时，针对这些组件准备设备。

更新 **apt-get**。

   ```bash
   sudo apt-get update
   ```

安装容器运行时 **Moby**。

   ```bash
   sudo apt-get install moby-engine
   ```

安装 Moby 的 CLI 命令。 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>安装并配置 IoT Edge 安全守护程序

此安全守护程序作为系统服务安装，因此，每次设备启动时，IoT Edge 运行时就会启动。 安装还包括一个 **hsmlib** 版本，该版本允许安全守护程序与设备的硬件安全交互。 

1. 下载并安装 IoT Edge 安全守护程序。 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. 打开 IoT Edge 配置文件。 它是一个受保护的文件，因此可能需要使用提升的权限才能访问它。
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. 添加 IoT Edge 设备连接字符串。 找到变量 **device_connection_string**，将其值更新为注册设备后复制的字符串。

4. 保存并关闭该文件。 

   `CTRL + X`、`Y`、`Enter`

4. 重启 IoT Edge 安全守护程序。

   ```bash
   sudo systemctl restart iotedge
   ```

5. 查看 Edge 安全守护程序是否正作为系统服务运行。

   ```bash
   sudo systemctl status iotedge
   ```

   ![查看作为系统服务运行的 Edge 守护程序](./media/quickstart-linux/iotedged-running.png)

   也可通过运行以下命令来查看 Edge 安全守护程序提供的日志：

   ```bash
   journalctl -u iotedge
   ```

6. 查看在设备上运行的模块。 

   >[!TIP]
   >首先，需使用 *sudo* 运行 `iotedge` 命令。 注销计算机后重新登录以更新权限，然后即可在不提升特权的情况下运行 `iotedge` 命令。 

   ```bash
   sudo iotedge list
   ```

   ![查看设备上的一个模块](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>部署模块

从云端管理 Azure IoT Edge 设备，部署将遥测数据发送到 IoT 中心的模块。
![注册设备][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户推送 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。 这种情况下，推送的模块创建可用于本教程的环境数据。 

在运行模拟设备的计算机上再次打开命令提示符。 确认从云中部署的模块正在 IoT Edge 设备上运行：

   ```bash
   sudo iotedge list
   ```

   ![查看设备上的三个模块](./media/quickstart-linux/iotedge-list-2.png)

查看从 tempSensor 模块发送的消息：

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

先注销再登录以后，上述命令就不需要 *sudo*。

![查看模块中的数据](./media/quickstart-linux/iotedge-logs.png)

如果在日志中看到的最后一行是 `Using transport Mqtt_Tcp_Only`，则说明温度传感器模块可能正等着连接到 Edge 中心。 尝试终止该模块，然后让 Edge 代理重启它。 可以使用 `sudo docker stop tempSensor` 命令来终止它。

也可使用 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看设备正发送的遥测数据。 

## <a name="clean-up-resources"></a>清理资源

若要继续学习 IoT Edge 教程，可以使用在本快速入门中注册并设置的设备。 否则，可删除创建的 Azure 资源，并从设备中删除 IoT Edge 运行时。 

### <a name="delete-azure-resources"></a>删除 Azure 资源

如果是在新资源组中创建的虚拟机和 IoT 中心，则可删除该组以及所有关联的资源。 如果该资源组中有需要保留的内容，则请将要清除的资源逐一删除。 

若要删除资源组，请执行以下步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com)，然后单击“资源组”。
2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 
3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。
4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

### <a name="remove-the-iot-edge-runtime"></a>删除 IoT Edge 运行时

若要从设备中删除这些安装，请使用以下命令。  

删除 IoT Edge 运行时。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

删除 IoT Edge 运行时以后，已创建的容器会被停止，但仍存在于设备上。 查看所有容器。

   ```bash
   sudo docker ps -a
   ```

通过 IoT Edge 运行时删除在设备上创建的容器。 更改 tempSensor 容器的名称（如果使用了其他名称）。 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

删除容器运行时。

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了新的 IoT Edge 设备并使用 Azure IoT Edge 云接口将代码部署到该设备上。 现在，已有能生成其环境原始数据的模拟设备。 

本快速入门是所有 IoT Edge 教程的先决条件。 可以继续查看任何教程，了解如何通过 Azure IoT Edge 将这些数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 Azure Functions 筛选传感器数据](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 