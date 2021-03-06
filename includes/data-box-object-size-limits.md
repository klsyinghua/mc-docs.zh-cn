---
author: WenJason
ms.service: databox
ms.subservice: heavy
ms.topic: include
origin.date: 05/21/2019
ms.date: 06/10/2019
ms.author: v-jay
ms.openlocfilehash: c810a1f03e0260979f3b4a8c7652ba07b7db26f9
ms.sourcegitcommit: 67a78cae1f34c2d19ef3eeeff2717aa0f78de38e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726529"
---
下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 4.75 TiB                                                 |
| 页 blob         | 8 TiB <br> 以页 Blob 格式上传的文件都必须是 512 字节对齐（整数倍），否则上传失败。 <br> VHD 和 VHDX 为 512 字节对齐。 |
| Azure 文件        | 1 TiB                                                      |
| 托管磁盘     | 4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[标准 SSD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[高级 SSD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[标准 HDD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[托管磁盘的定价和计费](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
