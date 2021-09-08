---
title: 排查复制活动的性能问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中复制活动的性能问题。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: cea4999752d416f36f435ba88403fd9dc735f689
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255804"
---
# <a name="troubleshoot-copy-activity-performance"></a>排查复制活动的性能问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何排查 Azure 数据工厂中复制活动的性能问题。 

运行复制活动后，可以在[复制活动监视](copy-activity-monitoring.md)视图中收集运行结果和性能统计信息。 下面是一个示例。

![监视复制活动运行详细信息](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>性能优化提示

在某些情况下，运行复制活动时，顶部会显示“性能优化提示”，如上述示例所示。 这些提示告知服务针对此特定复制运行识别到的瓶颈，并建议如何提高复制吞吐量。 请尝试根据建议进行更改，然后再次运行复制。

作为参考，当前性能优化提示针对以下情况提供了建议：

| Category              | 性能优化提示                                      |
| --------------------- | ------------------------------------------------------------ |
| 特定于数据存储   | 将数据载入 Azure Synapse Analytics：建议使用 PolyBase；如果 PolyBase 不可用，则使用 COPY 语句。 |
| &nbsp;                | 从/向 **Azure SQL 数据库** 复制数据：当 DTU 的利用率较高时，建议升级到更高的层。 |
| &nbsp;                | 从/向 **Azure Cosmos DB** 复制数据：当 RU 的利用率较高时，建议升级到更大的 RU。 |
|                       | 从 SAP 表复制数据：复制大量数据时，建议利用 SAP 连接器的分区选项启用并行加载并增加最大分区数。 |
| &nbsp;                | 从 **Amazon Redshift** 引入数据：如果未使用 UNLOAD，建议使用它。 |
| 数据存储限制 | 如果在复制期间数据存储限制了一些读/写操作，则建议检查并增大数据存储允许的请求速率，或减小并发工作负荷。 |
| 集成运行时  | 如果使用了 **自承载集成运行时 (IR)** ，而复制活动在队列中长时间等待，直到 IR 提供了用于执行该活动的资源，则建议横向/纵向扩展 IR。 |
| &nbsp;                | 如果使用了非最佳区域中的 **Azure Integration Runtime**，导致读/写速度缓慢，则建议配置为使用另一区域中的 IR。 |
| 容错       | 如果配置了容错并跳过不兼容的行，导致性能变慢，则建议确保源和接收器数据兼容。 |
| 暂存复制           | 如果配置了分阶段复制，但此方法对于源-接收器对不起作用，则建议删除此方法。 |
| 恢复                | 如果复制活动已从上一故障点恢复，但你在完成原始运行后正好更改了 DIU 设置，请注意，新的 DIU 设置不会生效。 |

## <a name="understand-copy-activity-execution-details"></a>了解复制活动执行详细信息

复制活动监视视图底部的执行详细信息和持续时间描述了复制活动所要经历的重要阶段（请参阅本文开头的示例），这对于排查复制性能问题特别有用。 复制运行的瓶颈就是持续时间最长的那个运行。 请参阅下表中每个阶段的定义，并了解如何使用此类信息[排查 Azure IR 中的复制活动的问题](#troubleshoot-copy-activity-on-azure-ir)和[排查自承载 IR 中的复制活动的问题](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 阶段           | 说明                                                  |
| --------------- | ------------------------------------------------------------ |
| 队列           | 复制活动在集成运行时中实际启动之前所消逝的时间。 |
| 复制前脚本 | 复制活动在 IR 中启动之后、在接收器数据存储中执行完复制前脚本之前所消逝的时间。 为数据库接收器配置复制前脚本时适用，例如，将数据写入 Azure SQL 数据库会在复制新数据之前执行清理。 |
| 传输        | 完成前一步骤之后、在 IR 将所有数据从源传输到接收器之前所消逝的时间。 <br/>请注意，传输中的子步骤会并行运行，某些操作（例如，分析/生成文件格式）现在未显示。<br><br/>- **距第一字节的时间：** 在前一步骤结束之后、IR 从源数据存储收到第一个字节之前所经过的时间。 适用于不是基于文件的源。<br>- **列出源：** 枚举源文件或数据分区所花费的时间。 后者适用于为数据库源配置分区选项时，例如，从 Oracle/SAP HANA/Teradata/Netezza 等数据库复制数据时。<br/>-**从源中读取：** 从源数据存储检索数据所花费的时间。<br/>- **写入接收器：** 将数据写入接收器数据存储所花费的时间。 请注意，某些连接器（包括 Azure 认知搜索、Azure 数据资源管理器、Azure 表存储、Oracle、SQL Server、Common Data Service、Dynamics 365、Dynamics CRM、Salesforce/Salesforce 服务云）目前没有此指标。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>排查 Azure IR 中的复制活动的问题

遵循[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划并执行性能测试。 

当复制活动性能不符合预期时，若要排查 Azure Integration Runtime 中运行的单个复制活动的问题，在看到复制监视视图中显示了[性能优化提示](#performance-tuning-tips)的情况下，请应用建议并重试。 否则，请 [了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间 **最长**，并应用以下指导以提升复制性能：

- **“复制前脚本”的持续时间较长：** 表示接收器数据库中运行的复制前脚本花费了较长时间来完成。 优化指定的复制前脚本逻辑，以增强性能。 如果在改进脚本方面需要更多的帮助，请与数据库团队联系。

- **“传输 - 距第一字节的时间”的工作持续时间较长：** 表示源查询花费了较长时间来返回任何数据。 检查并优化查询或服务器。 如需更多帮助，请与数据存储团队联系。

- **“传输 - 列出源”的工作持续时间较长：** 表示枚举源文件或源数据库数据分区的速度缓慢。
  - 从基于文件的源复制数据时，如果对文件夹路径或文件名使用 **通配符筛选器**（`wildcardFolderPath` 或 `wildcardFileName`），或使用 **文件上次修改时间筛选器**（`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`），请注意，此类筛选器会导致复制活动在客户端中列出指定文件夹下的所有文件，然后应用筛选器。 此类文件枚举可能会变成瓶颈，尤其是只有少量的文件符合筛选规则时。

    - 检查是否可以[基于按日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这不会在“列出源”端带来负担。

    - 检查是否可以改用数据存储的本机筛选器，具体说来就是 Amazon S3/Azure Blob 存储/Azure 文件存储的“前缀”，以及 ADLS Gen1 的“listAfter/listBefore” 。 这些筛选器是一个数据存储服务器端筛选器，其性能要好得多。

    - 考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 解决方案模板，其中提供了一般性的示例。

  - 检查服务是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 使用同一源数据存储区域或者与之靠近的区域中的 Azure IR。

- **“传输 - 从源读取”的工作持续时间较长：** 

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，从 [Amazon Redshift](connector-amazon-redshift.md) 复制数据时，请配置为使用 Redshift UNLOAD。

  - 检查服务是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果复制模式支持 4 个以上的数据集成单位 (DIU) - 请参阅[此部分](copy-activity-performance-features.md#data-integration-units)中的详细信息，一般情况下，可以尝试增加 DIU 以获得更好的性能。 

    - 否则，请考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 或[使用控制表进行批量复制](solution-template-bulk-copy-with-control-table.md)解决方案模板，其中提供了一般性的示例。

  - 使用同一源数据存储区域中或者与之靠近的区域中的 Azure IR。

- **“传输 - 写入接收器”的工作持续时间较长：**

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，将数据复制到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) 时，请使用 PolyBase 或 COPY 语句。 

  - 检查服务是否报告了接收器中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果复制模式支持 4 个以上的数据集成单位 (DIU) - 请参阅[此部分](copy-activity-performance-features.md#data-integration-units)中的详细信息，一般情况下，可以尝试增加 DIU 以获得更好的性能。 

    - 否则，请逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。

  - 使用同一接收器数据存储区域中或者与之靠近的区域中的 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>排查自承载 IR 中的复制活动的问题

遵循[性能优化步骤](copy-activity-performance.md#performance-tuning-steps)为方案规划并执行性能测试。 

当复制性能不符合预期时，若要排查 Azure Integration Runtime 中运行的单个复制活动的问题，在看到复制监视视图中显示了[性能优化提示](#performance-tuning-tips)的情况下，请应用建议并重试。 否则，请 [了解复制活动执行详细信息](#understand-copy-activity-execution-details)，检查哪个阶段的持续时间 **最长**，并应用以下指导以提升复制性能：

- **“队列”持续时间较长：** 表示复制活动在队列中长时间等待，直到自承载 IR 提供了用于执行该活动的资源。 检查 IR 容量和使用率，并根据工作负荷进行[纵向或横向扩展](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

- **“传输 - 距第一字节的时间”的工作持续时间较长：** 表示源查询花费了较长时间来返回任何数据。 检查并优化查询或服务器。 如需更多帮助，请与数据存储团队联系。

- **“传输 - 列出源”的工作持续时间较长：** 表示枚举源文件或源数据库数据分区的速度缓慢。

  - 检查自承载 IR 计算机是否以较低的延迟连接到源数据存储。 如果源位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 从基于文件的源复制数据时，如果对文件夹路径或文件名使用 **通配符筛选器**（`wildcardFolderPath` 或 `wildcardFileName`），或使用 **文件上次修改时间筛选器**（`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`），请注意，此类筛选器会导致复制活动在客户端中列出指定文件夹下的所有文件，然后应用筛选器。 此类文件枚举可能会变成瓶颈，尤其是只有少量的文件符合筛选规则时。

    - 检查是否可以[基于按日期时间分区的文件路径或名称复制文件](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 这不会在“列出源”端带来负担。

    - 检查是否可以改用数据存储的本机筛选器，具体说来就是 Amazon S3/Azure Blob 存储/Azure 文件存储的“前缀”，以及 ADLS Gen1 的“listAfter/listBefore” 。 这些筛选器是一个数据存储服务器端筛选器，其性能要好得多。

    - 考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)或[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 解决方案模板，其中提供了一般性的示例。

  - 检查服务是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

- **“传输 - 从源读取”的工作持续时间较长：** 

  - 检查自承载 IR 计算机是否以较低的延迟连接到源数据存储。 如果源位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 检查自承载 IR 计算机是否具有足够的入站带宽，可以有效地读取和传输数据。 如果源数据存储位于 Azure 中，你可以使用[此工具](https://www.azurespeed.com/Azure/Download)检查下载速度。

  - 在 Azure 门户 -> 数据工厂或 Synapse 工作区 -> 概述页面，检查自承载 IR 的 CPU 和内存使用趋势。 如果 CPU 使用率较高或可用内存不足，请考虑[纵向/横向扩展 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如：

    - 从 [Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[Teradata](connector-teradata.md#teradata-as-source)、[SAP HANA](connector-sap-hana.md#sap-hana-as-source)、[SAP Table](connector-sap-table.md#sap-table-as-source) 和 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) 复制数据时，请启用数据分区选项以并行复制数据。

    - 从 [HDFS](connector-hdfs.md)复制数据时，请配置为使用 DistCp。

    - 从 [Amazon Redshift](connector-amazon-redshift.md) 复制数据时，请配置为使用 Redshift UNLOAD。

  - 检查服务是否报告了源中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 检查复制源和接收器模式： 

    - 如果从已启用分区选项的数据存储复制数据，请考虑逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。

    - 否则，请考虑将单个大型数据集拆分为多个小型数据集，并让每个并发运行的复制作业处理一部分数据。 为此，可以使用 Lookup/GetMetadata + ForEach + Copy。 请参阅[从多个容器复制文件](solution-template-copy-files-multiple-containers.md)、[将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md) 或[使用控制表进行批量复制](solution-template-bulk-copy-with-control-table.md)解决方案模板，其中提供了一般性的示例。

- **“传输 - 写入接收器”的工作持续时间较长：**

  - 采用特定于连接器的数据加载最佳做法（如果适用）。 例如，将数据复制到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) 时，请使用 PolyBase 或 COPY 语句。 

  - 检查自承载 IR 计算机是否以较低的延迟连接到接收器数据存储。 如果接收器位于 Azure 中，你可以使用[此工具](http://www.azurespeed.com/Azure/Latency)检查自承载 IR 计算机与 Azure 区域之间的连接延迟，延迟值越小越好。

  - 检查自承载 IR 计算机是否具有足够的出站带宽，可以有效地传输和写入数据。 如果接收器数据存储位于 Azure 中，你可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)检查上传速度。

  - 在 Azure 门户 -> 数据工厂或 Synapse 工作区 -> 概述页面，检查自承载 IR 的 CPU 和内存使用趋势。 如果 CPU 使用率较高或可用内存不足，请考虑[纵向/横向扩展 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

  - 检查服务是否报告了接收器中的任何限制错误，或者数据存储是否处于高利用率状态。 如果是，请减少数据存储中的工作负荷，或者尝试联系数据存储管理员来提高限制或增加可用资源。

  - 考虑逐步优化[并行复制](copy-activity-performance-features.md)，同时请注意，过多的并行复制可能会进一步损害性能。


## <a name="connector-and-ir-performance"></a>连接器和 IR 性能 

本部分探讨特定连接器类型或集成运行时的一些性能故障排除指南。

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>使用 Azure IR 与 Azure VNet IR 时，活动执行时间会有所不同

当数据集基于不同 Integration Runtime 时，活动执行时间会有所不同。

- **症状**：只需在数据集中切换“链接服务”下拉列表就可以执行相同的管道活动，但运行时间会明显不同。 当数据集基于托管虚拟网络集成运行时时，运行所需的平均时间要比基于默认集成运行时时更长。  

- **原因**：检查管道运行的详细信息，可以看到慢速管道在托管 VNet（虚拟网络）IR 上运行，而正常管道在 Azure IR 上运行。 按照设计，托管 VNet IR 的队列时间比 Azure IR 长，因为我们不会为每个服务实例都保留一个计算节点，因此每个复制活动都需要预热后才能启动，并且主要在 VNet 联接而不是在 Azure IR 上发生。 

    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>在将数据加载到 Azure SQL 数据库时性能较低

- **症状**：在将数据复制到 Azure SQL 数据库时速度变慢。

- **原因：** 此问题的根本原因主要由 Azure SQL 数据库端的瓶颈触发。 下面是一些可能的原因：

    - Azure SQL 数据库层不够高。

    - Azure SQL 数据库 DTU 使用率接近 100%。 可以[监视性能](../azure-sql/database/monitor-tune-overview.md)并考虑将 Azure SQL 数据库层升级。

    - 未正确设置索引。 请在加载数据之前先删除所有索引，并在加载完成之后再重新创建索引。

    - WriteBatchSize 不够大，无法容纳架构行大小。 若要解决此问题，请尝试增大该属性。

    - 使用的是存储过程，而不是批量插入，这会使性能更差。 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>分析大型 Excel 文件时超时或性能较低

- **症状**：

    - 在创建 Excel 数据集并从连接/存储导入架构、预览数据、列出或刷新工作表时，如果 Excel 文件很大，则可能会出现超时错误。

    - 在使用复制活动将大型 Excel 文件（不小于 100 MB）中的数据复制到其他数据存储时，可能会遇到性能低下或 OOM 问题。

- **原因**： 

    - 对于导入架构、预览数据以及在 Excel 数据集上列出工作表等操作，超时为 100 秒并且是静态的。 对于大型 Excel 文件，这些操作可能无法在超时值内完成。

    - 复制活动将整个 Excel 文件读入内存，然后查找指定的工作表和单元格来读取数据。 此行为是由服务使用的基础 SDK 导致的。

- **解决方法**： 

    - 对于导入架构，你可以生成一个较小的示例文件（原始文件的一部分），并选择“从示例文件导入架构”而不是“从连接/存储导入架构”。

    - 若要列出工作表，可以改为在工作表下拉框中单击“编辑”并输入工作表名称/索引。

    - 若要将大型 Excel 文件 (>100 MB) 复制到其他存储，可以使用支持流式读取且性能更好的数据流 Excel 源。
    
## <a name="other-references"></a>其他参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure Blob 存储：[Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)和 [Blob 存储的性能与可伸缩性查检表](../storage/blobs/storage-performance-checklist.md)。
* Azure 表存储：[表存储的可伸缩性和性能目标](../storage/tables/scalability-targets.md)和[表存储的性能与可伸缩性查检表](../storage/tables/storage-performance-checklist.md)。
* Azure SQL 数据库：可[监视性能](../azure-sql/database/monitor-tune-overview.md)并检查数据库事务单位 (DTU) 百分比。
* Azure Synapse Analytics：其功能以数据仓库单位 (DWU) 衡量。 请参阅[管理 Azure Synapse Analytics 中的计算能力（概述）](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* SQL Server：[性能监视和优化](/sql/relational-databases/performance/monitor-and-tune-for-performance)。
* 本地文件服务器：[文件服务器性能优化](/previous-versions//dn567661(v=vs.85))。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [复制活动性能优化功能](copy-activity-performance-features.md)
- [将数据从数据湖或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
