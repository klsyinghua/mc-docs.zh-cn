---
title: 将一对多关系数据迁移到 Azure Cosmos DB SQL API 中
description: 了解如何处理将一对多关系数据迁移到 SQL API 的复杂方案
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 12/12/2019
ms.date: 02/10/2020
ms.author: v-yeche
ms.openlocfilehash: 1680164d23dfad589949ce0948baf7d3f29e7162
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028831"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>将一对多关系数据迁移到 Azure Cosmos DB SQL API 帐户中

若要从关系数据库迁移到 Azure Cosmos DB SQL API，可能需要更改数据模型以进行优化。

一种常见的转换方法是，通过将相关子项嵌入到一个 JSON 文档来反规范化数据。 本文探讨使用 Azure 数据工厂或 Azure Databricks 实现此目的的几个选项。 有关 Cosmos DB 的数据建模的一般指导，请查看 [Azure Cosmos DB 中的数据建模](modeling-data.md)。  

## <a name="example-scenario"></a>示例方案

假设 SQL 数据库中包含以下两个表：Orders 和 OrderDetails。

![订单详细信息](./media/migrate-relational-to-cosmos-sql-api/orders.png)

我们希望在迁移期间，将此一对多关系合并到一个 JSON 文档中。 为此，我们可以按如下所示，使用“FOR JSON”创建一个 T-SQL 查询：

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

此查询的结果如下所示： 

![订单详细信息](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)

理想情况下，你希望使用单个 Azure 数据工厂 (ADF) 复制活动来查询用作源的 SQL 数据，并将输出作为适当的 JSON 对象直接写入 Azure Cosmos DB 接收器。 目前，无法在一个复制活动中执行所需的 JSON 转换。 如果我们尝试将上述查询的结果复制到 Azure Cosmos DB SQL API 容器中，将会看到文档的字符串属性形式的 OrderDetails 字段，而不是预期的 JSON 数组。

可通过以下方式之一解决当前的这种限制：

* **使用包含两个复制活动的 Azure 数据工厂**： 
  1. 将 SQL 中的 JSON 格式的数据提取到位于中间 Blob 存储位置的某个文本文件，并 
  2. 将 JSON 文本文件中的数据加载到 Azure Cosmos DB 中的某个容器。

* **使用 Azure Databricks 从 SQL 中读取数据并将其写入 Azure Cosmos DB** - 我们将演示这两个选项。

让我们更详细地了解这些方法：

## <a name="azure-data-factory"></a>Azure 数据工厂

尽管我们无法将 OrderDetails 作为 JSON 数组嵌入到目标 Cosmos DB 文档中，但可以使用两个独立的复制活动来解决该问题。

### <a name="copy-activity-1-sqljsontoblobtext"></a>复制活动 #1：SqlJsonToBlobText

对于源数据，我们使用 SQL 查询通过 SQL Server OPENJSON 和 FOR JSON PATH 功能获取结果集，该结果集以单列的形式提供，每行包含一个 JSON 对象（表示订单）：

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf1.png)

对于 SqlJsonToBlobText 复制活动的接收器，我们选择“分隔文本”，并使用动态生成的唯一文件名（例如，'@concat(pipeline().RunId,'.json'）将其指向 Azure Blob 存储中的特定文件夹。
由于我们的文本文件实际上并不是“分隔的”，并且我们不希望使用逗号将其分析成单独的列，而是要保留双引号 (")，因此我们将“列分隔符”设置为制表符 ("\t") 或数据中未出现其他字符，并将“引号字符”设置为“无引号字符”。

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>复制活动 #2：BlobJsonToCosmos

接下来，我们修改 ADF 管道：添加第二个复制活动，用于在 Azure Blob 存储中查找第一个活动创建的文本文件。 第二个复制活动将结果作为“JSON”源进行处理，将文本文件中找到的每个 JSON 行作为一个文档插入到 Cosmos DB 接收器中。

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

（可选）我们还将一个“删除”活动添加到了管道，以便在每次运行之前删除 /Orders/ 文件夹中剩余的所有旧文件。 现在，我们的 ADF 管道如下所示：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

触发上述管道后，会看到中间 Azure Blob 存储位置创建了一个文件，其中的每行包含一个 JSON 对象：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

我们还会看到 Orders 文档，其中适当嵌入的 OrderDetails 已插入到 Cosmos DB 集合中：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf6.png)

<!--Not Available on ## Azure Databricks-->
<!--Not Available on ### Scala-->
<!--Not Available on ### Python-->

## <a name="next-steps"></a>后续步骤
* 了解 [Azure Cosmos DB 中的数据建模](/cosmos-db/modeling-data)
* 了解[如何在 Azure Cosmos DB 中为数据建模和分区](/cosmos-db/how-to-model-partition-example)

<!-- Update_Description: new article about migrate relational to cosmos db sql api -->
<!--NEW.date: 01/20/2020-->