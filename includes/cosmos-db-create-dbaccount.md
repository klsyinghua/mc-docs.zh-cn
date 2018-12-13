---
title: include 文件
description: include 文件
services: cosmos-db
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
origin.date: 04/13/2018
ms.date: 12/03/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 018d672d3251bf99ad4eff1fc92963daae11b1db
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52677105"
---
1. 在新浏览器窗口中，登录到 [Azure 门户](https://portal.azure.cn/)。
2. 单击“创建资源” > “数据库” > “Azure Cosmos DB”。

   ![Azure 门户“数据库”窗格](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. 在“新建帐户”页上，输入新 Azure Cosmos DB 帐户的设置。 

    设置|值|说明
    ---|---|---
    ID|*输入唯一名称*|输入标识此 Azure Cosmos DB 帐户的唯一名称。 由于 documents.azure.cn 字符串将追加到所提供的 ID 后面以创建 URI，因此，请使用唯一的 ID。<br><br>ID 只能包含小写字母、数字和连字符 (-) 字符，并且必须包含 3 到 50 个字符。
    API|SQL|API 确定要创建的帐户的类型。 Azure Cosmos DB 提供两种 API：SQL（文档数据库）和 MongoDB（文档数据库）。 每种 API 当前均需要用户创建单独的帐户。<br><br>之所以选择 **SQL** 是因为本文将使用 SQL 语法创建文档数据库和查询。 <br><br>[详细了解 SQL API](../articles/cosmos-db/documentdb-introduction.md)|
    订阅|*订阅*|选择要用于此 Azure Cosmos DB 帐户的 Azure 订阅。 
    资源组|新建<br><br>然后输入上面在 ID 中提供的同一唯一名称|选择“新建”，然后输入帐户的新资源组名称。 为简单起见，可以使用与 ID 相同的名称。 
    位置|*选择离用户最近的区域*|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。
    启用异地冗余| 留空 | 这将在第二个（配对）区域中创建数据库的复制版本。 将此项留空。  
    
    <!-- Not Available on Line 24  Gremlin (graph database) and Cassandra, Table --> 然后单击“创建”。

    ![Azure Cosmos DB 的“新建帐户”页](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. 创建帐户需要几分钟时间。 等待门户中显示“祝贺你!已创建 Azure Cosmos DB 帐户”页。

    ![Azure 门户“通知”窗格](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)
    
    <!--Update_Description: wording update, update link-->