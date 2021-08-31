---
title: Azure 德国数据库服务 | Microsoft Docs
description: 本文提供 Azure 德国的 SQL 数据库服务的比较。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029079"
---
# <a name="azure-germany-database-services"></a>Azure 德国数据库服务

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>SQL 数据库
Azure SQL 数据库和 Azure SQL 托管实例 V12 通常在 Azure 德国中可用。 有关元数据可见性配置和保护最佳实践的指导，请参阅 [Microsoft 安全中心的 SQL 数据库引擎](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)以及[SQL 数据库全球文档](../azure-sql/database/index.yml)和 [SQL 托管实例全球文档](../azure-sql/managed-instance/index.yml)。

### <a name="variations"></a>变体
Azure 德国中的 SQL 数据库地址与全球 Azure 中的地址不同：

| 服务类型 | 全球 Azure | Azure 德国 |
| --- | --- | --- |
| SQL 数据库 | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>用于 Redis 的 Azure 缓存
有关用于 Redis 的 Azure 缓存以及如何使用的详细信息，请参阅 [用于 Redis 的 Azure 缓存全球文档](../azure-cache-for-redis/index.yml)。

### <a name="variations"></a>变体
用于访问和管理 Azure 德国中用于 Redis 的 Azure 缓存的 URL 与全球 Azure 中的 URL 不同：

| 服务类型 | 全球 Azure | Azure 德国 |
| --- | --- | --- |
| 缓存终结点 | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure 门户 | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> 所有脚本和代码均需要说明相应终结点和环境。 有关详细信息，请参阅[使用 Azure PowerShell 管理 Azure Redis 缓存](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md)中的“连接到 Microsoft Azure Germany”。
>
>


## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Azure 德国博客](/archive/blogs/azuregermany/)。