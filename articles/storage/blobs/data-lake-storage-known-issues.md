---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题
author: WenJason
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
origin.date: 11/03/2019
ms.date: 02/10/2020
ms.author: v-jay
ms.reviewer: jamesbak
ms.openlocfilehash: 56d66e903d4f13f9b6ee3acbe6467c58f93b639e
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028519"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的功能与工具。

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>使用 Blob API 时的问题和限制

Blob API 和 Data Lake Storage Gen2 API 可以对相同的数据执行操作。

本部分介绍使用 Blob API 和 Data Lake Storage Gen2 API 对相同的数据执行操作时存在的问题和限制。

* 不能同时使用 Blob API 和 Data Lake Storage API 向文件的同一实例写入数据。 如果使用 Data Lake Storage Gen2 API 向某个文件写入数据，则在调用[获取块列表](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Blob API 时，该文件的块将不可见。 覆盖某个文件时，可以使用 Data Lake Storage Gen2 API 或 Blob API。 这不会影响文件属性。

* 如果在使用[列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作时不指定分隔符，则结果会包含目录和 Blob。 如果选择使用分隔符，请只使用正斜杠 (`/`)。 这是唯一支持的分隔符。

* 如果使用[删除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 来删除目录，则只能在该目录为空的情况下将其删除。 这意味着，不能使用 Blob API 以递归方式删除目录。

这些 Blob REST API 不受支持：

* [放置 Blob（页）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置页](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [获取页面范围](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [放置 Blob（追加）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [追加块](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [从 URL 追加块](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

在有分层命名空间的帐户中，非托管 VM 磁盘不受支持。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘放到未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library" />

## <a name="filesystem-support-in-sdks"></a>SDK 中的文件系统支持

- [.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md) 和 [Python](data-lake-storage-directory-file-acl-python.md) 支持以公共预览版提供。 目前不支持其他 SDK。
- 获取和设置 ACL 的操作当前不是递归的。

## <a name="filesystem-support-in-powershelli"></a>PowerShell 中的文件系统支持

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) 支持以公共预览版提供。
- 获取和设置 ACL 的操作当前不是递归的。

## <a name="support-for-other-blob-storage-features"></a>支持其他 Blob 存储功能

下表列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的所有其他功能与工具。

| 功能/工具    | 详细信息    |
|--------|-----------|
| **AzCopy** | 特定于版本的支持 <br><br>仅使用最新版本的 AzCopy ([AzCopy v10](/storage/common/storage-use-azcopy-v10?toc=%2fstorage%2ftables%2ftoc.json))。 不支持早期版本的 AzCopy，例如 AzCopy v8.1。|
| **Azure Blob 存储生命周期管理策略** | 支持生命周期管理策略（预览版）。  在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)注册获取生命周期管理策略和存档访问层的预览版。   <br><br>支持所有访问层。 存档访问层目前处于预览状态。 尚不支持删除 Blob 快照的功能。  当前有一些影响生命周期管理策略和存档访问层的 Bug。  |
| **Azure 存储资源管理器** | 特定于版本的支持。 <br><br>仅使用版本 `1.6.0` 或更高版本。 <br> 当前存在影响版本 `1.11.0` 的存储 Bug，在某些情况下可能会导致身份验证错误。 即将推出存储 bug 的修补程序，但我们建议你使用可供[免费下载](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)的版本 `1.10.x` 作为一种解决方法。 `1.10.x` 不受存储 Bug 的影响。|
| **Blob 容器 ACL** |尚不支持|
| **Blobfuse** |尚不支持|
| **自定义域** |尚不支持|
| **Azure 门户中的存储资源管理器** | 有限支持。 尚不支持 ACL。 |
| **诊断日志记录** |支持诊断日志（预览版）。 <br><br>Azure 存储资源管理器 1.10.x 不能用于查看诊断日志。 若要查看日志，请使用 AzCopy 或 SDK。
| **不可变存储** |尚不支持 <br><br>使用不可变存储可以 [WORM（一次写入，多次读取）](/storage/blobs/storage-blob-immutable-storage)状态存储数据。|
| **对象级层** |支持冷层和存档层。 存档层处于预览状态。 所有其他访问层目前均不受支持。 <br><br> 当前有一些影响存档访问层的 Bug。  在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)注册获取存档访问层的预览版。|
| **第三方应用程序** | 有限支持 <br><br>对于使用 REST API 保持正常运行的第三方应用程序，如果在 Data Lake Storage Gen2 中使用这些应用程序，则它们可继续正常运行。 <br>调用 Blob API 的应用程序可能会正常运行。|
|**软删除** |尚不支持|
| **版本控制功能** |尚不支持 <br><br>这包括[软删除](/storage/blobs/storage-blob-soft-delete)和其他版本控制功能，例如[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)。|


