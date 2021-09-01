---
title: 连接和查询 - 单一服务器 MySQL
description: 指向快速入门的链接，演示如何连接到 Azure MySQL 数据库单一服务器并运行查询。
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: def4794d091155cbf10a854d304d4390659626bf
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652098"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Azure Database for MySQL（单一服务器）连接和查询概述

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

以下文档包含指向示例的链接，这些示例显示了如何连接和查询 Azure Database for MySQL 单一服务器。 本指南还包括 TLS 建议和库，你可以利用它们以下述受支持的语言连接到服务器。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[MySQL Workbench](connect-workbench.md)|本快速入门演示如何使用 MySQL Workbench 客户端连接到数据库。 然后，可使用 MySQL 语句在数据库中查询、插入、更新和删除数据。|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|本文介绍如何在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行 mysql.exe 以连接到服务器，然后运行语句以在数据库中查询、插入、更新和删除数据。|
|[将 MySQL 与 Visual Studio 配合使用](https://www.mysql.com/why-mysql/windows/visualstudio)|可以使用 MySQL for Visual Studio 连接到 MySQL 服务器。 MySQL for Visual Studio 直接集成到服务器资源管理器中，因而可以轻松设置新连接和使用数据库对象。|
|[PHP](connect-php.md)|本快速入门演示如何使用 PHP 创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|
|[Java](connect-java.md)|本快速入门演示如何使用 Java 连接到数据库，然后使用 MySQL 语句来查询数据。|
|[Node.js](connect-nodejs.md)|本快速入门演示如何使用 Node.js 创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|
|[.NET(C#)](connect-csharp.md)|本快速入门演示如何使用 .NET (C#) 创建连接到数据库的 C# 程序，并使用 MySQL 语句来查询数据。|
|[Go](connect-go.md)|本快速入门演示如何使用 Go 连接到数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Python](connect-python.md)|本快速入门演示如何使用 Python 连接到数据库，并使用 MySQL 语句来查询数据。 |
|[Ruby](connect-ruby.md)|本快速入门演示如何使用 Ruby 创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|
|[C++](connect-cpp.md)|本快速入门演示如何使用 C++ 创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|

## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

Microsoft 提供或支持的所有驱动程序使用传输层安全性 (TLS) 连接到 Azure Database for MySQL 中的数据库。 不需要特殊配置，但对于新创建的服务器，请强制实施 TLS 1.2。 如果你使用的是 TLS 1.0 和 1.1，则建议你更新服务器的 TLS 版本。 请参阅[如何配置 TLS](howto-tls-configurations.md)

## <a name="libraries"></a>库

Azure Database for MySQL 使用世界上最常用的 MySQL 数据库社区版。 因此，它与多种编程语言和驱动程序兼容。 目标是支持三个最新版本的 MySQL 驱动程序，并且与来自开源社区的创建者共同努力，不断改进 MySQL 驱动程序的功能和可用性。

查看哪些[驱动程序](concepts-compatibility.md)与 Azure Database for MySQL 单一服务器兼容。

## <a name="next-steps"></a>后续步骤

- [使用转储和还原迁移数据](concepts-migrate-dump-restore.md)
- [使用导入和导出迁移数据](concepts-migrate-import-export.md)