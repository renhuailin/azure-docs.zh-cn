---
title: 将现有数据迁移到 Azure Cosmos DB 的表 API 帐户
description: 了解如何将本地或云数据迁移或导入到 Azure Cosmos DB 的 Azure 表 API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fa40f5d77fc9aa993f510510eb6396e011bbf3fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782115"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>将数据迁移到 Azure Cosmos DB 表 API 帐户
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

本教程说明如何导入要在 Azure Cosmos DB [表 API](introduction.md) 中使用的数据。 如果已将数据存储在 Azure 表存储中，可以使用数据迁移工具或 AzCopy 将数据导入 Azure Cosmos DB 表 API。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据

## <a name="prerequisites"></a>先决条件

* **增加吞吐量：** 数据迁移的持续时间取决于为单个容器或一组容器设置的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。

* 创建 Azure Cosmos DB 资源：在开始迁移数据之前，从 Azure 门户创建所有表。 如果要迁移到具有数据库级别吞吐量的 Azure Cosmos DB 帐户，请确保在创建 Azure Cosmos DB 表时提供分区键。

## <a name="data-migration-tool"></a>数据迁移工具

你可以使用命令行数据迁移工具 (dt.exe) Azure Cosmos DB 将现有 Azure 表存储数据导入表 API 帐户。 

如要迁移表数据：

1. 从 [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) 下载迁移工具。
2. 使用适用于你方案的命令行参数运行 `dt.exe`。 `dt.exe` 采用以下格式的命令：

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

此命令支持的选项为：

* **/ErrorLog：** 可选。 要重定向数据传输失败的 CSV 文件名称。
* **/OverwriteErrorLog：** 可选。 覆盖错误日志文件。
* /ProgressUpdateInterval：可选，默认值为 `00:00:01`。 刷新屏幕上数据传输进度的时间间隔。
* /ErrorDetails：可选，默认值为 `None`。 指定应针对以下错误显示详细错误信息：`None`、`Critical` 或 `All`。
* **/EnableCosmosTableLog：** 可选。 将日志定向到 Azure Cosmos DB 表帐户。 如果设置，这会默认为目标帐户连接字符串，除非还提供了 `/CosmosTableLogConnectionString`。 如果同时运行多个工具实例，这将很有用。
* **/CosmosTableLogConnectionString：** 可选。 用于将日志定向到远程 Azure Cosmos DB 表帐户的连接字符串。

### <a name="command-line-source-settings"></a>命令行源设置

将 Azure 表存储定义为迁移源时，请使用以下源选项。

* /s:AzureTable：从表存储读取数据。
* **/s.ConnectionString：** 表终结点的连接字符串。 你可从 Azure 门户进行检索。
* /s.LocationMode：可选，默认值为 `PrimaryOnly`。 指定连接到表存储时要使用的位置模式：`PrimaryOnly`、`PrimaryThenSecondary`、`SecondaryOnly` 和 `SecondaryThenPrimary`。
* /s.Table：Azure 表的名称。
* /s.InternalFields：由于导入需要 `RowKey` 和 `PartitionKey`，请设置为 `All` 以进行表迁移。
* **/s.Filter：** 可选。 要应用的筛选器字符串。
* **/s.Projection：** 可选。 要选择的列的列表。

若要在从表存储导入时检索源连接字符串，请打开 Azure 门户。 选择“存储帐户” > “帐户” > “访问密钥”，并复制连接字符串。

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="显示“存储帐户”>“帐户”>“访问密钥”选项并突出显示“复制”图标的屏幕截图。":::

### <a name="command-line-target-settings"></a>命令行目标设置

当你将 Azure Cosmos DB 表 API 定义为迁移目标时，请使用以下目标选项。

* /t:TableAPIBulk：按批次将数据上传到 Azure Cosmos DB 表 API。
* /t.ConnectionString：表终结点的连接字符串。
* /t.TableName：指定要写入的表名称。
* /t.Overwrite：可选，默认值为 `false`。 指定是否应覆盖现有值。
* /t.MaxInputBufferSize：可选，默认值为 `1GB`。 将数据刷新到接收器之前要缓冲的输入字节大致估计值。
* **/t.Throughput：** 可选，如果未指定，则为服务默认值。 指定要为表配置的吞吐量。
* /t.MaxBatchSize：可选，默认值为 `2MB`。 指定批大小（以字节为单位）。

### <a name="sample-command-source-is-table-storage"></a>示例命令：源是表存储

下面的命令行示例展示了如何从表存储导入到表 API：

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>使用 AzCopy 迁移数据

你还可以使用 AzCopy 命令行实用程序将数据从表存储迁移到 Azure Cosmos DB 表 API。 若要使用 AzCopy，首先按[从表存储导出你的数据](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)所述导出你的数据。 然后，你可以按 [Azure Cosmos DB 表 API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) 所述将数据导入 Azure Cosmos DB。

导入到 Azure Cosmos DB 时，请参阅以下示例。 请注意 `/Dest` 值使用 `cosmosdb`，而非 `core`。

示例导入命令：

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>后续步骤

如何使用 Azure Cosmos DB 表 API 查询表数据。 

> [!div class="nextstepaction"]
>[如何查询数据？](tutorial-query-table.md)




