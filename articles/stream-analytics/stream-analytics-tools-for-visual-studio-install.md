---
title: 安装适用于 Visual Studio 的 Azure 流分析工具
description: 本文介绍安装要求以及如何安装适用于 Visual Studio 的 Azure 流分析工具。
services: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 05/22/2018
ms.date: 11/26/2018
ms.openlocfilehash: 13524a0948f3a43316c46c5665a269374e145c10
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675234"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安装适用于 Visual Studio 的 Azure 流分析工具
Azure 流分析工具支持 Visual Studio 2017、2015 和 2013。 本文介绍如何安装和卸载工具。

有关如何使用工具的详细信息，请参阅[适用于 Visual Studio 的流分析工具](stream-analytics-quick-create-vs.md)。

## <a name="install"></a>安装
### <a name="visual-studio-2017"></a>Visual Studio 2017
* 下载 [Visual Studio 2017（15.3 或更高版本）](https://www.visualstudio.com/)。 支持 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支持 Express 版本。 
* 流分析工具是 Visual Studio 2017 中 **Azure 开发**以及**数据存储和处理**工作负载的一部分。 在 Visual Studio 安装过程中，启用这两个工作负载中的任何一个。

启用“数据存储和处理”工作负载，如下所示：

![选择“数据存储和处理”工作负载](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

启用“Azure 开发”工作负载，如下所示：

![选择“Azure 开发”工作负载](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* 在“工具”菜单中，选择“扩展和更新”。 在已安装的扩展中查找 Azure Data Lake 和流分析工具，然后单击“更新”以安装最新的扩展。 

![Visual Studio 扩展和更新](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
* 安装 Visual Studio 2015 或 Visual Studio 2013 Update 4。 支持 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支持 Express 版本。 
* 使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)安装用于 .NET 的 Azure SDK 2.7.1 或更高版本。
* 安装[适用于 Visual Studio 的 Azure 流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="update"></a>更新

### <a name="visual-studio-2017"></a>Visual Studio 2017
新的版本提醒显示在 Visual Studio 通知中。 

![Visual Studio 新版本提醒](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 和 Visual Studio 2015
已安装的适用于 Visual Studio 的流分析工具会自动检查是否有新版本。 按照弹出窗口中的说明安装最新版本。 

## <a name="uninstall"></a>卸载

### <a name="visual-studio-2017"></a>Visual Studio 2017
双击 Visual Studio 安装程序并选择“修改”。 从“数据存储和处理”工作负载或“Azure 开发”工作负载中取消选中“Azure Data Lake 和流分析工具”复选框。

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 和 Visual Studio 2015
转到控制面板，卸载“用于 Visual Studio 的 Azure Data Lake 和流分析工具”。

<!--Update_Description: update meta properties, wording update, update link -->