---
title: 将推送通知添加到 iOS
description: 了解如何使用 Azure 移动应用将推送通知发送到 iOS 应用。
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
origin.date: 06/25/2019
ms.date: 12/16/2019
ms.author: v-tawe
ms.openlocfilehash: f82c27ebefad3106845878a77e3b617520c6affb
ms.sourcegitcommit: d468b4e0c3288555b1ac5aec43c228328f49b60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "75600551"
---
# <a name="add-push-notifications-to-your-ios-app"></a>将推送通知添加到 iOS 应用

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视其应用的状态和使用情况，并使用**推送**服务与用户互动。 开发人员还可以利用 **Auth** 对用户进行身份验证，利用**数据**服务在云中持久保存和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述

本教程介绍如何向 [iOS 快速入门]项目添加推送通知，以便每次插入一条记录时，都向设备发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 指南。

[iOS 模拟器不支持推送通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。 用户需要 iOS 物理设备和 [Apple 开发人员计划成员身份](https://developer.apple.com/programs/ios/)。

## <a name="configure-hub"></a>配置通知中心

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>为推送通知注册应用

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>配置 Azure 以发送推送通知

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>更新后端以发送推送通知

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>将推送通知添加到应用

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>测试推送通知

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>其他信息

* 使用模板可以灵活地发送跨平台推送和本地化推送。 [如何使用适用于 Azure 移动应用的 iOS 客户端库](app-service-mobile-ios-how-to-use-client-library.md#templates)说明了如何注册模板。

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS 快速入门]: app-service-mobile-ios-get-started.md
