---
title: 什么是 Azure Cosmos DB 分析存储？
description: 了解 Azure Cosmos DB 事务性（基于行）和分析（基于列）存储。 分析存储的优势、对大型工作负荷的性能响应以及自动将事务性存储中的数据同步到分析存储
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: cc12626747aa7ce8a294695e27239fac36ce5cd0
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824927"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>什么是 Azure Cosmos DB 分析存储？
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 分析存储是完全独立的列存储，可以借助它对 Azure Cosmos DB 中的操作数据进行大型分析，这对事务性工作负载没有任何影响。 

Azure Cosmos DB 事务性存储架构不可知，因此你能够迭代事务性应用程序，而无需处理架构或索引管理。 与此相反，Azure Cosmos DB 分析存储已架构化，以便优化分析查询性能。 本文详细介绍分析存储。

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>对操作数据进行大型分析面临的挑战

Azure Cosmos DB 容器中的多模型操作数据存储在已编索的基于行的“事务性存储”内部。 行存储格式旨在快速实现事务性读写（以毫秒级的响应时间）和操作查询。 如果数据集增长得很大，就以此格式存储的数据的预配吞吐量而言，复杂的分析查询可能会非常昂贵。 预配吞吐量的高消耗反过来会影响你的实时应用程序和服务所使用的事务工作负荷的性能。

通常，若要分析大量的数据，则从 Azure Cosmos DB 的事务性存储中提取操作数据，并将其存储在单独的数据层中。 例如，采用适当的格式将数据存储在数据仓库或数据湖中。 之后将使用此数据进行大型分析，并使用计算引擎（如 Apache Spark 群集）分析它们。 从操作数据中分离分析存储和计算层会增加延迟，因为运行 ETL（提取、转换、加载）管道的频率会降低，以最大程度减少对事务性工作负荷的潜在影响。

相对于仅处理新引入的操作数据而言，处理操作数据更新时，ETL 管道也会变得复杂。 

## <a name="column-oriented-analytical-store"></a>面向列的分析存储

Azure Cosmos DB 分析存储解决了传统 ETL 管道所具有的复杂和延迟问题。 Azure Cosmos DB 分析存储可以自动将操作数据同步到单独的列存储。 列存储格式适用于采用优化的方式执行大型分析查询，可改进此类查询的延迟性。

借助 Azure Synapse Link，现可直接从 Azure Synapse Analytics 链接到 Azure Cosmos DB 分析存储，构建不用 ETL HTAP 的解决方案。 借助它，你可以以接近实时的速度对操作数据运行的大型分析。

## <a name="features-of-analytical-store"></a>分析存储的功能 

在 Azure Cosmos DB 容器中启用分析存储时，将基于容器中的操作数据在内部创建新的列存储。 此列存储将与该容器的面向行的事务性存储分开保存。 对操作数据的插入、更新和删除将自动同步到分析存储。 你无需更改源或 ETL，即可同步数据。

## <a name="column-store-for-analytical-workloads-on-operational-data"></a>用于操作数据分析工作负荷的列存储

分析工作负荷通常涉及聚合和按顺序扫描选定字段。 通过按以列为主的顺序存储数据，分析存储可以将每个字段的值组合在一起序列化。 此格式减少了扫描或计算特定字段中统计信息所需的 IOPS。 它极大地改进了扫描大型数据集所需的查询响应时间。 

例如，操作表采用下面的格式时：

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="示例操作表" border="false":::

行存储会采用序列化格式将上面的数据按行保存到磁盘中。 此格式可以加快事务性读写和操作查询，如“返回产品 1 的相关信息”。 不过，随着数据集增大，如果你想要对数据运行复杂的分析查询，它的成本可能会很高。 例如，如果想要获取“‘设备’类别下的某个产品在不同业务单位和月份的销量趋势”，则需要运行复杂查询。 就预配的吞吐量而言，对此数据集执行大型扫描的成本可能会变得很高，这还可能会影响支持实时应用程序和服务的事务性工作负荷的性能。

分析存储是列存储，它更适用于此类查询，因为它会将相似的数据字段一起序列化，减少磁盘 IOPS。

下图说明了 Azure Cosmos DB 中的事务性行存储与分析列存储：

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Azure Cosmos DB 中的事务性行存储与分析列存储" border="false":::

