---
title: 如何在 Azure Stack 上备份存储帐户 | Microsoft Docs
description: 了解如何在 Azure Stack 上备份存储帐户。
services: azure-stack
author: WenJason
ms.service: azure-stack
ms.topic: how-to
origin.date: 10/19/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 0c6d8f05984455896fd03be40f526914c5a6073e
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020496"
---
# <a name="back-up-your-storage-accounts-on-azure-stack"></a>在 Azure Stack 上备份存储帐户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文探讨如何保护和恢复 Azure Stack 上 Azure 存储帐户中的存储帐户。

## <a name="elements-of-the-solution"></a>解决方案的要素

本部分探讨解决方案的整体结构和主要组成部分。

![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>应用层

可以发出多个 [PUT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob) 或 [PUT Block](https://docs.microsoft.com/rest/api/storageservices/put-block) 操作将对象写入到多个位置，以在不同 Azure Stack 缩放单元上的存储帐户之间复制数据。 或者，应用程序可以发出 [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作，以便在主要帐户的放置操作完成后，将 Blob 复制到托管在不同缩放单元上的存储帐户。

### <a name="scheduled-copy-task"></a>计划的复制任务

AzCopy 是一个极佳的工具，可用于复制本地文件系统、Azure 云存储、Azure Stack 存储和 s3 中的数据。 目前，AzCopy 无法在两个 Azure Stack 存储帐户之间复制数据。 将对象从源 Azure Stack 存储帐户复制到目标 Azure Stack 存储帐户需有一个中间本地文件系统。

有关详细信息，请参阅[在 Azure Stack 存储中使用数据传输工具](/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy)一文中的“AzCopy”。

### <a name="azure-stack-source"></a>Azure Stack（源）

这是要备份的存储帐户数据的源。

需要提供源存储帐户 URL 和 SAS 令牌。 有关如何使用存储帐户的说明，请参阅 [Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="azure-stack-target"></a>Azure Stack（目标）

这是用于存储所要备份帐户数据的目标。 目标 Azure Stack 实例必须与目标 Azure Stack 位于不同的位置。 源需要能够连接到目标。

需要提供源存储帐户 URL 和 SAS 令牌。 有关如何使用存储帐户的说明，请参阅 [Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="intermediary-local-filesystem"></a>中间本地文件系统

需要提供一个位置来运行 AzCopy，在从源复制并写入到目标 Azure Stack 时，还要使用此位置来存储数据。 这是源 Azure Stack 中的中间服务器。

可以创建 Linux 或 Windows 服务器作为中间服务器。 该服务器需有足够的空间，以便能够存储源存储帐户容器中的所有对象。
- 有关设置 Linux 服务器的说明，请参阅[使用 Azure Stack 门户创建 Linux 服务器 VM](azure-stack-quick-linux-portal.md)。  
- 有关设置 Windows 服务器的说明，请参阅[使用 Azure Stack 门户创建 Windows 服务器 VM](azure-stack-quick-windows-portal.md)。  

设置 Windows 服务器之后，需要安装 [Azure Stack PowerShell](/azure-stack/operator/azure-stack-powershell-install) 和 [Azure Stack 工具](/azure-stack/operator/azure-stack-powershell-download)。

## <a name="set-up-backup-for-storage-accounts"></a>为存储帐户设置备份

1. 检索源和目标存储帐户的 Blob 终结点。

    ![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. 创建并记下源和目标存储帐户的 SAS 令牌。

    ![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. 在中间服务器上安装 [AzCopy](https://github.com/Azure/azure-storage-azcopy)，并将“API 版本”设置为 Azure Stack 存储帐户的帐户。

    - 对于 Windows 服务器：

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - 对于 Linux (Ubuntu) 服务器：

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. 在中间服务器上创建脚本。 使用**存储帐户**、**SAS 密钥**和**本地目录路径**更新此命令。 稍后需要运行该脚本以增量方式从**源**存储帐户复制数据。

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  输入**存储帐户**、**SAS 密钥**和**本地目录路径。  稍后将使用此信息以增量方式将数据复制到**目标**存储帐户
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  使用 Cron 或 Windows 任务计划程序，来计划从源 Azure Stack 存储帐户复制到中间服务器上的本地存储。 然后，从中间服务器中的本地存储复制到目标 Azure Stack 存储帐户。

    使用此解决方案可以实现的 RPO 取决于 /MO 参数值，以及源帐户与中间服务器和中间服务器与目标帐户之间的网络带宽。

    - 对于 Linux (Ubuntu) 服务器：

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | 参数 | 注意 | 
    | ---- | ---- |
    | /SC | 使用分钟计划。 |
    | /MO | *XX* 分钟间隔。 |
    | /TN | 任务名称。 |
    | /TR | `script.bat` 文件的路径。 |


    - 对于 Windows 服务器：

    有关如何使用 Windows 任务计划的信息，请参阅[面向开发人员的任务计划程序](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page)
    

## <a name="use-your-storage-account-in-a-disaster"></a>发生灾难时使用存储帐户

每个 Azure Stack 存储帐户都有唯一的 DNS 名称，此名称派生自 Azure Stack 区域本身的名称，例如 `https://krsource.blob.east.asicdc.com/`。 如果在发生灾难期间需要使用目标帐户（例如 `https://krtarget.blob.west.asicdc.com/`），通过此 DNS 名称写入和读取数据的应用程序需要适应存储帐户的 DNS 名称更改。

在对帐户声明发生灾难之后可以修改应用程序连接字符串以便重新放置对象，或者，如果在对源和目标存储帐户进行前端处理的负载均衡器前面使用 CNAME 记录，则可以为负载均衡器配置手动故障转移算法，使管理员能够声明目标

如果是应用程序而不是 AAD 或 AD FS 在使用 SAS，则上述方法不适用，需要使用为目标存储帐户生成的目标存储帐户 URL 和 SAS 密钥来更新应用程序连接字符串。

## <a name="next-steps"></a>后续步骤

[Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)