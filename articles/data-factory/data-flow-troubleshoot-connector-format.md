---
title: 排查映射数据流中的连接器和格式问题
description: 了解如何排查 Azure 数据工厂中与连接器和格式相关的数据流问题。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/17/2021
ms.openlocfilehash: 79a64a7eb1e06fef3c9e534a69324faaf9f23107
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867532"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>排查 Azure 数据工厂中映射数据流中的连接器和格式问题


本文探讨了 Azure 数据工厂 (ADF) 中与映射数据流的连接器和格式相关的故障排除方法。

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="account-kind-of-storage-general-purpose-v1-doesnt-support-service-principal-and-mi-authentication"></a>存储帐户类型（常规用途 v1）不支持服务主体和 MI 身份验证

#### <a name="symptoms"></a>症状

在数据流中，如果将 Azure Blob 存储（常规用途 v1）与服务主体或 MI 身份验证一起使用，则可能会遇到以下错误消息：

`com.microsoft.dataflow.broker.InvalidOperationException: ServicePrincipal and MI auth are not supported if blob storage kind is Storage (general purpose v1)`

#### <a name="cause"></a>原因

在数据流中使用 Azure Blob 链接服务时，如果帐户类型为空或“存储”，则不支持托管标识或服务主体身份验证。 此情况如下图 1 和图 2 所示。

图 1： Azure Blob 存储链接服务中的帐户类型

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-kind.png" alt-text="屏幕截图：显示 Azure Blob 存储链接服务中存储帐户类型。"::: 

图 2：存储帐户页

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-page.png" alt-text="屏幕截图：显示存储帐户页。" lightbox="./media/data-flow-troubleshoot-connector-format/storage-account-page.png"::: 


#### <a name="recommendation"></a>建议

若要解决此问题，请参考以下建议：

- 如果在 Azure Blob 链接服务中存储帐户类型为“无”，请指定适当的帐户类型，并参阅下图 3 来完成该操作。 此外，请参阅图 2 获取存储帐户类型，检查并确认该帐户类型不是“存储”（常规用途 v1）。

    图 3：在 Azure Blob 存储链接服务中指定存储帐户类型

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/specify-storage-account-kind.png" alt-text="屏幕截图：显示如何在 Azure Blob 存储链接服务中指定存储帐户类型。"::: 
    

- 如果帐户类型为“存储”（常规用途 v1），请将存储帐户升级为常规用途 v2 或选择其他身份验证。

    图 4：将存储帐户升级为常规用途 v2

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png" alt-text="屏幕截图：显示如何将存储帐户升级为常规用途 v2。" lightbox="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png"::: 

## <a name="azure-cosmos-db-and-json-format"></a>Azure Cosmos DB 和 JSON 格式

### <a name="support-customized-schemas-in-the-source"></a>支持源中的自定义架构

#### <a name="symptoms"></a>症状
如果要使用 ADF 数据流将数据从 Cosmos DB/JSON 移动或传输到其他数据存储，则可能会缺失源数据的某些列。 

#### <a name="cause"></a>原因 
对于自由架构连接器（与其他行进行比较时，每行的列号、列名和列数据类型可能不同），默认情况下，ADF 使用样本行（例如，前 100 或 1000 行数据）来推断架构，并且推断结果会用作架构来读取数据。 因此，如果数据存储具有未出现在样本行中的额外列，则这些额外列的数据不会被读取、移动或传输到接收器数据存储中。

#### <a name="recommendation"></a>建议
为了覆盖默认行为并引入其他字段，ADF 提供了用于自定义源架构的选项。 可以在数据流源投影中指定架构推断结果中可能缺失的额外/缺失列来读取数据，并且可以应用以下选项之一来设置自定义架构。 通常情况下，选项 1 更为可取。

