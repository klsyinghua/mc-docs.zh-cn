---
title: 安装 Azure Analysis Services 的本地数据网关 | Azure
description: 了解如何安装和配置本地数据网关，以从 Azure Analysis Services 服务器连接到本地数据源。
author: rockboyfor
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 10/30/2019
ms.date: 11/25/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: 4c9c9df89b1e4e9ad54d5d161bf94fde83f9cc22
ms.sourcegitcommit: c5e012385df740bf4a326eaedabb987314c571a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74203632"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>安装并配置本地数据网关

同一区域中的一个或多个 Azure Analysis Services 服务器连接到本地数据源时，需要具备本地数据网关。  虽然你安装的网关与其他服务（例如 Power BI、Power Apps 和逻辑应用）使用的网关相同，但在针对 Azure Analysis Services 进行安装时，有一些需要完成的额外步骤。 本安装文章专门针对 **Azure Analysis Services**。

若要详细了解网关以及 Azure Analysis Services 如何使用网关，请参阅[连接达到本地数据源](analysis-services-gateway.md)。

## <a name="prerequisites"></a>先决条件

**最低要求：**

* .NET 4.5 Framework
* 64 位版本的 Windows 7/Windows Server 2008 R2（或更高版本）

**推荐：**

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

**重要注意事项：**

* 安装过程中，向 Azure 注册你的网关时，会选中你订阅的默认区域。 你也可以选择其他区域。 如果你在多个区域有服务器，则必须为每个区域都安装一个网关。 
* 不能在域控制器上安装网关。
* 一台计算机上只能安装一个网关。
* 在计算机处于开启但未处于休眠状态下安装网关。
* 不要在使用无线网络连接的计算机上安装网关。 否则，可能会降低性能。
* 安装网关时，你用来登录到计算机的用户帐户必须具有“作为服务登录”权限。 安装完成后，本地数据网关服务使用 NT SERVICE\PBIEgwService 帐户作为服务登录。 可以在安装期间指定一个不同的帐户，也可以在安装完成后在“服务”中指定一个不同的帐户。 请确保组策略设置同时允许你在安装时登录的帐户以及你选择的具有“作为服务登录”权限的服务帐户。
* 在 Azure AD 中使用与要在其中注册网关的订阅相同[租户](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant)的帐户登录到 Azure。 安装和注册网关时不支持 Azure B2B（来宾）帐户。
    
    <!--MOONCAKE: Available on [tenant](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant)-->
    
* 如果数据源位于 Azure 虚拟网络 (VNet) 上，则必须配置 [AlwaysUseGateway](analysis-services-vnet-gateway.md) 服务器属性。

    <!--MOONCAKE Not Available on * The (unified) gateway described here is not supported in Azure Germany regions. Instead, use **Dedicated On-premises gateway for Azure Analysis Services**, installed from your server's **Quick Start** in the portal.-->

<a name="download"></a>
## <a name="download"></a>下载

 [下载网关](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

<a name="install"></a>
## <a name="install"></a>安装

1. 运行安装程序。

2. 选择“本地数据网关”。 

    ![Select](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. 选择一个位置，接受条款，然后单击“安装”  。

    ![安装位置和许可条款](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. 登录 Azure。 该帐户必须位于你租户的 Azure Active Directory 中。 该帐户用于网关管理员。 安装和注册网关时不支持 Azure B2B（来宾）帐户。

    ![登录 Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

    > [!NOTE]
    > 如果使用域帐户登录，它将映射到你在 Azure AD 中的组织帐户。 你的组织帐户将用作网关管理员。

<a name="register"></a>
## <a name="register"></a>注册

若要在 Azure 中创建网关资源，则必须在网关云服务中注册你安装的本地实例。 

1. 选择“在此计算机上注册新网关”  。

    ![注册](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. 为你的网关键入名称和恢复密钥。 默认情况下，网关使用你订阅的默认区域。 如果你需要选择其他区域，请选择“更改区域”  。

    > [!IMPORTANT]
    > 将恢复密钥保存在安全位置。 接管、迁移或还原网关时需要使用恢复密钥。 

    ![注册](media/analysis-services-gateway-install/aas-gateway-register-name.png)

<a name="create-resource"></a>
## <a name="create-an-azure-gateway-resource"></a>创建 Azure 网关资源

安装并注册网关后，需在 Azure 订阅中创建网关资源。 使用注册网关时使用的帐户登录到 Azure。

1. 在 Azure 门户中单击“创建资源”，接着搜索“本地数据网关”，然后单击“创建”。   

    ![创建网关资源](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. 在“创建连接网关”中  ，输入以下设置：

    * **名称**：输入网关资源的名称。 

    * **订阅**：选择要与网关资源关联的 Azure 订阅。 

        默认订阅取决于用来登录的 Azure 帐户。

    * **资源组**：创建资源组，或选择现有资源组。

    * **位置**：选择网关的注册区域。

    * **安装名称**：如果尚未选择网关安装，请选择在计算机上安装并注册的网关。 

        完成后，单击“创建”。 

<a name="connect-servers"></a>
## <a name="connect-servers-to-the-gateway-resource"></a>将服务器连接到网关资源

1. 在 Azure Analysis Services 服务器概述中，单击“本地数据网关”。 

    ![将服务器连接到网关](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. 在“选取要连接的本地数据网关”  ，选择你的网关资源，然后单击“连接所选网关”  。

    ![将服务器连接到网关资源](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > 如果列表中不显示你的网关，很可能是你的服务器与你注册网关时指定的区域不在同一个区域。 

    在服务器和网关资源之间成功建立连接以后，状态会显示“已连接”。 

    ![将服务器连接到网关资源成功](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

就这么简单。 如果你需要打开端口或执行任何故障排除时，一定要签出[本地数据网关](analysis-services-gateway.md)。

## <a name="next-steps"></a>后续步骤

* [管理 Analysis Services](analysis-services-manage.md)   
* [从 Azure Analysis Services 获取数据](analysis-services-connect.md)   
* [对 Azure 虚拟网络上的数据源使用网关](analysis-services-vnet-gateway.md)

<!-- Update_Description: update meta properties, wording update-->
