---
title: 排查 XML 格式连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 XML 格式连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390418"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 XML 格式连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 XML 格式连接器问题的建议。

## <a name="error-code-xmlsinknotsupported"></a>错误代码：XmlSinkNotSupported

- **消息**：`Write data in XML format is not supported yet, choose a different format!`

- **原因**：XML 数据集在复制活动中用作接收器数据集。

- **建议**：使用与接收器数据集格式不同的数据集。


## <a name="error-code-xmlattributecolumnnameconflict"></a>错误代码：XmlAttributeColumnNameConflict

- **消息**：`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**：使用了属性前缀，这导致出现冲突。

- **建议**：为“attributePrefix”属性设置其他值。


## <a name="error-code-xmlvaluecolumnnameconflict"></a>错误代码：XmlValueColumnNameConflict

- **消息**：`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**：其中一个子元素名用作了元素值的列名。

- **建议**：为“valueColumn”属性设置其他值。


## <a name="error-code-xmlinvalid"></a>错误代码：XmlInvalid

- **消息**：`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**：输入 XML 文件的格式不正确。

- **建议**：更正 XML 文件以使其格式正确.

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
