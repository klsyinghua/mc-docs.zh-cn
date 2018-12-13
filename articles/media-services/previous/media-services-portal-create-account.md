---
title: 使用 Azure 门户创建 Azure 媒体服务帐户| Microsoft Docs
description: 本教程指导你完成利用 Azure 门户创建 Azure 媒体服务帐户的步骤。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 07/01/2018
ms.date: 10/01/2018
ms.author: v-jay
ms.openlocfilehash: fb846635c413e8d5e1c2a3fc3c09f0b2bf89cc98
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648881"
---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>利用 Azure 门户创建 Azure 媒体服务帐户
> [!div class="op_single_selector"]
> * [门户](media-services-portal-create-account.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> 若要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)。 
> 
> 

使用 Azure 门户，可以快速创建 Azure 媒体服务 (AMS) 帐户。 可以使用自己的帐户访问媒体服务，通过这些服务可以在 Azure 中存储、加密、编码、管理和流式传输媒体内容。 创建媒体服务帐户时，还会在此帐户所在的地理区域内创建一个关联的媒体服务帐户（或使用现有存储帐户）。 

可以将“常规用途 v1”或“常规用途 v2”作为主存储帐户。 目前，Azure 门户仅允许选择 v1，但在使用 API 或 Powershell 创建帐户时，可以添加 v2。 有关存储类型的详细信息，请参阅[关于 Azure 存储帐户](/storage/common/storage-create-storage-account)。

本文介绍一些常见概念，并展示如何使用 Azure 门户创建媒体服务帐户。

> [!NOTE]
> 若要了解 Azure 媒体服务功能在不同区域的可用性，请参阅 [AMS 功能的跨数据中心可用性](scenarios-and-availability.md#availability)。

## <a name="concepts"></a>概念
访问媒体服务需要两个关联的帐户：

* 一个媒体服务帐户。 可以使用帐户访问 Azure 中可用的一组基于云的媒体服务资源。 媒体服务帐户不会存储实际媒体内容， 而是存储有关你帐户中的媒体内容和媒体处理作业的元数据。 创建该帐户时，需要选择一个可用的媒体服务区域。 所选的区域是存储你帐户的元数据记录的数据中心。
  
* Azure 存储帐户。 存储帐户必须位于媒体服务帐户所在的地理区域内。 创建媒体服务帐户时，可以选择位于同一区域内的现有存储帐户，也可以在同一区域内创建新的存储帐户。 如果删除媒体服务帐户，相关存储帐户中的 Blob 不会被删除。

## <a name="create-an-ams-account"></a>创建 AMS 帐户
本部分中的步骤说明如何创建 AMS 帐户。

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 单击“+新建”> 搜索“Web + 移动” > “媒体服务”。
   
    ![媒体服务创建](./media/media-services-create-account/media-services-new1.png)
3. 在“创建媒体服务帐户”中输入所需的值。
   
    ![媒体服务创建](./media/media-services-create-account/media-services-new3.png)
   
   1. 在“帐户名”中，输入新的 AMS 帐户的名称。 媒体服务帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。
   2. 在“订阅”中，在有权访问的不同 Azure 订阅中进行选择。
   3. 在“资源组”中，选择新的或现有的资源。  资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../../azure-resource-manager/resource-group-overview.md#resource-groups)了解更多信息。
   4. 在“位置”中，选择用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式传输媒体。 下拉列表中仅显示可用的媒体服务区域。 
   5. 在“存储帐户”中，选择一个存储帐户以便为媒体服务帐户中的媒体内容提供 Blob 存储。 可选择媒体服务帐户所在的地理区域内的现有存储帐户，也可创建存储帐户。 将在同一区域内创建一个新的存储帐户。 适用于存储帐户名的规则对媒体服务帐户同样适用。
      
       单击[此处](../../storage/common/storage-introduction.md)了解有关存储的详细信息。
   6. 选择“固定到仪表板”以查看帐户部署进度。
4. 单击窗体底部的“创建”。
   
    成功创建帐户后，会加载概述页。 在流式处理终结点表中，帐户包含一个处于“已停止”状态的默认流式处理终结点。 

    >[!NOTE]
    >创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。  若要开始对内容进行流式处理并利用动态打包和动态加密功能，必须确保要从其流式获取内容的流式处理终结点处于“正在运行”状态。 
   
## <a name="to-manage-your-ams-account"></a>管理 AMS 帐户

若要管理 AMS 帐户（例如，以编程方式连接到 AMS API、上传视频、对资产进行编码、配置内容保护、监视作业进度），请在门户左侧选择“设置”。 从“设置”导航都某个可用的边栏选项卡（例如：“API 访问权限”、“资产”、“作业”、“内容保护”）。


## <a name="next-steps"></a>后续步骤

现在可以将文件上传到 AMS 帐户。 有关详细信息，请参阅[上传文件](media-services-portal-upload-files.md)。

如果计划以编程方式访问 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。

<!--Update_Description: add note for Media Services features availability-->