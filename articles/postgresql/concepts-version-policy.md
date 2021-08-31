---
title: 版本控制策略 - Azure Database for PostgreSQL 单一服务器和灵活服务器（预览版）
description: 介绍针对 Azure Database for PostgreSQL （单一服务器）中的 Postgres 主版本和次要版本的策略。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 6d66d030514f121fabe8de36783d879afe0b5ea9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725248"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL 版本策略

本页介绍 Azure Database for PostgreSQL 版本控制策略，适用于 Azure Database for PostgreSQL 单一服务器和 Azure Database for PostgreSQL 灵活服务器（预览版）部署模式。

## <a name="supported--postgresql-versions"></a>支持的 PostgreSQL 版本

Azure Database for PostgreSQL 支持以下数据库版本。

| 版本 | 单台服务器 | 灵活服务器（预览版） | 超大规模 (Citus) |
| ----- | :------: | :----: | :----: |
| PostgreSQL 13 |  | X  | X |
| PostgreSQL 12 |  | X  | X |
| PostgreSQL 11 | X | X | X |
| PostgreSQL 10 | X |  |  |
| PostgreSQL 9.6 | X |  |  |
| *PostgreSQL 9.5（已停用）* | X |  |  |

## <a name="major-version-support"></a>主要版本支持
如 [PostgreSQL 社区版本控制策略](https://www.postgresql.org/support/versioning/)所述，从 Azure 开始支持该版本之日起，到 PostgreSQL 社区停用该版本之日结束，在此期间，Azure Database for PostgreSQL 将支持 PostgreSQL 的每个主版本。

## <a name="minor-version-support"></a>次要版本支持
在定期维护过程中，Azure Database for PostgreSQL 会自动从次要版本升级到 Azure 首选的 PostgreSQL 版本。 

## <a name="major-version-retirement-policy"></a>主要版本停用策略
下表提供了 PostgreSQL 主版本的停用详细信息。 日期遵循 [PostgreSQL 社区版本控制策略](https://www.postgresql.org/support/versioning/)。

| 版本 | 新增功能 | Azure 支持开始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9.5（已停用）](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [功能](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018 年 4 月 18 日   | 2021 年 2 月 11 日
| [PostgreSQL 9.6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [功能](https://wiki.postgresql.org/wiki/NewIn96) | 2018 年 4 月 18 日  | 2021 年 11 月 11 日
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [功能](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018 年 6 月 4 日  | 2022 年 11 月 10 日
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [功能](https://www.postgresql.org/docs/11/release-11.html) | 2019 年 7 月 24 日  | 2023 年 11 月 9 日
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [功能](https://www.postgresql.org/docs/12/release-12.html) | 2020 年 9 月 22 日  | 2024 年 11 月 14 日
| [PostgreSQL 13](https://www.postgresql.org/about/news/postgresql-13-released-2077/) | [功能](https://www.postgresql.org/docs/13/release-13.html) | 2021 年 5 月 25 日   | 2025 年 11 月 13 日

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 不支持已停用的 PostgreSQL 引擎版本

可以继续在 Azure Database for PostgreSQL 中运行已停用的版本。 但是，请注意每个 PostgreSQL 数据库版本的停用日期之后的以下限制：
- 由于社区将不会发布任何进一步的 bug 修复或安全修复，Azure Database for PostgreSQL 将不会针对任何 bug 或安全问题修补已停用的数据库引擎，也不会针对已停用的数据库引擎采取安全措施。 因此，你可能会遇到安全漏洞或其他问题。 但是，Azure 将继续对主机、OS、容器以及任何其他与服务相关的组件执行定期维护和修补。
- 如果遇到的任何支持问题与 PostgreSQL 引擎本身有关，由于社区不再提供补丁，因此我们可能无法为您提供支持。 在这种情况下，必须将数据库升级到受支持的版本之一。
- 无法为已停用的版本创建新的数据库服务器。 但能够执行时间点恢复并为现有服务器创建只读副本。
- Azure Database for PostgreSQL 开发的新服务功能可能仅适用于受支持的数据库服务器版本。
- 运行时间 SLA 将仅适用于与 Azure Database for PostgreSQL 服务相关的问题，而不适用于与数据库引擎相关的 bug 导致的任何故障时间。  
- 极端情况下，如果在已停用的数据库版本中识别的 PostgreSQL 数据库引擎漏洞对服务造成严重威胁，Azure 可能会选择停止数据库服务器，以保护服务。 这种情况下，系统会通知你在将服务器联机之前升级服务器。

## <a name="postgresql-version-syntax"></a>PostgreSQL 版本语法
在 PostgreSQL 版本 10 之前，[PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将 _主版本_ 升级视为第一个 _或_ 第二个数字的增加。 例如，9.5 到 9.6 的升级视为 _主_ 版本升级。 从版本 10 开始，只有第一个数字更改才视为主版本升级。 例如，10.0 到 10.1 是 _次要_ 版本升级。 版本 10 到 11 的升级是 _主_ 版本升级。

## <a name="next-steps"></a>后续步骤
- 请参阅 Azure Database for PostgreSQL 单一服务器[支持的版本](./concepts-supported-versions.md)
- 请参阅 Azure Database for PostgreSQL 灵活服务器（预览版）[支持的版本](flexible-server/concepts-supported-versions.md)
- 有关如何执行主版本升级的信息，请参阅[主版本升级](how-to-upgrade-using-dump-and-restore.md)文档。
- 有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
