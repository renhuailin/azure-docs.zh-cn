---
title: 排查 Azure Database for PostgreSQL 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Database for PostgreSQL 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390359"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Azure Database for PostgreSQL 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂和 Azure Synapse 中常见的 Azure Database for PostgreSQL 连接器问题。

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>错误代码：AzurePostgreSqlNpgsqlDataTypeNotSupported

- **消息**：`The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **建议**：选择包含 int、bigint、smallint、serial、bigserial、smallserial、timestamp（带或不带时区、不带时区或日期数据类型的时间）的分区列。

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>错误代码：AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **消息**：`Partition column name must be specified.`

- **原因**：未提供分区列名称，并且无法自动确定该名称。
 
## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
