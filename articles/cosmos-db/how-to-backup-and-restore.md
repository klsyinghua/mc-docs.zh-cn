---
title: 如何从备份还原 Azure Cosmos DB 数据
description: 本文介绍如何从备份还原 Azure Cosmos DB 数据，如何联系 Azure 支持人员还原数据，以及数据恢复后要执行的步骤。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 11/15/2018
ms.date: 12/31/2018
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: b3d726f6269e295f3e2274ea9adc97f7eccadbd0
ms.sourcegitcommit: 54ddd3dc2452d7af3a6fa66dae908ad0c4ef99dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2018
ms.locfileid: "53814812"
---
# <a name="how-to-restore-azure-cosmos-db-data-from-a-backup"></a>如何从备份还原 Azure Cosmos DB 数据

如果意外删除了数据库或容器，可以[提交支持票证]( https://www.azure.cn/support/support-azure/)或[联系 Azure 支持]( https://www.azure.cn/support/contact/)，以便从自动联机备份中还原数据。 Azure 支持仅适用于选定计划（例如标准计划、开发人员计划以及更高级别的计划）。 不适用于基本计划。 若要了解不同的支持方案，请参阅 [Azure 支持计划](https://www.azure.cn/support/plans/)页。 

若要还原备份的特定快照，Azure Cosmos DB 要求在该快照的备份周期的持续时间内可用。

## <a name="request-a-restore"></a>请求还原

在请求还原之前，应该了解以下详细信息：

* 准备好订阅 ID。

* 应根据数据被意外删除或修改的方式，准备好提供其他信息。 建议提前准备可用的信息，从而尽量减少可能在某些有时效的情况下造成不良影响的来回传输。

* 如果删除了整个 Azure Cosmos DB 帐户，则需要提供删除的帐户的名称。 如果创建了同名的另一个帐户，请与支持团队共享该帐户，因为这有助于确定要选择的正确帐户。 建议为删除的每个帐户提交不同的支持票证，因为这可以最大限度地减少还原状态的混乱。

* 如果删除了一个或多个数据库，应提供 Azure Cosmos 帐户及 Azure Cosmos 数据库名，并指定是否存在同名的新数据库。

* 如果删除了一个或多个容器，应提供 Azure Cosmos 帐户名、数据库名和容器名。 并指定是否存在同名容器。

发生数据损坏时，如果容器中的文档遭到修改或删除，请尽快删除容器。 这样就可以避免 Azure Cosmos DB 覆盖备份。 如果因某种原因而无法删除，应尽快提交票证。 除了 Azure Cosmos 帐户名、数据库名、集合名以外，还应指定数据可以恢复到的时间点。 务必尽量精确，因为这有助于我们确定当时可用的最佳备份。 指定 UTC 时间也很重要。 

<!-- Not Available on Support Request-->

<!-- Not Available on ![Create a backup support request using Azure portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)-->

## <a name="post-restore-actions"></a>还原后的操作

还原数据后，你会收到有关新帐户名（通常采用 `<original-name>-restored1` 格式）和帐户要还原到的时间的通知。 还原的帐户与原始帐户具有相同的预配吞吐量、索引策略，并且二者位于同一区域。 角色为订阅管理员或共同管理员的用户可以看到还原的帐户。

还原数据后，应检查并验证还原的帐户中的数据，确保其中包含你需要的版本。 如果一切正常，应该使用 [Azure Cosmos DB 更改源](change-feed.md)将数据迁移回原始帐户。

<!-- Not Available on [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)-->

建议在迁移数据之后立即删除容器或数据库。 如果不删除已还原的数据库或容器，它们将在请求单位、存储和流出量方面产生成本。

## <a name="next-steps"></a>后续步骤

接下来，可以通过以下文章学习如何将数据迁移回原始帐户：

* 若要提出还原请求，请联系 Azure 支持，并[从 Azure 门户提交票证](https://www.azure.cn/support/support-azure/)
* [使用 Cosmos DB 更改源](change-feed.md)将数据移动到 Azure Cosmos DB。

<!--Not Available on [Use Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)-->
<!-- Update_Description: new articles on how to backup and restore -->
<!--ms.date: 12/31/2018-->