## <a name="decoupled-performance-for-analytical-workloads"></a>分析工作负荷性能已分离

分析查询不会影响事务性工作负荷的性能，因为分析存储已与事务性存储分开。  分析存储无需分配单独的请求单位 (RU)。

## <a name="auto-sync"></a>自动同步

自动同步是指 Azure Cosmos DB 的完全托管功能，对操作数据执行的插入、更新、删除将准实时自动从事务存储同步到分析存储。 自动同步延迟通常在 2 分钟内。 如果共享吞吐量数据库拥有大量容器，则单个容器的自动同步延迟可能会更高，最长可能达 5 分钟。 我们希望详细了解此延迟如何适应你的场景。 请联系 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)提供相关反馈。

每次执行自动同步过程结束时，事务数据将立即可用于 Azure Synapse Analytics 运行时：

* Azure Synapse Analytics Spark 池可以通过自动更新的 Spark 表读取所有数据（包括最新更新），也可以通过始终读取数据最新状态的 `spark.read` 命令读取。

*  Azure Synapse Analytics SQL 无服务器池可以通过自动更新的视图读取所有数据（包括最新更新），也可以通过始终读取数据最新状态的 `SELECT` 和 ` OPENROWSET` 命令读取。

> [!NOTE]
> 即使事务 TTL 小于 2 分钟，事务数据也会同步到分析存储。 

## <a name="scalability--elasticity"></a>可伸缩性和弹性

通过使用水平分区，Azure Cosmos DB 事务性存储无需停机即可弹性缩放存储和吞吐量。 事务性存储中的水平分区为自动同步提供了可伸缩性和弹性，确保数据以接近实时的速度同步到分析存储。 无论事务性流量吞吐量如何数据同步都会执行（无论是 1000 个操作/秒还是 1 百万个操作/秒），并且它不会影响事务性存储的预配吞吐量。 

## <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>自动处理架构更新

Azure Cosmos DB 事务性存储架构不可知，因此你能够迭代事务性应用程序，而无需处理架构或索引管理。 与此相反，Azure Cosmos DB 分析存储已架构化，以便优化分析查询性能。 借助自动同步功能，Azure Cosmos DB 可管理对事务存储中最新更新的架构推断。 它还管理现成分析存储中的架构表示形式，其中包括处理嵌套数据类型。

随着架构不断演化，并且将随时间推移添加新属性，分析存储会自动跨事务存储中的所有历史架构呈现联合架构。

> [!NOTE]
> 在分析存储的上下文中，我们将以下结构视为属性：
> * JSON“元素”或“由 `:` 分隔的字符串值对”。
> * JSON 对象，由 `{` 和 `}` 分隔。
> * JSON 数组，由 `[` 和 `]` 分隔。


### <a name="schema-constraints"></a>架构约束

当启用分析存储以自动推断并正确表示架构时，以下约束适用于 Azure Cosmos DB 中的操作数据：

* 架构的任何嵌套级别最多可以有 1000 个属性，最大嵌套深度为 127 个级别。
  * 分析存储中只表示前 1000 个属性。
  * 分析存储中只表示前 127 个嵌套层级。
  * JSON 文档的第一个级别是它的 `/` 根级别。
  * 文档中第一级的属性将以列的形式表示。


* 示例方案：
  * 如果文档的第一个级别具有 2000 个属性，则只表示前 1000 个属性。
  * 如果文档有 5 个级别，每个级别包含 200 个属性，则将表示所有属性。
  * 如果文档有 10 个级别，每个级别包含 400 个属性，则只有前 2 个级别将在分析存储中完全表示。 还将表示第三个级别一半的属性。

* 下面的假设文档包含 4 个属性和 3 个级别。
  * 其中的级别为 `root`、`myArray` 和 `myArray` 中的嵌套结构。
  * 其中的属性为 `id`、`myArray`、`myArray.nested1` 和 `myArray.nested2`。
  * 分析存储表示形式将具有两列：`id` 和 `myArray`。 使用 Spark 或 T-SQL 函数也可以将嵌套结构作为列公开。


```json
{
  "id": "1",
  "myArray": [
    "string1",
    "string2",
    {
      "nested1": "abc",
      "nested2": "cde"
    }
  ]
}
```

