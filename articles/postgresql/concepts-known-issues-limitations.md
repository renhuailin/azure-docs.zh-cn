---
title: 已知问题和限制 - Azure Database for PostgreSQL - 单一服务器和灵活服务器（预览版）
description: 列出客户应该注意的已知问题。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100106396"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 已知问题和限制

此页提供 Azure Database for PostgreSQL 中可能影响应用程序的已知问题列表。 它还列出了规避此问题的所有缓解措施和建议。

## <a name="intelligent-performance---query-store"></a>智能性能 - 查询存储

适用于 Azure Database for PostgreSQL - 单一服务器。

| 适用 | 原因 | 补救|
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | 在某些罕见的情况下，打开服务器参数 `pg_qs.replace_parameter_placeholders` 可能会导致服务器关闭。 | 通过 Azure 门户的“服务器参数”部分，将参数 `pg_qs.replace_parameter_placeholders` 值切换为 `OFF` 并保存。   | 

## <a name="server-parameters"></a>服务器参数

适用于 Azure Database for PostgreSQL - 单一服务器和灵活服务器

| 适用 | 原因 | 补救| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | 将服务器参数 `max_locks_per_transaction` 更改为比[建议值](https://www.postgresql.org/docs/11/kernel-resources.html)更高的值可能会导致服务器在重启后无法启动。 | 将其保留为默认值（32 或 64），或按照 PostgreSQL [文档](https://www.postgresql.org/docs/11/kernel-resources.html)中的说明更改为合理的值。 <br> <br> 从服务端来看，这是为了根据 SKU 限制较高的值。  | 

## <a name="next-steps"></a>后续步骤
- 请参阅查询存储[最佳做法](./concepts-query-store-best-practices.md)
