---
title: Azure 数据工厂中的增量格式
description: 使用增量格式转换和移动来自 delta lake 的数据
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: makromer
ms.openlocfilehash: b5f344d97813e5f8bc7099631b56760ca2115f1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637788"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure 数据工厂中的增量格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文重点介绍如何使用增量格式从/向存储在 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) 或 [Azure Blob 存储](connector-azure-blob-storage.md)中的 delta lake 复制数据。 该连接器作为映射数据流中的[内联数据集](data-flow-source.md#inline-datasets)提供，同时用作源和接收器。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>映射数据流属性

该连接器作为映射数据流中的[内联数据集](data-flow-source.md#inline-datasets)提供，同时用作源和接收器。

### <a name="source-properties"></a>源属性

下表列出了 delta 源支持的属性。 可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `delta` | 是 | `delta` | format |
| 文件系统 | delta lake 的容器/文件系统 | 是 | 字符串 | fileSystem |
| 文件夹路径 | delta lake 的直接 | 是 | 字符串 | folderPath |
| 压缩类型 | delta 表的压缩类型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 压缩级别 | 选择压缩是否尽快完成，或者是否应该以最佳方式压缩生成的文件。 | 如果指定了 `compressedType`，则为必需项。 | `Optimal` 或 `Fastest` | compressionLevel |
| 按时间顺序查看 | 选择是否查询 delta 表的旧快照 | 否 | 按时间戳查询：Timestamp <br> 按版本查询：Integer | timestampAsOf <br> versionAsOf |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>导入架构

Delta 仅作为内联数据集提供，且默认情况下没有关联架构。 若要获取列元数据，请单击“投影”选项卡中的“导入架构”按钮 。这样你可以引用语料库指定的列名称和数据类型。 若要导入架构，[数据流调试会话](concepts-data-flow-debug-mode.md)必须处于活动状态，还必须具有可以指向的现有 CDM 实体定义文件。
 

### <a name="delta-source-script-example"></a>Delta 源脚本示例

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>接收器属性

下表列出了 delta 接收器支持的属性。 可以在“设置”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `delta` | 是 | `delta` | format |
| 文件系统 | delta lake 的容器/文件系统 | 是 | 字符串 | fileSystem |
| 文件夹路径 | delta lake 的直接 | 是 | 字符串 | folderPath |
| 压缩类型 | delta 表的压缩类型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 压缩级别 | 选择压缩是否尽快完成，或者是否应该以最佳方式压缩生成的文件。 | 如果指定了 `compressedType`，则为必需项。 | `Optimal` 或 `Fastest` | compressionLevel |
| 清空 | 以小时为单位指定旧版本表的保留期阈值。 小于等于 0 的值默认为 30 天 | 是 | Integer | vacuum |
| Update 方法 | 指定在 delta lake 上允许哪些更新操作。 对于不插入的方法，需要执行前面的更改行转换才能标记行。 | 是 | `true` 或 `false` | deletable <br> insertable <br> 可更新 <br> merge |
| 优化写入 | 通过优化 Spark 执行程序中的内部随机选择，使写入操作实现更高的吞吐量。 因此，你可能会注意到较大的分区和文件比较少 | 否 | `true` 或 `false` | optimizedWrite: true |
| 自动压缩 | 完成任何写入操作后，Spark 会自动执行 ```OPTIMIZE``` 命令以重新组织数据，并在必要时生成更多分区，以便在将来获得更好的读取性能 | 否 | `true` 或 `false` |    autoCompact: true |

### <a name="delta-sink-script-example"></a>Delta 接收器脚本示例

关联的数据流脚本为：

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>已知限制

写入 delta 接收器时，存在一个已知的限制，即不会在监视输出中返回写入的行数。

## <a name="next-steps"></a>后续步骤

* 在映射数据流中创建[源转换](data-flow-source.md)。
* 在映射数据流中创建[接收器转换](data-flow-sink.md)。
* 创建[更改行转换](data-flow-alter-row.md)，将行标记为插入、更新、更新插入或删除。
