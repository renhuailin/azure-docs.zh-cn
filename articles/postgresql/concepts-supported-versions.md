---
title: 支持的版本 - Azure Database for PostgreSQL - 单一服务器
description: 介绍 Azure Database for PostgreSQL 中支持的 Postgres 主版本和次要版本 - 单一服务器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518447"
---
# <a name="supported-postgresql-major-versions"></a>支持的 PostgreSQL 主版本

请参阅 [Azure Database for PostgreSQL 版本控制策略](concepts-version-policy.md)以了解支持策略详细信息。

Azure Database for PostgreSQL 目前支持以下主版本：

## <a name="postgresql-version-11"></a>PostgreSQL 版本 11
当前次要版本为 11.6。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-6.html)。

## <a name="postgresql-version-10"></a>PostgreSQL 版本 10
当前次要版本为 10.11。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-11.html)。

## <a name="postgresql-version-96"></a>PostgreSQL 版本 9.6
当前次要版本为 9.6.16。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html)。

## <a name="postgresql-version-95-retired"></a>PostgreSQL 版本 9.5（已停用）
为遵守 Postgres 社区的[版本控制策略](https://www.postgresql.org/support/versioning/)，Azure Database for PostgreSQL 已于 2021 年 2 月 11 日起停用 Postgres 版本 9.5。 请参阅 [Azure Database for PostgreSQL 版本控制策略](concepts-version-policy.md)以了解更多详细信息和限制。 如果运行的是此主版本，请升级到更高版本，最好尽早升级到 PostgreSQL 11。

## <a name="managing-upgrades"></a>管理升级
PostgreSQL 项目定期发布次要版本来修复已报告的 bug。 Azure Database for PostgreSQL 在服务的每月部署期间会自动使用次要版本修补服务器。 

不支持主要版本的自动就地升级。 若要升级到更高的主版本，你可以 
   * 采用[使用转储和还原执行主版本升级](./how-to-upgrade-using-dump-and-restore.md)中介绍的方法之一。
   * 使用 [pg_dump 和 pg_restore](./howto-migrate-using-dump-and-restore.md) 将数据库移动到使用新引擎版本创建的服务器上。
   * 使用 [Azure 数据库迁移服务](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)进行联机升级。

### <a name="version-syntax"></a>版本语法
在 PostgreSQL 版本 10 之前，[PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将 _主版本_ 升级视为第一个 _或_ 第二个数字的增加。 例如，9.5 到 9.6 的升级视为 _主_ 版本升级。 从版本 10 开始，只有第一个数字更改才视为主版本升级。 例如，10.0 到 10.1 是 _次要_ 版本升级。 版本 10 到 11 的升级是 _主_ 版本升级。

## <a name="next-steps"></a>后续步骤
有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
