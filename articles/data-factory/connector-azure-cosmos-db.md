---
title: 在 Azure Cosmos DB (SQL API) 中复制和转换数据
description: 了解如何使用数据工厂向/从 Azure Cosmos DB (SQL API) 复制数据，以及如何在 Azure Cosmos DB (SQL API) 中转换数据。
services: data-factory, cosmosdb
ms.author: v-jay
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
origin.date: 12/11/2019
ms.date: 01/06/2020
ms.openlocfilehash: ca09411ec8ae22539160a9362a725b60fbf7a135
ms.sourcegitcommit: 6a8bf63f55c925e0e735e830d67029743d2c7c0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75624244"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure Cosmos DB (SQL API) 中复制和转换数据

本文概述如何使用 Azure 数据工厂中的复制活动在 Azure Cosmos DB (SQL API) 中复制和粘贴数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

>[!NOTE]
>此连接器仅支持 Cosmos DB SQL API。 有关 MongoDB API，请参阅[适用于 MongoDB 的 Auzre Cosmos DB API 的连接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支持其他 API 类型。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Cosmos DB (SQL API) 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

对于复制活动，此 Azure Cosmos DB(SQL API) 连接器支持：

- 在 Azure Cosmos DB [SQL API](/cosmos-db/documentdb-introduction) 中复制和粘贴数据。
- 以 **insert** 或 **upsert** 的形式写入 Azure Cosmos DB。
- 按原样导入和导出 JSON 文档，或在表格数据集中复制或粘贴数据。 示例包括 SQL 数据库和 CSV 文件。 若要在 JSON 文件或另一个 Azure Cosmos DB 集合中按原样复制或粘贴文档，请参阅[导入和导出 JSON 文档](#import-and-export-json-documents)。

数据工厂与 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)集成，以便在写入 Azure Cosmos DB 时提供最佳性能。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Cosmos DB (SQL API) 的数据工厂实体，以下部分提供有关可用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB (SQL API) 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **CosmosDb**。 | 是 |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。<br />**注意**：必须如以下示例所示，在连接字符串中指定数据库信息。 <br/> 还可以将帐户密钥放在 Azure 密钥保管库中，并从连接字符串中拉取 `accountKey` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

**示例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储帐户密钥**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。

Azure Cosmos DB (SQL API) 数据集支持以下属性： 

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **CosmosDbSqlApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 文档集合的名称。 |是 |

如果使用“DocumentDbCollection”类型的数据集，则仍按原样提供支持，以实现复制和查找活动的后向兼容性。 建议你今后使用新模型。

**示例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分列出了Azure Cosmos DB (SQL API) 源和接收器支持的属性。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) 用作源

若要从 Azure Cosmos DB (SQL API) 复制数据，请将复制活动中的 **source** 类型设置为 **DocumentDbCollectionSource**。 

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **CosmosDbSqlApiSource**。 |是 |
| 查询 |指定要读取数据的 Azure Cosmos DB 查询。<br/><br/>示例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行此 SQL 语句：`select <columns defined in structure> from mycollection` |
| preferredRegions | 从 Cosmos DB 检索数据时要连接到的区域的首选列表。 | 否 |
| pageSize | 查询结果的每页文档数。 默认值为“-1”，表示使用服务端动态页大小，最大为 1000。 | 否 |

如果使用“DocumentDbCollectionSource”类型的源，则仍按原样提供支持以实现后向兼容性。 建议今后使用新模型，新模型提供了更丰富的功能来从 Cosmos DB 复制数据。

**示例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "China East 2"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

从 Cosmos DB 复制数据时，除非你想[原样导出 JSON 文档](#import-and-export-json-documents)，否则，最佳做法是在复制活动中指定映射。 数据工厂遵循你在活动上指定的映射 - 如果某个行的某个列中未包含值，则会为列值提供 null 值。 如果未指定映射，则数据工厂将使用数据中的第一行来推断架构。 如果第一行不包含完整架构，则活动操作的结果中将丢失部分列。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) 用作接收器

若要将数据复制到 Azure Cosmos DB (SQL API)，请将复制活动中的 **sink** 类型设置为 **DocumentDbCollectionSink**。 

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **CosmosDbSqlApiSink**。 |是 |
| writeBehavior |描述如何将数据写入 Azure Cosmos DB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 ID 的文档，则替换该文档；否则将插入该文档。<br /><br />**注意**：如果未在原始文档中指定 ID，或未通过列映射指定 ID，则数据工厂会自动为文档生成 ID。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | 数据工厂使用 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)将数据写入 Azure Cosmos DB。 **writeBatchSize** 属性控制 ADF 提供给库的文档的大小。 可尝试增加 writeBatchSize 的值以提高性能，并在文档大小较大时降低该值 - 请参阅下面的提示  。 |否<br />（默认值为 **10,000**） |
| disableMetricsCollection | 数据工厂收集指标（如 Cosmos DB RU），以获取复制性能优化和建议。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |

>[!TIP]
>若要按原样导入 JSON 文档，请参阅[导入或导出 JSON 文档](#import-and-export-json-documents)部分；若要从表格形数据复制，请参阅[从关系数据库迁移到 Cosmos DB](#migrate-from-relational-database-to-cosmos-db)。

>[!TIP]
>Cosmos DB 将单个请求的大小限制为 2MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 若出现“请求太大。”错误，请减少复制接收器配置中的 `writeBatchSize` 值   。

如果使用“DocumentDbCollectionSink”类型的源，则仍按原样提供支持以实现后向兼容性。 建议今后使用新模型，新模型提供了更丰富的功能来从 Cosmos DB 复制数据。

**示例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>架构映射

要将数据从 Azure Cosmos DB 复制到表格接收器或进行反向复制，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。
## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="import-and-export-json-documents"></a>导入和导出 JSON 文档

使用此 Azure Cosmos DB(SQL API) 连接器，可以轻松地：

* 在两个 Azure Cosmos DB 集合之间按原样复制文档。
* 将各种源（包括 Azure Blob 存储和 Azure 数据工厂所支持的其他基于文件的存储）中的 JSON 文档导入 Azure Cosmos DB。
* 将 JSON 文档从 Azure Cosmos DB 集合导出到各种基于文件的存储。

若要实现“架构不可知”复制，请执行以下操作：

* 使用复制数据工具时，选择“原样导出到 JSON 文件或 Cosmos DB 集合”选项  。
* 使用活动创作时，请为源或接收器选择 JSON 格式以及相应的文件存储。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>从关系数据库迁移到 Cosmos DB

从关系数据库（例如 SQL Server）迁移到 Azure Cosmos DB 时，复制活动可以轻松地从源映射表格数据，以在 Cosmos DB 中平展 JSON 文档。 某些情况下，你可能希望根据 [Azure Cosmos DB 中的数据建模](../cosmos-db/modeling-data.md)重新设计数据模型，以便来针对 NoSQL 用例对其进行优化，例如，通过将所有相关子项嵌入到一个 JSON 文档中来使数据非规范化。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
