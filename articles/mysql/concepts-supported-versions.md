---
title: 支持的版本 - Azure Database for MySQL
description: 了解 Azure Database for MySQL 服务支持哪些版本的 MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 920f6a4fec1ec8a260a98641888268e4955bbf44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718771"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支持的 Azure Database for MySQL 服务器版本

已使用 InnoDB 存储引擎从 [MySQL 社区版](https://www.mysql.com/products/community/)开发 Azure Database for MySQL。 服务支持社区支持的所有当前主要版本，即 MySQL 5.6、5.7 和8.0。 MySQL 使用 X.x.x.x 命名方案，其中 X 为主要版本，Y 为次版本，Z 为 bug 修复版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 在单服务器部署选项中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

Azure Database for MySQL 当前支持以下主要和次要版本的 MySQL：


| 版本 | 单台服务器 <br/> 当前次要版本 |灵活服务器（预览版） <br/> 当前次要版本  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 版本 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | 不支持|
|MySQL 版本 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 版本 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务会自动管理针对 Bug 修复版本更新的修补。 例如，5.7.20 到 5.7.21。  

服务当前支持从 MySQL v5.6 升级到 v5.7 的主版本升级。 有关更多详细信息，请参阅[如何进行主版本升级](how-to-major-version-upgrade.md)。 如果要从 5.7 升级到 8.0，建议[转储和还原](./concepts-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Database for MySQL 版本控制策略的详细信息，请参阅[此文档](concepts-version-policy.md)。
- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)
