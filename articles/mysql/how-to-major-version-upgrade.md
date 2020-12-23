---
title: 主版本升级-Azure Database for MySQL-单服务器 Azure 门户
description: 本文介绍如何使用 Azure 门户升级 Azure Database for MySQL 单服务器的主版本
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753045"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>使用 Azure 门户 Azure Database for MySQL 单一服务器的主版本升级

> [!IMPORTANT]
> 适用于 Azure database for MySQL 单一服务器的主要版本升级是公开预览版。

本文介绍如何将 MySQL 主要版本就地升级 Azure Database for MySQL 单一服务器。

利用此功能，客户无需进行任何数据移动或需要更改任何应用程序连接字符串，即可将 MySQL 5.6 服务器就地升级到 MySQL 5.7。

> [!Note]
> * 主版本升级仅适用于从 MySQL 5.6 到 MySQL 5.7 的主要版本升级<br>
> * 副本服务器上尚不支持主要版本升级。
> * 在整个升级操作过程中，服务器将不可用。 因此，建议在计划内维护时段执行升级。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [单台服务器 Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>执行从 MySQL 5.6 到 MySQL 5.7 的主要版本升级

按照以下步骤为你的 Azure Database for MySQL 5.6 服务器执行主要版本升级

> [!IMPORTANT]
> 建议在服务器的还原副本上首先执行升级，而不是直接升级生产。 请参阅 [如何执行时间点还原](howto-restore-server-portal.md#point-in-time-restore)。 

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有的 Azure Database for MySQL 5.6 服务器。

2. 从 " **概述** " 页上，单击工具栏中的 " **升级** " 按钮。

3. 在 " **升级** " 部分，选择 **"确定"** 将 Azure database for MySQL 5.6 服务器升级到5.7 服务器。

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-概述-升级":::

4. 通知会确认升级是否成功。

## <a name="frequently-asked-questions"></a>常见问题

**1. 在我们需要升级的生产环境中，此升级功能将在正式发布时发布到我们的 MySQL 5.6？**

在 MySQL 5.6 版停用之前计划此功能的 GA。 但是，该功能是 Azure 的生产就绪且完全受支持，因此你应该在环境中自信地运行它。 作为推荐的最佳做法，我们强烈建议你先在服务器的还原副本上运行并测试，以便可以在升级过程中估计停机时间，并在生产中运行应用程序兼容性测试。 有关详细信息，请参阅 [如何执行时间点还原](howto-restore-server-portal.md#point-in-time-restore) ，以创建服务器的时间点副本。 

**2. 这会导致服务器停机，如果有多长时间？**

是的，此服务器在升级过程中将不可用，因此，我们建议你在计划的维护时段内执行此操作。 估计停机时间取决于数据库大小、预配的存储大小 (IOPs 预配) 以及数据库上的表数。 升级时间与服务器上的表数成正比。基本 SKU 服务器的升级预计需要更长时间，因为它在标准存储平台上。 为了估计服务器环境的停机时间，我们建议首先在服务器的还原副本上执行升级。  

**3. 注意到它在副本服务器上尚不受支持。这是什么意思？**

目前，副本服务器不支持主要版本升级，这意味着不应为复制所涉及的服务器运行它， (源或副本服务器) 。 如果要在添加升级功能的副本支持之前测试复制中涉及的服务器的升级，我们建议执行以下步骤：

1. 在计划内维护期间，在捕获其名称和所有配置信息 (防火墙设置，如果服务器参数配置不同于源服务器) ，则 [停止复制和删除副本服务器](howto-read-replicas-portal.md) 。
2. 执行源服务器的升级。
3. 使用在步骤1中捕获的相同名称和配置设置来预配新的读取副本服务器。 将源服务器升级到 v2.0 后，新的副本服务器将自动在版本5.7 上。

**4. 如果未选择在2021年2月5日之前升级 MySQL 5.6 server，会发生什么情况？**

你仍可像以前一样继续运行 MySQL 5.6 server。 Azure **决不会** 在你的服务器上执行强制升级。 但是，将应用 [Azure Database for MySQL 版本控制策略](concepts-version-policy.md) 中所述的限制。

## <a name="next-steps"></a>后续步骤

了解 [Azure Database for MySQL 版本控制策略](concepts-version-policy.md)。
