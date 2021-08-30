---
title: 连接池 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 缩放客户端数据库连接
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: d611698bcc72a4a8ceaafb6cfe43f7da362acf71
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740300"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Azure Database for PostgreSQL 超大规模 (Citus) 连接池

建立新连接需要时间。 这会对多数应用程序产生不利影响，因为这些应用程序会请求建立很多短时的连接。 建议使用连接池程序，既可以减少空闲事务，又可以重用现有连接。 要了解详细信息，请访问[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

你可以运行自己的连接池程序，也可以使用 Azure 托管的 PgBouncer。

## <a name="managed-pgbouncer"></a>托管 PgBouncer

使用 PgBouncer 之类的连接池程序时，可以一次性将更多客户端连接到协调器节点。 应用程序连接到池程序，池程序将命令中继到目标数据库。

当客户端通过 PgBouncer 连接时，可在数据库中主动运行的连接数不变。 相反，PgBouncer 会将超出的连接排入队列，并在数据库准备就绪时运行这些连接。

超大规模 (Citus) 现在为服务器组提供 PgBouncer 托管实例。 它支持多达 2,000 个客户端同时连接。  若要通过 PgBouncer 连接，请按照以下步骤操作：

1. 在 Azure 门户中转到相应服务器组的“连接字符串”页。
2. 启用“PgBouncer 连接字符串”复选框。 （列出的连接字符串将发生更改。）
3. 将客户端应用程序更新为使用新的字符串进行连接。

## <a name="next-steps"></a>后续步骤

详细了解超大规模 (Citus) 的[限制和局限性](concepts-hyperscale-limits.md)。
