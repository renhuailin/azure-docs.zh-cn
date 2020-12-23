---
title: 连接和查询-灵活的服务器 PostgreSQL
description: 指向快速入门的链接，其中显示了如何连接到 Azure Database for PostgreSQL 灵活的服务器并运行查询。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364555"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>适用于 PostgreSQL 的 Azure 数据库的连接和查询概述-灵活服务器

以下文档包含指向示例的链接，这些示例显示了如何连接和查询 Azure Database for PostgreSQL 单一服务器。 本指南还包括 TLS 建议和扩展，你可以利用它们以下述受支持的语言连接到服务器。

>[!IMPORTANT]
> Azure Database for PostgreSQL 灵活的服务器处于 **预览阶段**。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|可以使用 pgadmin 连接到服务器，它可简化数据库对象的创建、维护和使用。|
|[Azure Cloud Shell 中的 psql](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|本文介绍如何在 [Azure Cloud Shell](../../cloud-shell/overview.md)中运行 [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html)以连接到服务器，然后运行语句以在数据库中查询、插入、更新和删除数据。如果已在开发环境中安装，则可以运行 **psql**|
|[Python](connect-python.md)|本快速入门演示如何使用 Python 连接到数据库，并使用数据库对象来查询数据。 |
|[应用服务的 Django](tutorial-django-app-service-postgres.md)|本教程演示如何使用 Ruby 创建连接到数据库的程序，并使用数据库对象处理数据。|

## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

Microsoft 提供或支持的所有驱动程序使用传输层安全性 (TLS) 连接到 Azure Database for PostgreSQL 中的数据库。 不需要特殊配置，但对于新创建的服务器，请强制实施 TLS 1.2。 如果你使用的是 TLS 1.0 和 1.1，则建议你更新服务器的 TLS 版本。 请参阅[如何配置 TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>PostgreSQL 扩展

PostgreSQL 支持使用扩展来扩展数据的功能。 扩展在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载之后，扩展会如同内置功能一样运行。

- [Postgres 12 扩展](./concepts-extensions.md#postgres-12-extensions)
- [Postgres 11 扩展](./concepts-extensions.md#postgres-11-extensions)
- [dblink 和 postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

有关更多详细信息，请参阅 [如何在灵活的服务器上使用 PostgreSQL 扩展](concepts-extensions.md)。

## <a name="next-steps"></a>后续步骤

- [使用转储和还原迁移数据](../howto-migrate-using-dump-and-restore.md)
- [使用导入和导出迁移数据](../howto-migrate-using-export-and-import.md)
