---
title: '版本控制策略-Azure Database for PostgreSQL-单服务器和灵活的服务器 (预览版) '
description: 描述围绕 Azure Database for PostgreSQL-Single Server 中的 Postgres 主版本和次版本的策略。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f325a43895e1e9d73b11c06662851d7654d31ddb
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331815"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL 版本控制策略

本页介绍 Azure Database for PostgreSQL 版本控制策略，适用于 Azure Database for PostgreSQL 单一服务器和 Azure Database for PostgreSQL 灵活的服务器 (预览版) 部署模式。

## <a name="supported--postgresql-versions"></a>支持的 PostgreSQL 版本

Azure Database for PostgreSQL 支持以下数据库版本。

| 版本 | 单台服务器 | 灵活服务器（预览版） |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9。6 | X |  |
| PostgreSQL 9。5 | X |  |

## <a name="major-version-support"></a>主要版本支持
PostgreSQL 将支持每个主版本的，Azure Database for PostgreSQL 从 Azure 开始支持版本到 PostgreSQL 社区终止版本之前，如 [PostgreSQL 社区版本控制策略](https://www.postgresql.org/support/versioning/)中所述。

## <a name="minor-version-support"></a>次版本支持
Azure Database for PostgreSQL 会在定期维护过程中自动执行到 Azure 首选 PostgreSQL 版本的次要版本升级。 

## <a name="major-version-retirement-policy"></a>主要版本停用策略
下表提供了 PostgreSQL 主版本的停用详细信息。 日期遵循 [PostgreSQL 团体版本控制策略](https://www.postgresql.org/support/versioning/)。

| 版本 | 新增功能 | Azure 支持开始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| PostgreSQL 9。5| [功能](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018 年 4 月 18 日    | 2021年2月11日
| [PostgreSQL 9。6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [功能](https://wiki.postgresql.org/wiki/NewIn96) | 2018 年 4 月 18 日  | 2021年11月11日
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [功能](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018 年 6 月 4 日  | 2022年11月10日
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [功能](https://www.postgresql.org/docs/11/release-11.html) | 2019 年 7 月 24 日  | 2023年11月9日
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [功能](https://www.postgresql.org/docs/12/release-12.html) | 9月22日2020  | 2024年11月14日

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中不支持停用的 PostgreSQL 引擎版本

在每个 PostgreSQL 数据库版本的停用日期之后，如果继续运行停用的版本，请注意以下限制：
- 由于社区不会发布任何更多的 bug 修复程序或安全修补程序，Azure Database for PostgreSQL 将不会修补已停用的数据库引擎的任何错误或安全问题，或者采用与停用的数据库引擎有关的安全措施。 因此，可能会遇到安全漏洞或其他问题。 但是，Azure 将继续针对主机、OS、容器和任何其他与服务相关的组件执行定期维护和修补。
- 如果你可能会遇到与 PostgreSQL 数据库相关的任何支持问题，我们可能无法为你提供支持。 在这种情况下，你将必须升级数据库，以便我们为你提供任何支持。
- 你将不能为已停用的版本创建新的数据库服务器。 但是，你将能够执行时间点恢复，并为现有服务器创建读取副本。
- Azure Database for PostgreSQL 开发的新服务功能仅适用于受支持的数据库服务器版本。
- 运行时间 Sla 仅适用于与 Azure Database for PostgreSQL 服务相关的问题，不适用于与数据库引擎相关的错误引起的任何停机时间。  
- 在对由停用的数据库版本中标识的 PostgreSQL 数据库引擎漏洞导致的服务产生严重威胁的极端情况下，Azure 可能会选择停止数据库服务器来保护服务。 在这种情况下，将在使服务器联机之前通知你升级服务器。

## <a name="postgresql-version-syntax"></a>PostgreSQL 版本语法
在 PostgreSQL 版本 10 之前， [PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将 _主版本_ 升级视为第一个 _或_ 第二个数字的增加。 例如，9.5 到 9.6 的升级视为 _主_ 版本升级。 从版本 10 开始，只有第一个数字更改才视为主版本升级。 例如，10.0 到 10.1 是 _次要_ 版本升级。 版本 10 到 11 的升级是 _主_ 版本升级。

## <a name="next-steps"></a>后续步骤
- 请参阅 Azure Database for PostgreSQL-单一服务器 [支持的版本](./concepts-supported-versions.md)
- 请参阅 Azure Database for PostgreSQL-灵活的服务器 (预览版) [支持的版本](flexible-server/concepts-supported-versions.md)
- 有关如何执行主要版本升级的信息，请参阅 [主要版本升级](how-to-upgrade-using-dump-and-restore.md) 文档。
- 有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
