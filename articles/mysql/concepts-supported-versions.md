---
title: 支持的版本 - Azure Database for MySQL
description: 了解 Azure Database for MySQL 服务支持哪些版本的 MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 6/3/2020
ms.openlocfilehash: 2c212922ebf550b75ee140637717a48beec9de02
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652546"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支持的 Azure Database for MySQL 服务器版本

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

使用 InnoDB 存储引擎通过 [MySQL Community Edition](https://www.mysql.com/products/community/) 开发 Azure Database for MySQL。 服务支持社区支持的所有当前主版本，即 MySQL 5.6、5.7 和 8.0。 MySQL 使用 X.Y.Z 命名方案，其中 X 为主版本，Y 为次要版本，Z 为 bug 修补版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>连接到运行特定 MySQL 版本的网关节点

在单一服务器部署选项中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。 查看[连接体系结构](./concepts-connectivity-architecture.md#connectivity-architecture)，详细了解 Azure Database for MySQL 服务体系结构中的网关。

由于 Azure Database for MySQL 支持主要版本 v5.6、v5.7 和 v8.0，连接到 Azure Database for MySQL 的默认端口 3306 运行 MySQL 客户端版本 5.6（最小公分母），以支持与所有 3 个受支持的主版本的服务器的连接。 但是，如果应用程序需要连接到特定的主要版本（如 v5.7 或 v8.0），则可以通过更改服务器连接字符串中的端口来实现。

在 Azure Database for MySQL 服务中，网关节点在端口 3308 上侦听 v5.7 客户端，在端口 3309 上侦听 v8.0 客户端。 换句话说，如果要连接到 v5.7 网关客户端，则应使用完全限定的服务器名称和端口 3308 从客户端应用程序连接到服务器。 同样，如果要连接到 v8.0 网关客户端，可以使用完全限定的服务器名称和端口 3309 连接到服务器。 查看以下示例以深入了解。

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="通过不同的网关 mysql 版本进行连接的示例":::

> [!NOTE]
> 只有公共连接支持通过端口 3308 和3309 连接到 Azure Database for MySQL，专用链接和 VNet 服务终结点仅支持搭配端口 3306 使用。

## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL 当前支持以下主版本和次要版本的 MySQL：

| 版本 | [单一服务器](overview.md) <br/> 当前次要版本 |[灵活服务器（预览版）](./flexible-server/overview.md) <br/> 当前次要版本  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 版本 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)（已停用） | 不支持|
|MySQL 版本 5.7 | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html) | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html)|
|MySQL 版本 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

请阅读[版本支持策略文档](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)中已停用版本的版本支持策略。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

该服务会自动管理针对 Bug 修复版本更新的修补。 例如，5.7.20 到 5.7.21。  

服务当前支持从 MySQL v5.6 升级到 v5.7 的主版本升级。 有关更多详细信息，请参阅[如何进行主版本升级](how-to-major-version-upgrade.md)。 如果要从 5.7 升级到 8.0，建议[转储和还原](./concepts-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Database for MySQL 版本控制策略的详细信息，请参阅[此文档](concepts-version-policy.md)。
- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)
