---
title: 备份 Azure 导入/导出驱动器清单 | Azure
description: 了解如何自动备份 Azure 导入/导出服务的驱动器清单。
author: hayley244
manager: digimobile
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/23/2017
ms.date: 08/28/2017
ms.author: v-haiqya
ms.openlocfilehash: f895167ec40fda6ba75199b788389f21fef73dee
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52652335"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>为 Azure 导入/导出作业备份驱动器清单

通过在[放置作业](https://docs.microsoft.com/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)或[更新作业属性](https://docs.microsoft.com/rest/api/storageimportexport/jobs#Jobs_Update) REST API 操作中将 `BackupDriveManifest` 属性设置为 `true`，可以自动将驱动器清单备份到 blob。 默认情况下，不会备份驱动器清单。 驱动器清单备份以块 Blob 的形式存储在与作业关联的存储帐户中的某个容器内。 该容器的名称默认为 `waimportexport`，但可以在调用 `Put Job` 或 `Update Job Properties` 操作时在 `DiagnosticsPath` 属性中指定不同的名称。 备份清单 Blob 按以下格式命名： `waies/jobname_driveid_timestamp_manifest.xml`。

 可以调用 [获取作业](https://docs.microsoft.com/rest/api/storageimportexport/jobs#Jobs_Get) 操作来检索备份驱动器清单的 URI。 在每个驱动器的 `ManifestUri` 属性中会返回 Blob URI。

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
<!--Update_Description: update link-->