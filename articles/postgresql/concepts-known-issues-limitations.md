---
title: 已知问题和限制 - Azure Database for PostgreSQL - 单一服务器和灵活服务器（预览版）
description: 列出客户应该注意的已知问题。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 0806ef1a191759e769271a97fb1809298cde10db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725270"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 已知问题和限制

此页提供 Azure Database for PostgreSQL 中可能影响应用程序的已知问题列表。 它还列出了规避此问题的所有缓解措施和建议。

## <a name="intelligent-performance---query-store"></a>智能性能 - 查询存储

适用于 Azure Database for PostgreSQL - 单一服务器。

| 适用 | 原因 | 补救|
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | 在某些罕见的情况下，打开服务器参数 `pg_qs.replace_parameter_placeholders` 可能会导致服务器关闭。 | 通过 Azure 门户的“服务器参数”部分，将参数 `pg_qs.replace_parameter_placeholders` 值切换为 `OFF` 并保存。   | 


## <a name="next-steps"></a>后续步骤
- 请参阅查询存储[最佳做法](./concepts-query-store-best-practices.md)
