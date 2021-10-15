---
title: 排查 DB2 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 DB2 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390361"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂 和 Azure Synapse 中的 DB2 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂 和 Azure Synapse 中常见的 Azure Database for PostgreSQL 连接器问题。

## <a name="error-code-db2driverrunfailed"></a>错误代码：DB2DriverRunFailed

- **消息**：`Error thrown from driver. Sql code: '%code;'`

- **原因**：如果错误消息包含字符串“SQLSTATE=51002 SQLCODE=-805”，请按照 [从 DB2 复制数据](./connector-db2.md#linked-service-properties)中的“提示”进行操作。

- **建议**：尝试在 `packageCollection` 属性中设置“NULLID”。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
