---
title: 连接和查询 - 单一服务器 PostgreSQL
description: 指向快速入门的链接，演示如何连接到 Azure Database for PostgreSQL 单一服务器并运行查询。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546545"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL（单一服务器）连接和查询概述

以下文档包含指向示例的链接，这些示例显示了如何连接和查询 Azure Database for PostgreSQL 单一服务器。 本指南还包括 TLS 建议和扩展，你可以利用它们以下述受支持的语言连接到服务器。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|可以使用 pgadmin 连接到服务器，它可简化数据库对象的创建、维护和使用。|
|[Azure Cloud Shell 中的 psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|本文介绍如何运行 [Azure Cloud Shell](../cloud-shell/overview.md) 中的[“psql”](https://www.postgresql.org/docs/current/static/app-psql.html)以连接到服务器，然后运行语句以在数据库中查询、插入、更新和删除数据。你可以运行“psql”（如果它已安装在你的开发环境中） |
|[PostgreSQL 与 VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|适用于 VS Code 的 Azure 数据库扩展（预览版）可让你使用 scrapbooks 和丰富的 Intellisense 在本地和云中浏览和查询 PostgreSQL 服务器。 |
|[PHP](connect-php.md)|本快速入门演示如何使用 PHP 创建连接到数据库的程序，并使用数据库对象来查询数据。|
|[Java](connect-java.md)|本快速入门演示如何使用 Java 连接到数据库，然后使用数据库对象来查询数据。|
|[Node.js](connect-nodejs.md)|本快速入门演示如何使用 Node.js 创建连接到数据库的程序，并使用数据库对象来查询数据。|
|[.NET(C#)](connect-csharp.md)|本快速入门演示如何使用.NET (C#) 创建连接到数据库的 C# 程序，并使用数据库对象来查询数据。|
|[Go](connect-go.md)|本快速入门演示如何使用 Go 连接到数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Python](connect-python.md)|本快速入门演示如何使用 Python 连接到数据库，并使用数据库对象来查询数据。 |
|[Ruby](connect-ruby.md)|本快速入门演示如何使用 Ruby 创建连接到数据库的程序，并使用数据库对象来查询数据。|

## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

Microsoft 提供或支持的所有驱动程序使用传输层安全性 (TLS) 连接到 Azure Database for PostgreSQL 中的数据库。 不需要特殊配置，但对于新创建的服务器，请强制实施 TLS 1.2。 如果你使用的是 TLS 1.0 和 1.1，则建议你更新服务器的 TLS 版本。 请参阅[如何配置 TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>PostgreSQL 扩展

PostgreSQL 支持使用扩展来扩展数据的功能。 扩展在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载之后，扩展会如同内置功能一样运行。

- [Postgres 11 扩展](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10 扩展](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9.6 扩展](./concepts-extensions.md#postgres-96-extensions)
- [Postgres 9.5 扩展](./concepts-extensions.md#postgres-95-extensions)

有关更多详细信息，请参阅[如何在单一服务器上使用 PostgreSQL 扩展](concepts-extensions.md)。

## <a name="next-steps"></a>后续步骤

- [使用转储和还原迁移数据](howto-migrate-using-dump-and-restore.md)
- [使用导入和导出迁移数据](howto-migrate-using-export-and-import.md)