* 尽管从唯一性角度来看，JSON 文档（和 Cosmos DB 集合/容器）区分大小写，但分析存储不区分大小写。

  * 在同一文档中：以不区分大小写的方式进行比较时，相同级别的属性名称应是唯一的。 例如，以下 JSON 文档在同一级别具有“Name”和“name”。 尽管它是有效的 JSON 文档，但不满足唯一性约束，因此不会完全表示在分析存储中。 在本例中，以不区分大小写的方式进行比较时，“Name”和“name”是相同的。 只有 `"Name": "fred"` 表示在分析存储中，因为它是第一个出现的。 完全不会表示 `"name": "john"`。
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * 在不同文档中：同一级别同名但大小写不同的属性会在同一列中表示，使用第一次出现的项目的名称格式。 例如，以下 JSON 文档在同一级别具有 `"Name"` 和 `"name"`。 由于第一个文档格式是 `"Name"`，则分析存储中将使用该格式来表示该属性名称。 换言之，分析存储中的列名称将为 `"Name"`。 `"fred"` 和 `"john"` 都会在 `"Name"` 列表示。


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* 集合的第一个文档定义初始分析存储架构。
  * 属性数量超过初始架构的文档将在分析存储中生成新列。
  * 不能删除列。
  * 删除集合中的所有文档不会重置分析存储架构。
  * 架构没有版本控制。 用户将在分析存储中看到的版本是从事务存储中推断出的最后一个版本。

