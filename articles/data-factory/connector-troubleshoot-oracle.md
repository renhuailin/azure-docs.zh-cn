---
title: 排查 Oracle 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Oracle 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390369"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂 和 Azure Synapse 中的 Oracle 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂 和 Azure Synapse 中常见的 Oracle 连接器问题。

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>错误代码：ArgumentOutOfRangeException

- 消息：`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**：在 Azure 数据工厂和 Synapse 管道中，支持从 0001-01-01 00:00:00 到 9999-12-31 23:59:59 范围内的日期时间值。 但是，Oracle 支持范围更广的日期时间值（例如公元前世纪或 min/sec>59），这会导致失败。

- **建议**： 

    若要查看 Oracle 中的值是否处于支持的日期范围内，请运行 `select dump(<column name>)`。 

    若要了解结果中的字节序列，请参阅[如何在 Oracle 中存储日期？](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
