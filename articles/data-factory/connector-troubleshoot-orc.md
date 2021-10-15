---
title: 排查 ORC 格式连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 ORC 格式连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390412"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 ORC 格式连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂和 Azure Synapse 中常见的 ORC 格式连接器问题。

## <a name="error-code-orcjavainvocationexception"></a>错误代码：OrcJavaInvocationException

- **消息**：`An error occurred when invoking Java, message: %javaException;.`
- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串时“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，这通常是 Integration Runtime 旧版本中的内存管理问题。 | 如果使用自承载集成运行时，则建议升级到最新版本。 |
    | 如果错误消息包含字符串“java.lang.OutOfMemory”，则集成运行时没有足够的资源来处理文件。 | 限制集成运行时中的并发运行。 对于自承载 IR，请纵向扩展到具有 8 GB 或更大内存的强大计算机。 |
    |如果错误消息包含字符串“NullPointerReference”，则可能是暂时性错误导致的。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |
    | 如果错误消息包含字符串“BufferOverflowException”，则可能是暂时性错误导致的。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |
    | 如果错误消息包含字符串“java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text”，则可能是 Java 运行时内部的类型转换问题导致的。 通常，这意味着在 Java 运行时中无法正常处理源数据。 | 这是数据问题。 请尝试在 ORC 格式的数据中使用字符串而不是 char 或 varchar。 |

## <a name="error-code-orcdatetimeexceedlimit"></a>错误代码：OrcDateTimeExceedLimit

- **消息**：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**：如果 datetime 值为“0001-01-01 00:00:00”，则可能是由于 [儒略历和格里历](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)之间的差异导致的。

- **建议**：检查时钟周期值，并避免使用日期/时间值“0001-01-01 00:00:00”。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
