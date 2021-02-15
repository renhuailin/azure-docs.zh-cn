---
title: '已知问题和限制-Azure Database for PostgreSQL 单服务器和灵活的服务器 (预览版) '
description: 列出客户应该注意的已知问题。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106396"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL-已知问题和限制

此页提供了可能影响应用程序 Azure Database for PostgreSQL 中的已知问题列表。 它还列出了解决此问题的任何缓解措施和建议。

## <a name="intelligent-performance---query-store"></a>智能性能-查询存储

适用于 Azure Database for PostgreSQL 单服务器。

| 适用 | 原因 | 补救|
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | `pg_qs.replace_parameter_placeholders`在某些罕见的情况下，打开服务器参数可能会导致服务器关闭。 | 通过 Azure 门户的 "服务器参数" 部分，将参数 `pg_qs.replace_parameter_placeholders` 值转换为， `OFF` 并保存。   | 

## <a name="server-parameters"></a>服务器参数

适用于 Azure Database for PostgreSQL 单服务器和灵活服务器

| 适用 | 原因 | 补救| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | 将 server 参数更改 `max_locks_per_transaction` 为比 [建议](https://www.postgresql.org/docs/11/kernel-resources.html) 的值更高的值可能会导致服务器无法在重启后启动。 | 将其保留为默认值 (32 或 64) 或更改为每 PostgreSQL [文档](https://www.postgresql.org/docs/11/kernel-resources.html)的合理值。 <br> <br> 从服务端开始，这是为了根据 SKU 限制高价值。  | 

## <a name="next-steps"></a>后续步骤
- 请参阅查询存储 [最佳实践](./concepts-query-store-best-practices.md)