- 选项 1：与可能是包含数百万行并具有复杂架构的一个大型文件、表或容器的原始源数据进行比较时，可以使用包含要读取的所有列的几行创建临时表/容器，然后继续执行以下操作： 

    1. 使用数据流源“调试设置”使具有样本文件/表的“导入投影”获取完整架构 。 可以执行下图中的步骤：<br/>

        ![屏幕截图，显示用于自定义源架构的第一个选项的第一部分。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. 在数据流画布中选择“调试设置”。
         1. 在弹出窗口中，选择“cosmosSource”选项卡下的“样本表”，然后在“表”块中输入表的名称  。
         1. 选择“保存”以保存设置。
         1. 选择“导入投影”。<br/>  
    
    1. 改回“调试设置”，以将源数据集用于其余数据移动/转换。 可以继续执行下图中的步骤：<br/>

        ![屏幕截图，显示用于自定义源架构的第一个选项的第二部分。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. 在数据流画布中选择“调试设置”。
         1. 在弹出窗口中，选择“cosmosSource”选项卡下的“源数据集” 。
         1. 选择“保存”以保存设置。<br/>
    
    之后，ADF 数据流运行时会接受并使用自定义架构从原始数据存储读取数据。 <br/>

- 选项 2：如果熟悉源数据的架构和 DSL 语言，则可以手动更新数据流源脚本，以添加额外/缺失列来读取数据。 下图显示了一个示例： 

    ![屏幕截图，显示用于自定义源架构的第二个选项。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

### <a name="support-map-type-in-the-source"></a>在源中支持映射类型

#### <a name="symptoms"></a>症状
在 ADF 数据流中，Cosmos DB 或 JSON 源无法直接支持映射数据类型，因此你无法在“导入投影”下获得映射数据类型。

#### <a name="cause"></a>原因
Cosmos DB 和 JSON 利用无架构连接，而相关的 spark 连接器使用示例数据来推理架构，然后该架构将用作 Cosmos DB/JSON 源架构。 推理架构时，Cosmos DB/JSON spark 连接器只能将对象数据推理为结构，而不能推理为映射数据类型，这正是无法直接支持映射类型的原因。

#### <a name="recommendation"></a>建议 
若要解决此问题，请参阅以下示例和步骤，手动更新 Cosmos DB/JSON 源的脚本 (DSL) 以支持映射数据类型。

示例：

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/script-example.png" alt-text="显示有关更新 Cosmos DB/JSON 源脚本 (DSL) 的示例的屏幕截图。" lightbox="./media/data-flow-troubleshoot-connector-format/script-example.png"::: 
    
步骤 1：打开数据流活动的脚本。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/open-script.png" alt-text="显示如何打开数据流活动脚本的屏幕截图。" ::: 
    
步骤 2：参考上述示例更新 DSL 以支持映射类型。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/update-dsl.png" alt-text="显示如何更新 DSL 的屏幕截图。" ::: 

映射类型支持：

|类型 |是否支持映射类型？   |注释|
|-------------------------|-----------|------------|
|Excel、CSV  |否      |两者都是使用基元类型的表格数据源，因此无需支持映射类型。 |
|Orc、Avro |是 |无。|
|JSON|是 |无法直接支持映射类型，请按照本部分中的建议更新源投影下的脚本 (DSL)。|
|Cosmos DB |是 |无法直接支持映射类型，请按照本部分中的建议更新源投影下的脚本 (DSL)。|
|Parquet |是 |parquet 数据集目前不支持复杂数据类型，因此你需要使用数据流 parquet 源下的“导入投影”来获取映射类型。|
|XML |否 |无。|

### <a name="consume-json-files-generated-by-copy-activities"></a>使用复制活动生成的 JSON 文件

#### <a name="symptoms"></a>症状

如果使用复制活动生成一些 JSON 文件，尝试在数据流中读取这些文件会失败，并显示错误消息：`JSON parsing error, unsupported encoding or multiline`

#### <a name="cause"></a>原因

对于复制和数据流，JSON 分别有以下限制：

- 对于 Unicode 编码（utf-8、utf-16、utf-32）JSON 文件，复制活动始终生成带有 BOM 的 JSON 文件。
- 启用了“单个文档”的数据流 JSON 源不支持使用 BOM 进行 Unicode 编码。

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/enabled-single-document.png" alt-text="显示启用了“单个文档”的屏幕截图。"::: 


因此，在满足以下条件时将出现问题：

- 复制活动使用的接收器数据集设置为 Unicode 编码（utf-8、utf-16、utf-16be、utf-32、utf-32be）或使用默认值。
- 复制接收器设置为使用“对象数组”文件模式（如下图所示），无论数据流 JSON 源中是否启用了“单个文件”。 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/array-of-objects-pattern.png" alt-text="显示“对象数组”模式设置的屏幕截图。"::: 
   
#### <a name="recommendation"></a>建议

- 如果在数据流中使用生成的文件，请始终在复制接收器中使用默认文件模式或显式“对象集”模式。
- 禁用数据流 JSON 源中的“单个文档”选项。

>[!Note]
> 从性能角度来看，还建议使用“对象集”。 由于数据流中的“单个文档”JSON 无法对单个大文件进行并行读取，因此本建议没有任何负面影响。

### <a name="the-query-with-parameters-does-not-work"></a>使用参数的查询不起作用

#### <a name="symptoms"></a>症状

Azure 数据工厂中的映射数据流支持使用参数。 参数值由调用管道通过执行数据流活动设置，使用参数可以使数据流具有通用性、灵活性和可重用性。 可以使用这些参数将数据流设置和表达式参数化：[参数化映射数据流](./parameters-data-flow.md)。

设置参数并在数据流源查询中使用它们后，这些参数不会生效。

#### <a name="cause"></a>原因

遇到此错误是由于配置错误。

#### <a name="recommendation"></a>建议

使用以下规则设置查询中的参数，有关详细信息，请参阅[在映射数据流中生成表达式](./concepts-data-flow-expression-builder.md)。

1. 在 SQL 语句的开头应用双引号。
2. 在参数两侧使用单引号。
3. 对所有 CLAUSE 语句使用小写字母。

例如：

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/set-parameter-in-query.png" alt-text="查询中设置参数的屏幕截图。"::: 

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="fail-to-create-files-with-service-principle-authentication"></a>无法创建具有服务主体身份验证的文件

#### <a name="symptoms"></a>症状
尝试将数据从不同源移动或传输至 ADLS Gen1 接收器时，如果链接服务的身份验证方法为服务主体身份验证，则作业可能会失败，并显示以下错误消息：

`org.apache.hadoop.security.AccessControlException: CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.). [2b5e5d92-xxxx-xxxx-xxxx-db4ce6fa0487] failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)`

#### <a name="cause"></a>原因

RWX 权限或数据集属性设置不正确。

#### <a name="recommendation"></a>建议

- 如果目标文件夹未具备正确的权限，请参阅[使用服务主体身份验证](./connector-azure-data-lake-store.md#use-service-principal-authentication)一文，以在 Gen1 中分配正确的权限。

- 如果目标文件夹具有正确的权限，并且你在数据流中使用文件名属性以定位至正确的文件夹和文件名，但数据集的文件路径属性未设置为目标文件路径（通常不设置），如下图所示，你将遇到此失败，因为后端系统尝试基于数据集的文件路径创建文件，而数据集的文件路径未具备正确的权限。
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-path-property.png" alt-text="显示文件路径属性的屏幕截图。"::: 
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-name-property.png" alt-text="显示文件名属性的屏幕截图。"::: 

    
    有两种方法可以解决此问题：
    1. 为数据集的文件路径分配 WX 权限。
    1. 将数据集的文件路径设置为具有 WX 权限的文件夹，并在数据流中设置其余文件夹路径和文件名。

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="failed-with-an-error-error-while-reading-file-xxx-it-is-possible-the-underlying-files-have-been-updated"></a>失败并显示错误：“读取文件 XXX 时出错。 基础文件可能已更新”

#### <a name="symptoms"></a>症状

在数据流中使用 ADLS Gen2 作为接收器来预览数据、调试/触发运行等，并且“接收器”阶段“优化”选项卡中的分区设置不是默认设置时，可能会发现作业失败并显示以下错误消息：

`Job failed due to reason: Error while reading file abfss:REDACTED_LOCAL_PART@prod.dfs.core.windows.net/import/data/e3342084-930c-4f08-9975-558a3116a1a9/part-00000-tid-7848242374008877624-5df7454e-7b14-4253-a20b-d20b63fe9983-1-1-c000.csv. It is possible the underlying files have been updated. You can explicitly invalidate the cache in Spark by running 'REFRESH TABLE tableName' command in SQL or by recreating the Dataset/DataFrame involved.`

#### <a name="cause"></a>原因

1. 没有为 MI/SP 身份验证分配适当的权限。
1. 你可能拥有自定义作业来处理不需要的文件，这将影响数据流的中间输出。

#### <a name="recommendation"></a>建议
1. 检查链接服务是否具有 Gen2 的 R/W/E 权限。 如果使用 MI 身份验证/SP 身份验证，请至少在访问控制 (IAM) 中授予“存储 Blob 数据参与者”角色。
1. 确认你是否有特定作业，可将文件删除或移动到其他位置，而其名称与你的规则并不匹配。 由于数据流首先将分区文件写入目标文件夹，然后执行合并和重命名操作，因此中间文件的名称可能与你的规则并不匹配。

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

### <a name="encounter-an-error-failed-with-exception-handshake_failure"></a>遇到错误：失败并出现异常：handshake_failure 

#### <a name="symptoms"></a>症状
在数据流中使用 Azure PostgreSQL 作为源或接收器来预览数据和调试/触发运行时，可能会发现作业失败并显示以下错误消息： 

   `PSQLException: SSL error: Received fatal alert: handshake_failure `<br/>
   `Caused by: SSLHandshakeException: Received fatal alert: handshake_failure.`

#### <a name="cause"></a>原因 
如果 Azure PostgreSQL 服务器使用灵活服务器或超大规模 (Citus)，由于系统是在 Azure Databricks 群集上通过 Spark 构建，所以 Azure Databricks 在阻止系统连接至灵活服务器或超大规模 (Citus) 时存在限制。 你可以参考以下两个链接：
- [尝试使用 SSL 从 Azure Databricks 连接至 Azure PostgreSQL 时握手失败](/answers/questions/170730/handshake-fails-trying-to-connect-from-azure-datab.html)
 
- [包含 Azure Database for PostgreSQL 超大规模的 MCW 实时数据](https://github.com/microsoft/MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale/blob/master/Hands-on%20lab/HOL%20step-by%20step%20-%20Real-time%20data%20with%20Azure%20Database%20for%20PostgreSQL%20Hyperscale.md)<br/>
    请参阅本文下图内容：<br/>

    ![屏幕截图：显示上述文章中的引用内容。](./media/data-flow-troubleshoot-connector-format/handshake-failure-cause-2.png)

#### <a name="recommendation"></a>建议
可以尝试使用复制活动解除阻止此问题。 

## <a name="azure-sql-database"></a>Azure SQL 数据库
 
### <a name="unable-to-connect-to-the-sql-database"></a>无法连接到 SQL 数据库

#### <a name="symptoms"></a>症状

Azure SQL 数据库可以在链接服务中的数据复制、数据集预览数据和测试连接中正常运行，但当相同的 Azure SQL 数据库用作数据流中的源或接收器时会失败，并显示诸如 `Cannot connect to SQL database: 'jdbc:sqlserver://powerbasenz.database.windows.net;..., Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access` 的错误

#### <a name="cause"></a>原因

Azure SQL 数据库服务器上的防火墙设置不正确，因此数据流运行时无法连接至该服务器。 目前，尝试使用数据流读取/写入 Azure SQL 数据库时，Azure Databricks 可用于构建 Spark 群集来运行作业，但不支持固定 IP 范围。 有关更多详细信息，请参阅 [Azure Integration Runtime IP 地址](./azure-integration-runtime-ip-addresses.md)。

#### <a name="recommendation"></a>建议

检查 Azure SQL 数据库的防火墙设置并将其设置为“允许访问 Azure 服务”，而不是设置固定 IP 范围。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-to-azure-service.png" alt-text="屏幕截图：显示如何在防火墙设置中允许访问 Azure 服务。"::: 

### <a name="syntax-error-when-using-queries-as-input"></a>使用查询作为输入时出现语法错误

#### <a name="symptoms"></a>症状

在 Azure SQL 的数据流源中使用查询作为输入时会失败，并显示以下错误消息：

`at Source 'source1': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax XXXXXXXX.`

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/error-detail.png" alt-text="屏幕截图显示了错误详细信息。"::: 

#### <a name="cause"></a>原因

数据流源中使用的查询应该能够作为子查询运行。 失败的原因是查询语法不正确，或者无法作为子查询运行。 可以在 SSMS 中运行以下查询进行验证：

`SELECT top(0) * from ($yourQuery) as T_TEMP`

#### <a name="recommendation"></a>建议

提供正确的查询，并首先在 SSMS 中测试该查询。

### <a name="failed-with-an-error-sqlserverexception-111212-operation-cannot-be-performed-within-a-transaction"></a>失败并显示错误：“SQLServerException：111212；无法在事务中执行操作。”

#### <a name="symptoms"></a>症状

在数据流中使用 Azure SQL 数据库作为接收器来预览数据、调试/触发运行和执行其他活动时，可能会发现作业失败并显示以下错误消息：

`{"StatusCode":"DFExecutorUserError","Message":"Job failed due to reason: at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction.","Details":"at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction."}`

#### <a name="cause"></a>原因
错误“`111212;Operation cannot be performed within a transaction.`”仅在 Synapse 专用 SQL 池中出现。 但你错误地将 Azure SQL 数据库用作连接器。

#### <a name="recommendation"></a>建议
确认 SQL 数据库是否为 Synapse 专用 SQL 池。 如果是，请使用 Azure Synapse Analytics 作为连接器，如下图所示。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/synapse-analytics-connector.png" alt-text="屏幕截图：显示 Azure Synapse Analytics 连接器。"::: 

### <a name="data-with-the-decimal-type-become-null"></a>十进制类型的数据为 null

#### <a name="symptoms"></a>症状

你想要将数据插入到 SQL 数据库的表格中。 如果数据包含十进制类型，并且需要插入到 SQL 数据库中具有十进制类型的列中，数据值可能会更改为 null。

如果进行预览，在先前阶段中，将显示如下图所示的值：

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-previous-stage.png" alt-text="屏幕截图：显示先前阶段的值。"::: 

在接收器阶段，该值将变为 null，如下图所示。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-sink-stage.png" alt-text="屏幕截图：显示接收器阶段的值。"::: 

#### <a name="cause"></a>原因
十进制类型具有小数位数和精度属性。 如果数据类型与接收器表中的数据类型不匹配，系统将验证目标十进制值是否大于原始十进制值，并且确保原始值不会在目标十进制值中溢出。 因此，该值将强制转换为 null。

#### <a name="recommendation"></a>建议
检查并比较 SQL 数据库中数据和表格之间的十进制类型，并将小数位数和精度更改为相同。

可以使用 toDecimal（IDecimal、小数位数、精度）确定原始数据是否可强制转换为目标小数位数和精度。 如果返回 null，则表示在插入时无法强制转换和进一步处理数据。

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="serverless-pool-sql-on-demand-related-issues"></a>无服务器池（按需 SQL）相关问题

#### <a name="symptoms"></a>症状
使用 Azure Synapse Analytics 时，链接服务实际上是 Synapse 无服务器池。 其前身为按需 SQL 池，可以通过包含 `ondemand` 的服务器名称（例如 `space-ondemand.sql.azuresynapse.net`）加以区分。 你可能会遇到多个独特的故障，如下所示：<br/>

1. 想要使用 Synapse 无服务器池作为接收器时，会遇到如下错误：<br/>
`Sink results in 0 output columns. Please ensure at least one column is mapped`
1. 在源中选择“启用暂存”时，会遇到错误 `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near 'IDENTITY'.`
1. 想要从外部表格提取数据时，会遇到错误 `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External table 'dbo' is not accessible because location does not exist or it is used by another process.`
1. 想要通过查询/从视图中经由无服务器池从 Cosmos DB 提取数据时，会遇到错误 `Job failed due to reason: Connection reset.`
1. 想要从视图中提取数据时，可能会遇到不同的错误。

#### <a name="cause"></a>原因
下文将分别说明这些症状的原因：
1. 无服务器池不能用作接收器。 其不支持将数据写入数据库。
1. 无服务器池不支持临时数据加载，因此不支持“启用暂存”。 
1. 你所使用的身份验证方法不具有对外部表格引用的外部数据源的正确权限。
1. Synapse 无服务器池中存在已知限制，阻止从数据流中提取 Cosmos DB 数据。
1. 视图是基于 SQL 语句的虚拟表。 根本原因在于视图语句内部。

#### <a name="recommendation"></a>建议

可以应用以下步骤来相应地解决问题。
1. 最好不要将无服务器池用作接收器。
1. 请勿在无服务器池的源中使用“启用暂存”。
1. 仅对外部表格数据具有权限的服务主体/托管标识才能进行查询。 对于在 ADF 中使用的身份验证方法，应向外部数据源授予“存储 Blob 数据参与者”权限。
    >[!Note]
    > 用户密码身份验证无法查询外部表格。 有关详细信息，请参阅[安全模型](../synapse-analytics/metadata/database.md#security-model)一文。

1. 可以使用复制活动从无服务器池中提取 Cosmos DB 数据。
1. 可以向工程支持团队提供创建视图的 SQL 语句，该团队可以帮助分析此语句是否命中身份验证问题或其他问题。


### <a name="load-small-size-data-to-data-warehouse-without-staging-is-slow"></a>在无暂存的情况下，将小型数据加载到 Data Warehouse 速度很慢 

#### <a name="symptoms"></a>症状
在无暂存的情况下，将小型数据加载到 Data Warehouse 时，需要很长时间才能完成。 例如，虽然数据大小为 2 MB，但需要一个多小时才能完成。 

#### <a name="cause"></a>原因
此问题是由行计数而非大小所引起。 行数以千计，每个插入都需要打包到独立的请求中，转到控制节点，启动新事务，获取锁定并重复转到分发节点。 大容量加载获取一次锁定，每个分发节点通过高效地批处理到内存执行插入操作。

如果仅以几条记录插入 2 MB，则速度会很快。 例如，如果每条记录为 500 kb * 4 行，则速度很快。 

#### <a name="recommendation"></a>建议
需要启用暂存以提高性能。


### <a name="read-empty-string-value--as-null-with-the-enable-staging"></a>使用启用暂存将空字符串值 ("") 读取为 NULL 

#### <a name="symptoms"></a>症状
在数据流中使用 Synapse 作为源来预览数据和调试/触发运行，并启用暂存以使用 PolyBase 时，如果列值包含空字符串值 (`""`)，则该值将更改为 null。

#### <a name="cause"></a>原因
数据流后端使用 Parquet 作为 PolyBase 格式，并且 Synapse SQL 池 Gen2 存在已知限制，会自动将空字符串值更改为 null。

#### <a name="recommendation"></a>建议
可以尝试使用以下方法解决此问题：
1. 如果数据大小不是很大，可以在源中禁用“启用暂存”，但性能将受到影响。
1. 如果需要启用暂存，可以使用 iifNull() 函数将特定列从 null 手动更改为空字符串值。

### <a name="managed-service-identity-error"></a>托管服务标识错误

#### <a name="symptoms"></a>症状
在数据流中使用 Synapse 作为源/接收器来预览数据、调试/触发运行等，启用暂存以使用 PolyBase，并创建临时存储的链接服务（Blob、Gen2 等）以使用托管标识 (MI) 身份验证时，作业可能会失败，并显示下图所示的错误： <br/>

![屏幕截图：显示服务标识错误。](./media/data-flow-troubleshoot-connector-format/service-identity-error.png)

#### <a name="error-message"></a>错误消息
`shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Managed Service Identity has not been enabled on this server. Please enable Managed Service Identity and try again.`

#### <a name="cause"></a>原因
1. 如果从 Synapse 工作区创建 SQL 池，则旧 SQL 池不支持使用 PolyBase 对临时存储进行 MI 身份验证。 
1. 如果 SQL 池是旧版 Data Warehouse (DWH)，则 SQL Server 的 MI 不会分配给临时存储。

#### <a name="recommendation"></a>建议
需要确认 SQL 池是否从 Synapse 工作区创建。

- 如果 SQL 池是从 Synapse 工作区创建，则需要重新注册工作区的 MI。 可以通过重新注册工作区的 MI，应用以下步骤来解决此问题：
    1. 在 Azure 门户中转到 Synapse 工作区。
    1. 转到“托管标识”边栏选项卡。
    1. 如果已选中“允许管道”选项，则必须取消选中此设置并保存。
    1. 选中“允许管道”选项并保存。

- 如果 SQL 池是旧版 DWH，则仅为 SQL Server 启用 MI，并将临时存储权限分配给 SQL Server 的 MI。 可以将[在 Azure SQL 数据库中针对服务器使用虚拟网络服务终结点和规则](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps)一文中的步骤作为示例进行参考。

### <a name="failed-with-an-error-sqlserverexception-not-able-to-validate-external-location-because-the-remote-server-returned-an-error-403"></a>失败并显示错误：“SQLServerException：无法验证外部位置，远程服务器返回错误：(403)”

#### <a name="symptoms"></a>症状

使用 SQLDW 作为接收器来触发并运行数据流活动时，活动可能会失败并显示诸如 `"SQLServerException: Not able to validate external location because the remote server returned an error: (403)"` 的错误

#### <a name="cause"></a>原因
1. 在 ADLS Gen2 帐户的身份验证方法中使用托管标识作为暂存时，cx 可能无法正确设置身份验证配置。
1. 如果使用 VNET 集成运行时，则需要在 ADLS Gen2 帐户的身份验证方法中使用托管标识作为暂存。 如果临时 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证。
1. 检查文件夹名称是否包含空格字符或其他特殊字符，例如 `Space " < > # % |`。
当前，Data Warehouse 复制命令不支持包含某些特殊字符的文件夹名称。

#### <a name="recommendation"></a>建议

对于原因 1，可以参考[在 Azure SQL 数据库中针对服务器使用虚拟网络服务终结点和规则 - 步骤](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps)一文来解决此问题。

对于原因 2，请使用以下选项之一进行解决：

- 选项 1：如果使用 VNET 集成运行时，则需要在 ADLS GEN 2 帐户的身份验证方法中使用托管标识作为暂存。

- 选项 2：如果临时 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证。 有关详细信息，请参阅[使用 PolyBase 进行暂存复制](./connector-azure-sql-data-warehouse.md#staged-copy-by-using-polybase)一文。

对于原因 3，请使用以下选项之一进行解决：

- 选项 1：重命名文件夹，并避免在文件夹名称中使用特殊字符。
- 选项 2：删除数据流脚本中的属性 `allowCopyCommand:true`，例如：

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/remove-allow-copy-command-true.png" alt-text="屏幕截图：显示如何删除“allowcopycommand：true”。"::: 


### <a name="failed-with-an-error-this-operation-is-not-permitted-on-a-non-empty-directory"></a>失败并显示错误：“不允许对非空目录执行此操作”

#### <a name="symptoms"></a>症状

在数据流中使用 Azure Synapse Analytics 作为接收器来预览数据、调试/触发运行或执行其他活动，并且启用暂存设置为 true 时，作业可能会失败并显示以下错误消息：

`DF-SYS-01 at Sink 'sink': Unable to stage data before write. Check configuration/credentials of storage.`<br/>
`org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: This operation is not permitted on a non-empty directory.`

#### <a name="cause"></a>原因
使用 Azure Blob 存储作为暂存链接服务，以链接到具有已启用分层命名空间的存储帐户，并且该帐户在链接服务中使用密钥身份验证。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-configuration.png" alt-text="屏幕截图：显示存储帐户配置。"::: 

#### <a name="recommendation"></a>建议
针对存储创建 Azure Data Lake Gen2 链接服务，并选择 Gen2 存储作为数据流活动中的暂存链接服务。

## <a name="common-data-model-format"></a>Common Data Model 格式

### <a name="modeljson-files-with-special-characters"></a>带有特殊字符的 Model.json 文件

#### <a name="symptoms"></a>症状 
你可能会遇到 model.json 文件的最终名称包含特殊字符的问题。  

#### <a name="error-message"></a>错误消息  
`at Source 'source1': java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: PPDFTable1.csv@snapshot=2020-10-21T18:00:36.9469086Z. ` 

#### <a name="recommendation"></a>建议  
替换文件名中的特殊字符，这将在 Synapse 中起作用，但在 ADF 中不起作用。  

### <a name="no-data-output-in-the-data-preview-or-after-running-pipelines"></a>数据预览中或在运行管道后没有数据输出

#### <a name="symptoms"></a>症状
将 manifest.json 用于 CDM 时，数据预览中或在运行管道后不会显示任何数据。 只显示标头。 可以在下图中查看此问题。<br/>

![显示无数据输出症状的屏幕截图。](./media/data-flow-troubleshoot-connector-format/no-data-output.png)

#### <a name="cause"></a>原因
清单文档描述 CDM 文件夹，例如，文件夹中有哪些实体、这些实体的引用以及与此实例对应的数据。 你的清单文档中缺少指示 ADF 在何处读取数据的 `dataPartitions` 信息，并且由于它是空文档，因此不会返回任何数据。 

#### <a name="recommendation"></a>建议
更新清单文档以获得 `dataPartitions` 信息，可以参考此示例清单文档来更新你的文档：[Common Data Model 元数据：引入清单示例清单文档](/common-data-model/cdm-manifest#example-manifest-document)。

### <a name="json-array-attributes-are-inferred-as-separate-columns"></a>JSON 数组属性被推断为单独的列

#### <a name="symptoms"></a>症状 
你可能会遇到 CDM 实体的一个属性（字符串类型）有一个 JSON 数组作为数据的问题。 遇到此数据时，ADF 错误地将数据推断为单独的列。 从下面的图片中可以看到，源 (msfp_otherproperties) 中显示的单个属性在 CDM 连接器的预览中被推断为一个单独的列。<br/> 

- 在 CSV 源数据（参阅第二列）中： <br/>

    ![显示 CSV 源数据中的属性的屏幕截图。](./media/data-flow-troubleshoot-connector-format/json-array-csv.png)

- 在 CDM 源数据预览中： <br/>

    ![显示 CDM 源数据中单独的列的屏幕截图。](./media/data-flow-troubleshoot-connector-format/json-array-cdm.png)

 
还可以尝试映射偏移列，并使用数据流表达式将此属性转换为数组。 但由于在读取时将此属性作一个单独的列读取，因此转换为数组不起作用。  

#### <a name="cause"></a>原因
此问题可能是由该列的 JSON 对象值中的逗号引起的。 因为数据文件应该是 CSV 文件，所以逗号表示它是列值的结尾。 

#### <a name="recommendation"></a>建议
若要解决此问题，需要使用双引号将 JSON 列括起来，并使用反斜杠 (`\`) 避免任何内部引号。 如此一来，可以将该列值的内容完全作为单列读取。  
  
>[!Note]
>CDM 不会通知列值的数据类型为 JSON，但会通知数据类型为字符串并按此进行分析。

### <a name="unable-to-fetch-data-in-the-data-flow-preview"></a>无法在数据流预览中提取数据

#### <a name="symptoms"></a>症状
你将 CDM 与由 Power BI 生成的 model.json 一起使用。 使用数据流预览来预览 CDM 数据时，会遇到错误：`No output data.`

#### <a name="cause"></a>原因
 由 Power BI 数据流生成的 model.json 文件的分区中存在以下代码。
```json
"partitions": [  
{  
"name": "Part001",  
"refreshTime": "2020-10-02T13:26:10.7624605+00:00",  
"location": "https://datalakegen2.dfs.core.windows.net/powerbi/salesEntities/salesPerfByYear.csv @snapshot=2020-10-02T13:26:10.6681248Z"  
}  
```
对于此 model.json 文件，问题是数据分区文件的命名架构中包含特殊字符，并且当前不存在带有“@”的支持文件路径。  

#### <a name="recommendation"></a>建议
请从数据分区文件名和 model.json 文件中删除 `@snapshot=2020-10-02T13:26:10.6681248Z`，然后重试。 

### <a name="the-corpus-path-is-null-or-empty"></a>语料库路径为 null 或空

#### <a name="symptoms"></a>症状
将数据流中的 CDM 用于模型格式时，无法预览数据，并遇到错误：`DF-CDM_005 The corpus path is null or empty`。 该错误如下图所示：  

![显示语料库路径错误的屏幕截图。](./media/data-flow-troubleshoot-connector-format/corpus-path-error.png)

#### <a name="cause"></a>原因
model.json 中的数据分区路径指向 Blob 存储位置，而非数据湖。 对于 ADLS Gen2，该位置的基 URL 应为 .dfs.core.windows.net。 

#### <a name="recommendation"></a>建议
为了解决此问题，可以参考文章：[ADF 向数据流添加了对内联数据集和 Common Data Model 的支持](https://techcommunity.microsoft.com/t5/azure-data-factory/adf-adds-support-for-inline-datasets-and-common-data-model-to/ba-p/1441798)。下面的图片展示了修复了此文中所述语料库路径错误的方法。

![显示如何修复语料库路径错误的屏幕截图。](./media/data-flow-troubleshoot-connector-format/fix-format-issue.png)

### <a name="unable-to-read-csv-data-files"></a>无法读取 CSV 数据文件

#### <a name="symptoms"></a>症状 
你使用内联数据集作为常见数据模型，将清单作为源，并提供了条目清单文件、根路径、实体名称和路径。 在清单中，有包含 CSV 文件位置的数据分区。 同时，实体架构和 CSV 架构完全相同，所有验证均成功。 但是，在数据预览中，只加载了架构而未加载数据，且数据不可见，如下图所示：

![显示无法读取数据文件的问题的屏幕截图。](./media/data-flow-troubleshoot-connector-format/unable-read-data.png)

#### <a name="cause"></a>原因
CDM 文件夹不分为逻辑模型和物理模型，并且其中只存在物理模型。 以下两篇文章介绍了差异：[逻辑定义](/common-data-model/sdk/logical-definitions)和[解析逻辑实体定义](/common-data-model/sdk/convert-logical-entities-resolved-entities)。<br/> 

#### <a name="recommendation"></a>建议
对于使用 CDM 作为源的数据流，请尝试使用逻辑模型作为实体引用，并使用描述物理解析实体位置和数据分区位置的清单。 可以在公共 CDM github 存储库 ([CDM-schemaDocuments](https://github.com/microsoft/CDM/tree/master/schemaDocuments)) 中看到一些逻辑实体定义示例<br/>

构建语料库的一个好的起点是复制架构文档文件夹（github 存储库中的该级别）中的文件，然后将这些文件放到一个文件夹中。 之后，可以使用存储库中预定义的逻辑实体之一（作为起点或参考点）来创建逻辑模型。<br/>

设置语料库后，建议将 CDM 用作数据流中的接收器，这样就可以正确地创建格式标准的 CDM 文件夹。 可以使用 CSV 数据库作为源，然后将其接收到所创建的 CDM 模型。

## <a name="csv-and-excel-format"></a>CSV 和 Excel 格式

### <a name="set-the-quote-character-to-no-quote-char-is-not-supported-in-the-csv"></a>CSV 中不支持将引号字符设置为“无引号字符”
 
#### <a name="symptoms"></a>症状

当引号字符设置为“无引号字符”时，CSV 中不支持以下几个问题：

1. 当引号字符设置为“无引号字符”时，多字符列分隔符不能以相同的字母开头和结尾。
2. 当引号字符设置为“无引号字符”时，多字符列分隔符不能包含转义字符：`\`。
3. 当引号字符设置为“无引号字符”时，列值不能包含行分隔符。
4. 如果列值包含列分隔符，则引号字符和转义字符不能同时为空（无引号且无转义）。

#### <a name="cause"></a>原因

下面分别举例说明了这些症状的原因：
1. 以相同字母开头和结尾。<br/>
`column delimiter: $*^$*`<br/>
`column value: abc$*^    def`<br/>
`csv sink: abc$*^$*^$*def ` <br/>
`will be read as "abc" and "^&*def"`<br/>

2. 多字符分隔符包含转义字符。<br/>
`column delimiter: \x`<br/>
`escape char:\`<br/>
`column value: "abc\\xdef"`<br/>
转义字符将对列分隔符或字符进行转义。

3. 列值包含行分隔符。 <br/>
`We need quote character to tell if row delimiter is inside column value or not.`

4. 引号字符和转义字符都为空，列值包含列分隔符。<br/>
`Column delimiter: \t`<br/>
`column value: 111\t222\t33\t3`<br/>
`It will be ambigious if it contains 3 columns 111,222,33\t3 or 4 columns 111,222,33,3.`<br/>

#### <a name="recommendation"></a>建议
当前无法解决第一个症状和第二个症状。 对于第三个和第四个症状，可以应用以下方法：
- 对于症状 3，请勿对多行 CSV 文件使用“无引号字符”。
- 对于症状 4，将引号或转义字符设置为非空，或者可以删除数据中的所有列分隔符。

### <a name="read-files-with-different-schemas-error"></a>读取具有不同架构的文件出错

#### <a name="symptoms"></a>症状

使用数据流读取具有不同架构的文件（例如 CSV 和 Excel 文件）时，数据流调试、沙盒或活动运行将失败。
- 对于 CSV，当文件架构不同时，存在数据未对齐的情况。 

    ![显示第一个架构错误的屏幕截图。](./media/data-flow-troubleshoot-connector-format/schema-error-1.png)

- 对于 Excel，文件架构不同时，会发生错误。

    ![显示第二个架构错误的屏幕截图。](./media/data-flow-troubleshoot-connector-format/schema-error-2.png)

#### <a name="cause"></a>原因

不支持读取数据流中具有不同架构的文件。

#### <a name="recommendation"></a>建议

如果仍要传输数据流中具有不同架构的文件（如 CSV 和 Excel 文件），可以使用以下方法解决此问题：

- 对于 CSV，需要手动合并不同文件的架构，以获取完整的架构。 例如，file_1 具有列 `c_1, c_2, c_3`，file_2 具有列 `c_3, c_4,... c_10`，因此合并的完整架构为 `c_1, c_2... c_10`。 然后使其他文件也有相同的完整架构（即使它没有数据），例如，file_x 只有列 `c_1, c_2, c_3, c_4`，请在文件中添加额外的列 `c_5, c_6, ... c_10`，以便其正常运行。

- 对于 Excel，你可以通过应用下列选项之一来解决此问题：

    - **选项-1**：需要手动合并不同文件的架构，以获取完整的架构。 例如，file_1 具有列 `c_1, c_2, c_3`，file_2 具有列 `c_3, c_4,... c_10`，因此合并的完整架构为 `c_1, c_2... c_10`。 然后使其他文件也有相同的架构（即使它没有数据），例如，file_x 与工作表“SHEET_1”只有列 `c_1, c_2, c_3, c_4`，请在工作表中添加额外的列 `c_5, c_6, ... c_10`，以便其正常运行。
    - **选项-2**：使用“范围（例如 A1:G100）+ firstRowAsHeader=false”，然后它可以从所有 Excel 文件加载数据，即使列名和计数不同。

## <a name="delta-format"></a>增量格式

### <a name="the-sink-does-not-support-the-schema-drift-with-upsert-or-update"></a>接收器不支持通过更新插入和更新实现架构偏差

#### <a name="symptoms"></a>症状
你可能会面临这样一个问题：映射数据流中的增量接收器不支持通过更新插入/更新实现架构偏差。 问题在于，当增量是映射数据流中的目标并且用户配置了更新插入/更新时，架构偏差将不起作用。 

如果在“初始”加载到增量后将列添加到源中，则后续作业将失败，并出现找不到新列的错误，在使用更改行进行更新插入/更新时会发生这种情况。 增量接收器似乎仅适用于插入操作。

#### <a name="error-message"></a>错误消息
`DF-SYS-01 at Sink 'SnkDeltaLake': org.apache.spark.sql.AnalysisException: cannot resolve target.BICC_RV in UPDATE clause given columns target. `

#### <a name="cause"></a>原因
这是增量格式的一个问题，由数据流运行时使用的 IO 增量库的限制造成。 此问题仍在修复中。

#### <a name="recommendation"></a>建议
要解决此问题，需要首先更新架构，然后写入代码。 可执行以下步骤： <br/>
1. 创建一个数据流，其中包含一个仅插入增量接收器，并使用合并架构选项来更新架构。 
1. 在步骤 1 之后，使用删除/更新插入/更新来修改目标接收器，而不更改架构。 <br/>



## <a name="snowflake"></a>Snowflake

### <a name="unable-to-connect-to-the-snowflake-linked-service"></a>无法连接到 Snowflake 链接服务

#### <a name="symptoms"></a>症状

在公用网络中创建 Snowflake 链接服务并使用自动解析集成运行时时，遇到以下错误。

`ERROR [HY000] [Microsoft][Snowflake] (4) REST request for URL https://XXXXXXXX.east-us- 2.azure.snowflakecomputing.com.snowflakecomputing.com:443/session/v1/login-request?requestId=XXXXXXXXXXXXXXXXXXXXXXXXX&request_guid=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` 

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/connection-fail-error.png" alt-text="显示连接失败错误的屏幕截图。"::: 

#### <a name="cause"></a>原因

尚未以 Snowflake 帐户文档中给定的格式（包括标识区域和云平台的其他段）应用帐户名，例如 `XXXXXXXX.east-us-2.azure`。 有关详细信息，请参阅文档：[链接服务属性](./connector-snowflake.md#linked-service-properties)。

#### <a name="recommendation"></a>建议

要解决此问题，请更改帐户名格式。 角色应为下图所示的角色之一，但默认值为“Public”。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/account-role.png" alt-text="显示帐户角色的屏幕截图。"::: 

### <a name="sql-access-control-error-insufficient-privileges-to-operate-on-schema"></a>SQL 访问控制错误：“权限不足，无法对架构进行操作”

#### <a name="symptoms"></a>症状

尝试在数据流的 Snowflake 源中使用“导入投影”、“数据预览”等时，遇到类似 `net.snowflake.client.jdbc.SnowflakeSQLException: SQL access control error: Insufficient privileges to operate on schema` 的错误。

#### <a name="cause"></a>原因

遇到此错误是由于配置错误。 使用数据流读取 Snowflake 数据时，运行时 Azure Databricks (ADB) 不会直接选择 Snowflake 的查询。 而是创建一个临时阶段，将数据从表拉取到该阶段，然后由 ADB 压缩和拉取数据。 此过程如下图所示。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/snowflake-data-read-model.png" alt-text="显示 Snowflake 数据读取模型的屏幕截图。"::: 

因此，在 ADB 中使用的用户/角色应具有在 Snowflake 中执行此操作所需的权限。 但用户/角色通常不具有该权限，因为该数据库是在该共享上创建的。 

#### <a name="recommendation"></a>建议
要解决此问题，可以创建不同的数据库，并在共享 DB 的基础上创建视图，以便从 ADB 访问该数据库。 有关更多详细信息，请参阅 [Snowflake](https://community.snowflake.com/s/question/0D50Z000095ktE4SAI/insufficient-privileges-to-operate-on-schema)。

### <a name="failed-with-an-error-snowflakesqlexception-ip-xxxx-is-not-allowed-to-access-snowflake-contact-your-local-security-administrator"></a>失败并出现错误：“SnowflakeSQLException: 不允许 IP x.x.x.x 访问 Snowflake。 请联系你的本地安全管理员”

#### <a name="symptoms"></a>症状

在 Azure 数据工厂中使用 Snowflake 时，可以成功使用 Snowflake 链接服务中的测试连接、Snowflake 数据集上的预览数据/导入架构，并使用它运行复制/查找/获取元数据或其他活动。 但在数据流活动中使用 Snowflake 时，可能会遇到类似 `SnowflakeSQLException: IP 13.66.58.164 is not allowed to access Snowflake. Contact your local security administrator.` 的错误

#### <a name="cause"></a>原因

Azure 数据工厂数据流不支持使用固定 IP 范围，可以参考 [Azure Integration Runtime IP 地址](./azure-integration-runtime-ip-addresses.md)了解更多详细信息。

#### <a name="recommendation"></a>建议

要解决此问题，可以通过以下步骤更改 Snowflake 帐户防火墙设置：

1. 可以从“服务标记 IP 范围下载链接”中获取服务标记的 IP 范围列表：[使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/ip-range-list.png" alt-text="屏幕截图：显示 IP 范围列表。"::: 

1. 如果在“southcentralus”区域中运行数据流，则需要允许从名称为“AzureCloud.southcentralus”的所有地址进行访问，例如：

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-with-name.png" alt-text="屏幕截图：显示如何允许从具有特定名称的所有地址进行访问。"::: 

### <a name="queries-in-the-source-does-not-work"></a>源中的查询不起作用

#### <a name="symptoms"></a>症状

尝试通过查询从 Snowflake 读取数据时，可能会遇到如下错误：

1. `SQL compilation error: error line 1 at position 7 invalid identifier 'xxx'`
2. `SQL compilation error: Object 'xxx' does not exist or not authorized.`

#### <a name="cause"></a>原因

遇到此错误是由于配置错误。

#### <a name="recommendation"></a>建议

对于 Snowflake，它在创建/定义时将以下规则应用于存储标识符，并在查询和其他 SQL 语句中解析它们：

当标识符（表名、架构名、列名等）不带引号时，默认情况下以大写形式存储和解析，并且不区分大小写。 例如：

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png" alt-text="显示不带引号的标识符示例的屏幕截图。"lightbox="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png"::: 

因为它不区分大小写，所以你可以随意使用以下查询来读取 Snowflake 数据，而结果是相同的：<br/>
- `Select MovieID, title from Public.TestQuotedTable2`<br/>
- `Select movieId, title from Public.TESTQUOTEDTABLE2`<br/>
- `Select movieID, TITLE from PUBLIC.TESTQUOTEDTABLE2`<br/>

当标识符（表名、架构名、列名等）带有双引号时，它的存储和解析方式与输入完全一致，包括大小写（因为区分大小写），你可以在下图中看到一个示例。 有关更多详细信息，请参阅此文档：[标识符要求](https://docs.snowflake.com/en/sql-reference/identifiers-syntax.html#identifier-requirements)。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png" alt-text="显示带双引号的标识符示例的屏幕截图。" lightbox="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png"::: 

由于区分大小写的标识符（表名、架构名、列名等）具有小写字符，因此在使用查询读取数据时必须在标识符两边使用引号，例如： <br/>

- Select "movieId", "title" from Public."testQuotedTable2"  

如果 Snowflake 查询出现错误，请按以下步骤检查某些标识符（表名、架构名、列名等）是否区分大小写：

1. 登录 Snowflake 服务器（`https://{accountName}.azure.snowflakecomputing.com/`，将 {accountName} 替换为你的帐户名）以检查标识符（表名、架构名、列名等）。

1. 创建用于测试和验证查询的工作表：
    - 运行 `Use database {databaseName}`，将 {databaseName} 替换为你的数据库名。
    - 使用表运行查询，例如 `select "movieId", "title" from Public."testQuotedTable2"`
    
1. 测试和验证 Snowflake 的 SQL 查询后，可以直接在数据流 Snowflake 源中使用它。

### <a name="the-expression-type-does-not-match-the-column-data-type-expecting-variant-but-got-varchar"></a>表达式类型与列数据类型不匹配，需要 VARIANT，但获得的是 VARCHAR 

#### <a name="symptoms"></a>症状

尝试将数据写入 Snowflake 表时，可能会遇到以下错误：

`java.sql.BatchUpdateException: SQL compilation error: Expression type does not match column data type, expecting VARIANT but got VARCHAR`

#### <a name="cause"></a>原因

输入数据的列类型为字符串，此类型不同于 Snowflake 接收器中相关列的 VARIANT 类型。

在新的 Snowflake 表中通过复杂架构（数组/映射/结构）存储数据时，数据流类型将自动转换为其物理类型 VARIANT。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/physical-type-variant.png" alt-text="显示表中的 VARIANT 类型的屏幕截图。"::: 

相关值以 JSON 字符串的形式存储，如下图所示。

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/json-string.png" alt-text="显示存储的 JSON 字符串的屏幕截图。"::: 

#### <a name="recommendation"></a>建议

对于 Snowflake VARIANT，它只能接受类型为结构、映射或数组的数据流值。 如果输入数据列的值为 JSON、XML 或其他字符串，请使用下列选项之一来解决此问题：

- **选项-1**：使用 Snowflake 作为接收器之前，使用[分析转换](./data-flow-parse.md)将输入数据列值转换为结构、映射或数组类型，例如：

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/parse-transformation.png" alt-text="显示分析转换的屏幕截图。"::: 

    > [!Note]
    > 默认情况下，具有 VARIANT 类型的 Snowflake 列的值在 Spark 中以字符串形式读取。

- **选项-2**：登录 Snowflake 服务器（`https://{accountName}.azure.snowflakecomputing.com/`，将 {accountName} 替换为你的帐户名），更改 Snowflake 目标表的架构。 通过在每个步骤下运行查询，应用以下步骤。
    1. 创建一个具有 VARCHAR 的新列来存储这些值。 <br/>
        ```SQL
        alter table tablename add newcolumnname varchar;
        ```    
    1. 将 VARIANT 类型的值复制到新列中。 <br/>
    
        ```SQL
        update tablename t1 set newcolumnname = t1."details"
        ```
    1. 删除未使用的 VARIANT 列。 <br/>
        ```SQL
        alter table tablename drop column "details";
        ```
    1. 将新列重命名为旧名称。 <br/>
        ```SQL
        alter table tablename rename column newcolumnname to "details";
        ```

## <a name="next-steps"></a>后续步骤
在故障排除时如需更多帮助，请参阅以下资源：

*  [排查 Azure 数据工厂中的映射数据流问题](data-flow-troubleshoot-guide.md)
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](/answers/topics/azure-data-factory.html)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
