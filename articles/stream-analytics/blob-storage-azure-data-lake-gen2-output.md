---
title: 来自 Azure 流分析的 Blob 存储和 Azure Data Lake Gen2 输出
description: 本文介绍作为 Azure 流分析输出的 Blob 存储和 Azure Data Lake Gen 2。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/7/2021
ms.openlocfilehash: 92361142fa2ac7758b16e26faa5e5df8f1da9bf6
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506864"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>来自 Azure 流分析的 Blob 存储和 Azure Data Lake Gen2 输出

Data Lake Storage Gen2 使 Azure 存储成为在 Azure 上构建企业 Data Lake 的基础。 Data Lake Storage Gen2 从一开始就设计为存储数千万亿字节的信息，同时保持数百千兆位的吞吐量，允许你轻松管理大量数据。 Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加分层命名空间。

Azure Blob 存储提供了一种经济高效且可扩展的解决方案，用于在云中存储大量非结构化数据。 有关 Blob 存储及其用法的简介，请参阅[使用 Azure 门户上传、下载和列出 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

## <a name="output-configuration"></a>输出配置

下表列出了用于创建 blob 或 ADLS Gen2 输出的属性名称及其说明。

| 属性名称       | 说明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 输出别名        | 查询中使用的友好名称，用于将查询输出定向到此 blob 存储。 |
| 存储帐户     | 存储帐户的名称（正在向该存储帐户发送输出）。               |
| 存储帐户密钥 | 与存储帐户关联的密钥。                              |
| 存储容器   | 对存储在 Azure Blob 服务中的 blob 进行逻辑分组。 将 blob 上传到 Blob 服务时，必须为该 blob 指定一个容器。 |
| 路径模式 | 可选。 用于写入指定容器中的 blob 的文件路径模式。 <br /><br /> 在路径模式中，可以选择使用数据时间变量的一个或多个实例指定 blob 写入的频率： <br /> {date}、{time} <br /><br />可以使用自定义 blob 分区从事件数据中指定一个自定义 {field} 名称来对 blob 进行分区。 字段名称是字母数字，并且可以包含空格、连字符和下划线。 对自定义字段的限制包括以下内容： <ul><li>字段名称不区分大小写。 例如，服务无法区分列“ID”和列“id”。</li><li>不允许嵌套字段。 在作业查询中改用别名来“平展”字段。</li><li>不能使用表达式作为字段名称。</li></ul> <br />通过此功能可以在路径中使用自定义日期/时间格式说明符配置。 一次只能指定一个自定义日期和时间格式，并将其用 {datetime:\<specifier>} 关键字括起来。 \<specifier> 允许的输入为 yyyy、MM、M、dd、d、HH、H、mm、m、ss 或 s。 可以在路径中多次使用 {datetime:\<specifier>} 关键字以构成自定义的日期/时间配置。 <br /><br />示例: <ul><li>示例 1：cluster1/logs/{date}/{time}</li><li>示例 2：cluster1/logs/{date}</li><li>示例 3：cluster1/{client_id}/{date}/{time}</li><li>示例 4：cluster1/{datetime:ss}/{myField}，其中查询是：SELECT data.myField AS myField FROM Input;</li><li>示例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。 [System.Timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) 是用于所有基于时间的分区的时间。<br /><br />文件命名采用以下约定： <br /><br />{路径前缀模式}/schemaHashcode_Guid_Number.extension<br /><br /> 此处的 Guid 表示分配给为写入 blob 文件而创建的内部编写器的唯一标识符。 该数字表示 blob 块的索引。 <br /><br /> 示例输出文件：<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />有关此功能的详细信息，请参阅 [Azure 流分析自定义 blob 输出分区](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 示例：YYYY/MM/DD |
| 时间格式 | 可选。 如果在前缀路径中使用时间令牌，可指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV、Avro 和 Parquet。 |
|最小行数 |每批的最小行数。 对于 Parquet，每批都将创建一个新文件。 当前默认值为 2000 行，允许的最大值为 10000 行。|
|最长时间 |每批的最长等待时间。 在此时间后，即使不满足最小行数要求，也会将该批写入输出。 当前默认值为 1 分钟，允许的最大值为 2 小时。 如果 blob 输出具有路径模式频率，则等待时间不能超出分区时间范围。|
| 编码    | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。 |
| 分隔符   | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式      | 仅适用于 JSON 序列化。 分隔行指定通过新行分隔各个 JSON 对象，从而格式化输出。 如果选择“行分隔”，则读取 JSON 时，一次读取一个对象。 整个内容本身将不是有效的 JSON。 数组指定输出会被格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。 |

## <a name="blob-output-files"></a>Blob 输出文件

使用 blob 存储作为输出时，在以下情况下 blob 中创建一个新文件：

* 如果文件超出了允许的最大块数（目前为 50,000）。 可达到允许的最大块数，但不能达到允许的最大 blob 大小。 例如，如果输出率很高，则可以看到每个块的字节更多，并且文件大小会更大。 输出率较低时，每个块都有较少的数据，且文件大小较小。
* 如果输出中出现架构更改，输出格式也需要固定的架构（CSV、Avro 和 Parquet）。
* 如果作业重新启动，可选择在外部由用户停止或启动，或在内部进行系统维护或错误恢复。
* 如果对查询进行完全分区，会为每个输出分区创建新文件。
* 如果用户删除存储帐户的文件或容器。
* 如果使用路径前缀模式对输出进行了时间分区，当查询移动到下一个小时后，会使用新的 blob。
* 如果按自定义字段对输出进行分区，则每个分区键都会创建新的 blob（如果不存在）。
* 如果按照自定义字段对输出进行分区（其中分区键基数超过 8,000），并为每个分区键创建一个新的 blob。

## <a name="partitioning"></a>分区

对于分区键，请在路径模式中使用事件字段中的 {date} 和 {time} 标记。 选择日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 HH 用于时间格式。 可以通过单个自定义事件属性 {fieldname} 或 {datetime:\<specifier>} 对 Blob 输出进行分区。 输出编写器的数量与[可完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区一致。

## <a name="output-batch-size"></a>输出批大小

有关最大消息大小，请参阅 [Azure 存储限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 最大 Blob 块大小为 4 MB，最大 Blob 块计数为 50,000。

## <a name="limitations"></a>限制

* 如果在路径模式中使用“/”（例如“/folder2/folder3”），则会创建空文件夹，并且这些文件夹在存储资源管理器中不可见
* 在不需要新 blob 文件的情况下，流分析会追加到同一文件。 请注意，如果将事件网格之类的 Azure 服务配置为在 blob 文件更新时触发，这可能会导致生成额外的触发器

## <a name="next-steps"></a>后续步骤

* [使用托管标识对 Azure Blob 存储的 Azure 流分析作业进行身份验证](blob-output-managed-identity.md)
* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
