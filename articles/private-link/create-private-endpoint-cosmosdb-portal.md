---
title: 使用 Azure 专用链接连接到 Azure Cosmos 帐户
description: 了解如何通过创建专用终结点来安全地从 VM 访问 Azure Cosmos 帐户。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 11/04/2019
ms.date: 01/13/2020
ms.author: v-yeche
ms.openlocfilehash: 3d4a5b32e823306ad277f39f37371bccd47bd1f4
ms.sourcegitcommit: bc5f8b4f8ccd7c723f64055825508d1dfcc2162b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75859234"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>使用 Azure 专用链接以私密方式连接到 Azure Cosmos 帐户

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本文中，你将了解如何使用 Azure 门户在 Azure 虚拟网络中创建一个 VM，以及一个采用专用终结点的 Azure Cosmos 帐户。 然后，可以从该 VM 安全地访问该 Azure Cosmos 帐户。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.cn)。

## <a name="create-a-vm"></a>创建 VM

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源（在本例中为 Azure Cosmos 帐户）的 VM。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”    。

1. 在“创建虚拟网络”  中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | 名称 | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16  。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”    。 |
    | 位置 | 选择“chinaeast2”  。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24  。 |
    |||

1. 将其余的设置保留默认值，然后选择“创建”  。

### <a name="create-the-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。   

1. 在“创建虚拟机 - 基本信息”  中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 区域 | 选择“chinaeast2”  。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”  。 |
    | 映像 | 选择“Windows Server 2019 Datacenter”。  |
    | 大小 | 保留默认值“标准 DS1 v2”  。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入选择的密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认值“无”  。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”  。 |
    |||

1. 在完成时选择“下一步:**磁盘”** 。

1. 在“创建虚拟机 - 磁盘”中保留默认值，然后选择“下一步:  **网络”** 。

1. 在“创建虚拟机 - 基本信息”  中，选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”  。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。 |
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。 |
    | 公共 IP | 保留默认值“(new) myVm-ip”  。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。  |
    ||

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。

1. 看到“验证通过”消息时，选择“创建”   。

## <a name="create-an-azure-cosmos-account"></a>创建 Azure Cosmos 帐户

创建 [Azure Cosmos SQL API 帐户](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)。 为简单起见，可以在与其他资源相同的区域中（即“chinaeast2”）创建 Azure Cosmos 帐户。

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>为 Azure Cosmos 帐户创建专用终结点

如链接文章的“使用 Azure 门户创建专用链接”部分中所述，为 Azure Cosmos 帐户创建专用链接。

<!--Not Available on [Create a Private Link using the Azure portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)-->

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。  选择“连接”按钮后，“连接到虚拟机”随即打开   。

1. 选择“下载 RDP 文件”  。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 *.rdp* 文件。

    1. 出现提示时，选择“连接”  。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据   。

1. 选择“确定”  。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”   。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>从 VM 私密地访问 Azure Cosmos 帐户

在本部分中，你将使用专用终结点私密地连接到 Azure Cosmos 帐户。 

1. 若要包括 IP 地址和 DNS 映射，请登录到虚拟机 *myVM*，打开 `c:\Windows\System32\Drivers\etc\hosts` 文件，并采用以下格式包括上一步骤中的 DNS 信息：

    [专用 IP 地址] [帐户终结点].documents.azure.cn

    **示例：**

    10.1.255.13 mycosmosaccount.documents.azure.cn

    10.1.255.14 mycosmosaccount-chinaeast2.documents.azure.cn

1. 在  *myVM* 的远程桌面中，安装 [Azure 存储资源管理器](/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

1. 通过右键单击选择“Cosmos DB 帐户（预览版）”  。

1. 选择“连接到 Cosmos DB”  。

1. 选择“API”  。

1. 通过粘贴之前复制的信息来输入连接字符串。

1. 选择“**下一步**”。

1. 选择“连接”  。

1. 浏览 *mycosmosaccount* 中的 Azure Cosmos 数据库和容器。

1. （可选）向 *mycosmosaccount* 中添加新项。

1. 关闭与  *myVM* 建立的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

用完专用终结点、Azure Cosmos 帐户和 VM 之后，请删除资源组及其包含的所有资源： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。   **  

1. 选择“删除资源组”  。

1. 对于“键入资源组名称”，请输入  *myResourceGroup* ，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤

在本文中，你在虚拟网络上创建了一个 VM、一个 Azure Cosmos 帐户和一个专用终结点。 你从 Internet 连接到了该 VM，并使用专用链接安全地与 Azure Cosmos 帐户进行通信。

* 若要详细了解专用终结点，请参阅 [什么是 Azure 专用终结点？](private-endpoint-overview.md)。

    <!--Not Available on [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md)-->

<!-- Update_Description: new article about create private endpoint cosmosdb portal -->
<!--NEW.date: 01/06/2020-->