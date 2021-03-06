---
title: 在逻辑应用中使用 SharePoint Server 连接器
description: 在逻辑应用中开始使用 SharePoint Server 连接器
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 08/18/2016
ms.author: v-yiso
ms.date: 04/23/2018
ms.openlocfilehash: 0590781c6f5e3ea828b631b595efe775ad8942f0
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52662838"
---
# <a name="get-started-with-the-sharepoint-connector"></a>SharePoint 连接器入门
SharePoint 连接器提供在 SharePoint 上处理列表的方法。

若要开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-sharepoint"></a>创建到 SharePoint 的连接
要使用 SharePoint 连接器，首先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必须 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 SharePoint 凭据 |

若要连接到 **SharePoint**，需要输入自己的标识（用户名和密码、智能卡凭据等）。 经过身份验证后，可以继续在逻辑应用中使用 SharePoint 连接器。 

逻辑应用的设计器打开时，按照以下步骤登录，并创建要在逻辑应用中使用的**连接**：

1. 在搜索框中输入“SharePoint”，并等待搜索返回在名称中带有 SharePoint 的所有项：   

   ![配置 SharePoint][1]  
   
2. 选择“SharePoint - 创建文件时”   
3. 选择“登录到 SharePoint”：   

   ![配置 SharePoint][2]    
   
4. 提供 SharePoint 凭据进行登录，以使用 SharePoint 进行身份验证
   
   ![配置 SharePoint][3]     
   
5. 完成身份验证后，将重定向到逻辑应用，通过配置 SharePoint 的“创建文件时”对话框完成它。          

   ![配置 SharePoint][4]  
   
6. 然后可以添加完成逻辑应用所需的其他触发器和操作。   
7. 通过选择菜单（在顶部）中的“保存”保存工作。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/sharepoint/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