* 目前，Azure Synapse Spark 无法读取下列名称中包含某些特殊字符的属性。 如果是这样，请与 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)联系以了解详细信息。
  * :（冒号）
  * `（抑音符）
  * ,（逗号）
  * ;（分号）
  * {}
  * ()
  * \n
  * \t
  * =（等号）
  * "（引号）
 
* Azure Synapse Spark 现在支持名称中包含空格的属性。

### <a name="schema-representation"></a>架构表示形式

在分析存储中，架构表示形式有两种模式。 这些模式为数据库帐户中的所有容器定义架构表示方法，并在查询体验简单性与多态架构的更大包容性纵栏表示形式的方便性之间进行权衡。

* 明确定义的架构表示形式，是 SQL (CORE) API 帐户的默认选项。 
* 全保真架构表示形式，是 Azure Cosmos DB API for MongoDB 帐户的默认选项。

可为 SQL (Core) API 帐户使用全保真架构。 下面是有关这种可行方案的注意事项：

 * 此选项仅对未启用 Synapse Link 的帐户有效。
 * 无法通过关闭再打开 Synapse Link 来重置默认选项并从明确定义的表示形式更改为全保真表示形式。
 * 无法使用任何其他过程从明确定义的表示形式更改为全保真表示形式。
 * MongoDB 帐户与这种更改表示方法的可行方案不兼容。
 * 目前无法通过 Azure 门户做出此决策。
 * 应在对帐户启用 Synapse Link 的同时对此选项做出决策：
 
 使用 Azure CLI：
 ```cli
 az cosmosdb create --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --subscription MySubscription --analytical-storage-schema-type "FullFidelity" --enable-analytical-storage true
 ```
 
> [!NOTE]
> 在以上命令中，请将针对现有帐户运行的 `create` 替换为 `update`。
 
  使用 PowerShell：
  ```
   New-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount  -EnableAnalyticalStorage true -AnalyticalStorageSchemaType "FullFidelity"
   ```
 
> [!NOTE]
> 在以上命令中，请将针对现有帐户运行的 `New-AzCosmosDBAccount` 替换为 `Update-AzCosmosDBAccount`。
 


#### <a name="well-defined-schema-representation"></a>定义明确的架构表示形式

定义明确的架构表示形式可在事务存储中创建与架构无关的数据的简单表格表示形式。 定义明确的架构表示形式具有以下注意事项：

* 第一个文档定义基本架构，属性必须始终在所有文档中具有相同的类型。 仅有的例外情况是：
  * 从 null 更改为任何其他数据类型。第一个出现的非 null 项定义列数据类型。 不遵循第一个非 null 数据类型的任何文档不会在分析存储中表示。
  * 从 `float` 到 `integer`。 所有文档将在分析存储中表示。
  * 从 `integer` 到 `float`。 所有文档将在分析存储中表示。 但是，若要使用 Azure Synapse SQL 无服务器池读取此数据，必须使用 WITH 子句将列转换为 `varchar`。 完成这种初始转换后，可以再次将列转换为数字。 请查看以下示例，其中的 num 初始值是整数，第二个值是浮点数。

```SQL
SELECT CAST (num as float) as num
FROM OPENROWSET(PROVIDER = 'CosmosDB',
                CONNECTION = '<your-connection',
                OBJECT = 'IntToFloat',
                SERVER_CREDENTIAL = 'your-credential'
) 
WITH (num varchar(100)) AS [IntToFloat]
```

  * 不遵循基本架构数据类型的属性不会在分析存储中表示。 以下面的 2 个文档为例，其中第一个文档定义了分析存储基本架构。 第二个文档（其中的 `id` 为 `2`）没有明确定义的架构，因为属性 `"a"` 是字符串，而第一个文档中的 `"a"` 是数字。 在这种情况下，分析存储会在容器生存期内将 `"a"` 数据类型注册为 `integer`。 第二个文档仍会包含在分析存储中，但其 `"a"` 属性则不会。
  
    * `{"id": "1", "a":123}` 
    * `{"id": "2", "a": "str"}`
     
 > [!NOTE]
 > 上述条件不适用于 null 属性。 例如，`{"a":123} and {"a":null}` 仍是定义明确的。

* 数组类型必须包含单个重复的类型。 例如，`{"a": ["str",12]}` 不是定义明确的架构，因为此数组包含整数和字符串类型组合。

> [!NOTE]
> 如果 Azure Cosmos DB 分析存储遵循定义明确的架构表示形式，但某些项违反了上述规范，则这些项不会包含在分析存储中。

* 对于定义完善的架构中的不同类型，需要不同的行为：
  * Azure Synapse 中的 Spark 池会将这些值表示为 `undefined`。
  * Azure Synapse 中的 SQL 无服务器池会将这些值表示为 `NULL`。

* 对于显式 `null` 值，需要不同的行为：
  * Azure Synapse 中的 Spark 池会将这些值作为 `0`（零）读取。 一旦该列具有非 null 值，它就会更改为 `undefined`。
  * Azure Synapse 中的 SQL 无服务器池将这些值读取为 `NULL`。
    
* 对于缺少的列，预期存在不同的行为：
  * Azure Synapse 中的 Spark 池会将这些列表示为 `undefined`。
  * Azure Synapse 中的 SQL 无服务器池会将这些列表示为 `NULL`。


#### <a name="full-fidelity-schema-representation"></a>完全保真架构表示形式

完全保真架构表示形式旨在处理与架构无关的操作数据中的各种多态架构。 在此架构表示形式中，即使违反定义明确的架构约束（也就是既没有混合数据类型字段也没有混合数据类型数组），也不会从分析存储中删除任何项。

这是通过根据属性中值的数据类型将操作数据的叶属性转换为具有不同列的分析存储来实现的。 叶属性名称在分析存储架构中使用数据类型作为后缀进行扩展，以便它们可以无歧义地进行查询。

在完全保真架构表示形式中，每个属性的每个数据类型都将生成该数据类型的列。 每个属性都计为最多 1000 个属性的其中之一。

例如，我们来看事务存储中的以下示例文档：

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

嵌套对象 `address` 中的叶属性 `streetNo` 将在分析存储架构中表示为列 `address.object.streetNo.int32`。 数据类型作为后缀添加到列中。 这样，如果有另一个文档添加到事务存储中，其叶属性 `streetNo` 的值为“123”（请注意，这是一个字符串），则分析存储的架构会自动演变，而不会更改先前写入的列的类型。 一个新列作为 `address.object.streetNo.string` 添加到分析存储中，其中存储值“123”。

**要映射到后缀的数据类型**

下面是分析存储中所有属性数据类型及其后缀表示形式的映射：

|原始数据类型  |Suffix  |示例  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Array | ".array" |    ["a", "b"]|
|二进制 | ".binary" |0|
|布尔    | ".bool"   |True|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|String|    ".string" | "ABC"|
|时间戳 |    ".timestamp" |  Timestamp(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|文档   |".object" |    {"a": "a"}|

* 对于显式 `null` 值，需要不同的行为：
  * Azure Synapse 中的 Spark 池会将这些值作为 `0`（零）读取。
  * Azure Synapse 中的 SQL 无服务器池将这些值读取为 `NULL`。
  
* 对于缺少的列，预期存在不同的行为：
  * Azure Synapse 中的 Spark 池会将这些列表示为 `undefined`。
  * Azure Synapse 中的 SQL 无服务器池会将这些列表示为 `NULL`。

## <a name="cost-effective-archival-of-historical-data"></a>以经济高效的方式将历史数据存档

数据分层指在存储基础结构之间分隔数据，以适用于不同的方案， 从而提高端到端数据堆栈的整体性能和经济高效性。 Azure Cosmos DB 借助分析存储，现在支持使用不同的数据布局将事务性存储中的数据自动分层到分析存储。 相对于事务性存储，分析存储在存储成本方面实现了优化，你可以将操作数据保留更长时间，来进行历史分析。

启用分析存储后，你可以根据事务性工作负荷的数据保留需求，配置“事务性存储生存时间(事务性 TTL)”属性，以便超过特定时间后即自动从事务性存储删除记录。 同样，你可以通过“分析存储生存时间(分析 TTL)”管理分析存储（独立于事务性存储）中保留的数据的生命周。 通过启用分析存储并配置 TTL 属性，你可以进行无缝分层，并定义两个存储的数据保留期。

> [!NOTE]
>目前，分析存储不支持备份和还原。 不能依赖分析存储来计划备份策略。 有关详细信息，请参阅[本文档](synapse-link.md#limitations)的限制部分。 需要注意的是，分析存储中数据的架构不同于事务存储中已存在的架构。 虽然可以生成分析存储数据的快照，而不会产生任何 RU 成本，但我们不能保证使用此快照可以为事务存储提供反馈。 此过程不受支持。

## <a name="global-distribution"></a>全局分发

若你拥有全局分发的 Azure Cosmos DB 帐户，为容器启用分析存储后，它将适用于该帐户的所有区域。  对操作数据的任何更改均将全局复制到所有区域。 你可以高效地对 Azure Cosmos DB 中距离最近的区域的数据副本运行分析查询。

## <a name="security"></a>安全性

* 对分析存储进行身份验证的方式，与对给定数据库的事务性存储进行身份验证的方式相同。 可以使用主密钥或只读密钥进行身份验证。 可以利用 Synapse Studio 中的链接服务，以防止粘贴 Spark 笔记本中的 Azure Cosmos DB 密钥。 对于 Azure Synapse SQL 无服务器，也可使用 SQL 凭据防止粘贴 SQL 笔记本中的 Azure Cosmos DB 密钥。 有权访问该工作区的任何人都可以访问此链接的服务或此凭据。

* **使用专用终结点的网络隔离** - 可以单独控制对事务性和分析存储中的数据的网络访问。 在 Azure Synapse 工作区的托管虚拟网络内，为每个存储使用单独的托管专用终结点进行网络隔离。 如要了解详细信息，请参阅如何[为分析存储配置专用终结点](analytical-store-private-endpoints.md)一文。

* 使用客户管理的密钥进行数据加密 - 可以采用自动且透明的方式使用相同的客户管理密钥无缝地跨事务存储和分析存储加密数据。 Azure Synapse Link 仅支持使用 Azure Cosmos DB 帐户的托管标识配置客户管理的密钥。 在帐户上[启用 Azure Synapse Link](configure-synapse-link.md#enable-synapse-link) 之前，必须在 Azure Key Vault 访问策略中配置帐户的托管标识。 若要了解详细信息，请参阅[如何使用 Azure Cosmos DB 帐户的托管标识配置客户管理的密钥](how-to-setup-cmk.md#using-managed-identity)一文。

## <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>支持多个 Azure Synapse Analytics 运行时

分析存储已经过优化，无需依赖计算运行时即可为分析工作负荷提供可伸缩性、弹性和性能。 存储技术是自行管理，无需手动操作即可优化分析工作负荷。

通过将分析存储系统与分析计算系统分离，可以同时从 Azure Synapse Analytics 支持的不同分析运行时中查询 Azure Cosmos DB 分析存储中的数据。 目前，Azure Synapse Analytics 支持 Apache Spark 和无服务器 SQL 池使用 Azure Cosmos DB 分析存储。

> [!NOTE]
> 只能使用 Azure Synapse Analytics 运行时从分析存储中读取内容。 反过来也是如此，Azure Synapse Analytics 运行时只能从分析存储中读取内容。 只有自动同步过程才能更改分析存储中的数据。 可以使用 Azure Synapse Analytics Spark 池通过内置的 Azure Cosmos DB OLTP SDK 将数据写回到 Cosmos DB 事务存储。

## <a name="pricing"></a><a id="analytical-store-pricing"></a> 定价

分析存储采用基于消耗的定价模型，计费项包括：

* 存储：分析存储每个月保留的数据量，包括分析 TTL 定义的历史数据。

* 分析写入操作：从事务性存储将操作数据更新以完全托管的方式同步到分析存储（自动同步）

* 分析读取操作：从 Azure Synapse Analytics Spark 池和无服务器 SQL 池运行时对分析存储执行的读取操作。

分析存储定价与事务性存储定价模型不同。 分析存储中没有预配 RU 这一概念。 有关分析存储定价模型的完整详细信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

若要为在 Azure Cosmos DB 容器中启用分析存储进行高级别成本估算，可以使用 [Azure Cosmos DB 容量规划器](https://cosmos.azure.com/capacitycalculator/)，来获得分析存储和写入操作成本的估算值。 分析读取操作成本取决于分析工作负荷特性，但按照高级别估算，扫描分析存储中的 1 TB 的数据通常引发 130,000 个分析读取操作，产生的成本为 0.065 美元。

> [!NOTE]
> Cosmos DB 成本计算器不包括分析存储读取操作估计，因为它们是分析工作负载的函数。 尽管上述估计可用于扫描分析存储中 1 TB 的数据，但应用筛选器可减少扫描的数据量，并且可在给定消耗定价模型的情况下分析读取操作的确切数目。 围绕分析工作负载的概念证明可为分析读取操作提供更精细的估计。


## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> 分析生存时间 (TTL)

分析 TTL 表示应将容器的分析存储中的数据保留多久。 

如果启用了分析存储，则无论事务性 TTL 配置如何，都会从事务性存储中将对操作数据执行的插入、更新、删除自动同步到分析存储。 分析存储中操作数据的保留可以由容器级别的分析 TTL 值控制，如下所示：

使用 `AnalyticalStoreTimeToLiveInSeconds` 属性设置容器中的分析 TTL：

* 若将此值设置为“0”、此值缺失（或设置为 null）：将禁用分析存储，不会将任何数据从事务性存储复制到分析存储

* 若此值存在且设置为“-1”：无论事务性存储中数据的保留期是多久，分析存储都将保留所有历史数据。 此设置表示分析存储会无限期保留操作数据

* 若此值存在且设置为某个正数“n”：距在事务性存储中最后一次修改项“n”秒后，项将从分析存储中过期。 如果想要在有限的一段时间内将操作数据保留在分析存储中，而不考虑该数据在事务性存储中的保留期，可利用此设置。

考虑的要点：

*   使用分析 TTL 值启用分析存储后，可以在以后将其更新为其他有效值。 
*   尽管可在容器或项级别设置事务性 TTL，但目前只能在容器级别设置分析 TTL。
*   将容器级别的分析 TTL 设置为大于或等于事务性 TTL，即可将操作数据存档在分析存储中更长时间。
*   将分析 TTL 设置为等于事务性 TTL，分析存储即可镜像事务存储。

如何在容器上启用分析存储：

* 在 Azure 门户中，当打开分析 TTL 选项时，它设置为默认值 -1。 可以通过导航到数据资源管理器下的容器设置，将此值更改为“n”秒。 
 
* 在 Azure 管理 SDK、Azure Cosmos DB SDK、PowerShell 或 CLI 中，可以通过将分析 TTL 选项设置为 -1 或“n”秒来启用该选项。 

若要了解详细信息，请参阅[如何对容器配置分析 TTL](configure-synapse-link.md#create-analytical-ttl)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)

* 查看有关如何[使用 Azure Synapse Analytics 设计混合事务和分析处理](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/)的学习模块

* [开始使用 Azure Synapse Link for Azure Cosmos DB](configure-synapse-link.md)

* [有关 Synapse Link for Azure Cosmos DB 的常见问题](synapse-link-frequently-asked-questions.yml)

* [Azure Synapse Link for Azure Cosmos DB 用例](synapse-link-use-cases.md)
