---
title: 排查带分隔符的文本格式连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中带分隔符的文本格式连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390387"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的带分隔符的文本格式连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂和 Azure Synapse 中常见的带分隔符的文本格式连接器问题。

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>错误代码：DelimitedTextColumnNameNotAllowNull

- **消息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活动中设置“firstRowAsHeader”时，第一行用作列名。 此错误表示第一行包含空值（例如“ColumnA, ColumnB”）。

- **建议**：检查第一行，如果为空则修复值。


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>错误代码：DelimitedTextMoreColumnsThanDefined

- **消息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

  | 原因分析                                               | 建议                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 有问题的行的列计数大于第一行的列计数。 它可能是由于数据问题或列分隔符/引号字符设置有误而导致的。 | 请从错误消息中获取行计数，检查行的列，并修复数据。 |
  | 如果错误消息中预期的列计数为“1”，则可能指定了错误的压缩或格式设置，这导致文件未正确解析。 | 请检查格式设置，确保它们与源文件匹配。 |
  | 如果源是文件夹，则指定文件夹下的文件可能具有不同的架构。 | 请确保指定文件夹中的文件具有相同的架构。 |

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
