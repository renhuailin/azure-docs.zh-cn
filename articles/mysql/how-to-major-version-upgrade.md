---
title: 主版本升级-Azure Database for MySQL-单服务器 Azure 门户
description: 本文介绍如何使用 Azure 门户升级 Azure Database for MySQL 单服务器的主版本
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920638"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>使用 Azure 门户 Azure Database for MySQL 单一服务器的主版本升级

> [!IMPORTANT]
> 适用于 Azure database for MySQL 单一服务器的主要版本升级是公开预览版。

本文介绍如何将 MySQL 主要版本就地升级 Azure Database for MySQL 单一服务器。

利用此功能，客户无需进行任何数据移动或需要更改任何应用程序连接字符串，即可将 MySQL 5.6 服务器就地升级到 MySQL 5.7。

> [!Note]
> * 主版本升级仅适用于从 MySQL 5.6 到 MySQL 5.7 的主要版本升级<br>
> * 副本服务器上尚不支持主要版本升级。

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

## <a name="next-steps"></a>后续步骤

了解 [Azure Database for MySQL 版本控制策略](concepts-version-policy.md)。