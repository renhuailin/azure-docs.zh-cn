---
title: 排查 Parquet 格式连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Parquet 格式连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 64a49592609b2cb7fd262264bb9802de58db5f04
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390383"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Parquet 格式连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂和 Azure Synapse 中常见的 Parquet 格式连接器问题。

## <a name="error-code-parquetjavainvocationexception"></a>错误代码：ParquetJavaInvocationException

- **消息**：`An error occurred when invoking java, message: %javaException;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串时“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，这通常是 Integration Runtime 旧版本中的内存管理问题。 | 如果使用的是自承载 IR，而且版本低于 3.20.7159.1，建议升级到最新版本。 |
    | 如果错误消息包含字符串“java.lang.OutOfMemory”，则集成运行时没有足够的资源来处理文件。 | 限制集成运行时中的并发运行。 对于自承载 IR，请纵向扩展到具有 8 GB 或更大内存的强大计算机。 |
    | 如果错误消息包含字符串“NullPointerReference”，则可能是暂时性错误。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |

## <a name="error-code-parquetinvalidfile"></a>错误代码：ParquetInvalidFile

- **消息**：`File is not a valid Parquet file.`

- **原因**：这是一个 Parquet 文件问题。

- **建议**：检查输入是否为有效的 Parquet 文件。

## <a name="error-code-parquetnotsupportedtype"></a>错误代码：ParquetNotSupportedType

- 消息：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**：Azure 数据工厂和 Synapse 管道不支持 Parquet 格式。

- **建议**：通过转到 [复制活动支持的文件格式和压缩编解码器](./supported-file-formats-and-compression-codecs.md)来仔细检查源数据。

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>错误代码：ParquetMissedDecimalPrecisionScale

- **消息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：分析了数字的精度和小数位数，但未提供此类信息。

- **建议**：源没有返回正确的精度和小数位数信息。 有关信息，请查看问题列。

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>错误代码：ParquetInvalidDecimalPrecisionScale

- 消息：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因：** 架构无效。

- **建议**：检查问题列的精度和小数位数。

## <a name="error-code-parquetcolumnnotfound"></a>错误代码：ParquetColumnNotFound

- **消息**：`Column %column; does not exist in Parquet file.`

- **原因**：源架构与接收器架构不匹配。

- **建议**：检查活动中的映射。 确保源列可映射到正确的接收器列。

## <a name="error-code-parquetinvaliddataformat"></a>错误代码：ParquetInvalidDataFormat

- **消息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：数据无法转换为 mappings.source 中指定的类型。

- **建议**：仔细检查源数据，或在复制活动列映射中为此列指定正确的数据类型。 有关详细信息，请参阅[活动支持的文件格式和压缩编解码器](./supported-file-formats-and-compression-codecs.md)。

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>错误代码：ParquetDataCountNotMatchColumnCount

- **消息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列计数和接收器列计数不匹配。

- **建议**：仔细检查，确保源列计数与“映射”中的接收器列计数相同。

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>错误代码：ParquetDataTypeNotMatchColumnType

- **消息**：`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **原因**：源中的数据无法转换为接收器中定义的类型。

- **建议**：在 mapping.sink 中指定正确的类型。

## <a name="error-code-parquetbridgeinvaliddata"></a>错误代码：ParquetBridgeInvalidData

- **消息**：`%message;`

- **原因**：数据值超出了限制。

- **建议**：重试操作。 如果问题仍然存在，请联系我们。

## <a name="error-code-parquetunsupportedinterpretation"></a>错误代码：ParquetUnsupportedInterpretation

- **消息**：`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**：此方案不受支持。

- **建议**：“ParquetInterpretFor”不应为“sparkSql”。

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>错误代码：ParquetUnsupportFileLevelCompressionOption

- **消息**：`File level compression is not supported for Parquet.`

- **原因**：此方案不受支持。

- **建议**：删除有效负载中的“CompressionType”。

## <a name="error-code-usererrorjniexception"></a>错误代码：UserErrorJniException

- **消息**：`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**：无法创建 Java 虚拟机 (JVM)，因为设置了一些非法（全局）参数。

- **建议**：登录到托管自承载 IR 的每个节点的计算机。 检查确保系统变量设置正确，如下所示：`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`。 重启所有 IR 节点，然后重新运行该管道。

## <a name="arithmetic-overflow"></a>算术溢出

- **故障描述**：复制 Parquet 文件时出现错误消息：`Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**：将文件从 Oracle 复制到 Parquet 时，当前仅支持精度 <= 38 且整数部分的长度 <= 20 的十进制数。 

- **解决方法**：若要进行解决，可将出现此问题的所有列都转换为 VARCHAR2。

## <a name="no-enum-constant"></a>无枚举常量

- **故障描述**：将数据复制为 Parquet 格式时，出现以下错误消息：`java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 或 `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`。

- **原因**： 

    此问题可能是由于列名中的空格或不受支持的特殊字符（例如 ,;{}()\n\t=）导致的，因为 Parquet 不支持此类格式。 

    例如，contoso(test) 之类的列名会分析[代码](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` 中用括号括起的类型。 由于没有这样的“测试”类型，因此了引发了错误。

    若要查看支持的类型，请转到 GitHub [apache/parquet-mr 站点](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)。

- **解决方法**： 

    仔细检查以查看：
    - 接收器列名称中是否有空格。
    - 包含空格的第一行是否用作列名。
    - OriginalType 类型是否受支持。 请尽量不要使用以下特殊字符：`,;{}()\n\t=`。 

## <a name="error-code-parquetdatetimeexceedlimit"></a>错误代码：ParquetDateTimeExceedLimit

- 消息：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因：** 如果日期/时间值为“0001-01-01 00:00:00”，则可能是儒略历和公历之间的差异导致的。 有关更多详细信息，请参阅[儒略历和外推格里历日期之间的差异](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)。

- **解决方法**：检查时钟周期值，并避免使用日期/时间值“0001-01-01 00:00:00”。

## <a name="error-code-parquetinvalidcolumnname"></a>错误代码：ParquetInvalidColumnName

- **消息**：`The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **原因**：列名包含无效字符。

- **解决方法**：添加或修改列映射，使接收器列名有效。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
