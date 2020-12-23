---
title: 支持的版本 - Azure Database for MySQL
description: 了解 Azure Database for MySQL 服务支持哪些版本的 MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751022"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支持的 Azure Database for MySQL 服务器版本

使用 InnoDB 引擎通过 [MySQL Community Edition](https://www.mysql.com/products/community/) 开发 Azure Database for MySQL。

MySQL 使用 X.Y.Z 命名方案。 X 是主版本，Y 是次版本，Z 是 bug 修复版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。


> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

Azure Database for MySQL 目前支持以下版本：

## <a name="mysql-version-56"></a>MySQL 版本 5.6

Bug 修复版本：5.6.47

要详细了解此版本中的改进和修复，请参阅 MySQL [发行说明](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)。

## <a name="mysql-version-57"></a>MySQL 版本 5.7

Bug 修复版本：5.7.29

要详细了解此版本中的改进和修复，请参阅 MySQL [发行说明](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)。

## <a name="mysql-version-80"></a>MySQL 版本 8.0

Bug 修复版本：8.0.15

要详细了解此版本中的改进和修复，请参阅 MySQL [发行说明](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务会自动管理针对 Bug 修复版本更新的修补。 例如，5.7.20 到 5.7.21。  

当前，服务支持从 MySQL 5.6 版升级到版本5.7 的主要版本升级。 有关更多详细信息，请参阅 [如何执行主要版本升级](how-to-major-version-upgrade.md)。 如果要从5.7 升级到8.0，建议对使用新引擎版本创建的服务器执行 [转储和还原](./concepts-migrate-dump-restore.md) 。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Database for MySQL 版本控制策略的详细信息，请参阅 [此文档](concepts-version-policy.md)。
- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)
