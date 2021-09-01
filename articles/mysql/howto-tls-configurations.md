---
title: TLS 配置 - Azure 门户 - Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 设置 TLS 配置
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: c5de8a9f50dd280f8eb3a52ad0bd39a00e58c789
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652521"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中配置 TLS 设置

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何配置 Azure Database for MySQL 服务器，以强制设置连接所允许的最低 TLS 版本并拒绝所有使用更低 TLS 版本（与已配置的最低 TLS 版本相比）的连接，从而增强网络安全性。

可以强制设置用于连接到 Azure Database for MySQL 的 TLS 版本。 现在，客户可以选择为其数据库服务器设置最低 TLS 版本。 例如，将此最低 TLS 版本设置为 1.0 意味着应允许客户端使用 TLS 1.0、1.1 和 1.2 进行连接。 也可将此选项设置为 1.2，这意味着仅允许客户端使用 TLS 1.2+ 进行连接，所有使用 TLS 1.0 和 TLS 1.1 的传入连接都将被拒绝。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置 TLS 配置

请按照以下步骤设置 MySQL 服务器的最低 TLS 版本：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 MySQL 服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在“最低 TLS 版本”中，选择“1.2”，为 MySQL 服务器拒绝 TLS 版本低于 TLS 1.2 的连接 。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for MySQL 的 TLS 配置":::

1. 单击“保存”以保存更改。 

1. 此时将显示一则通知，确认已成功启用了连接安全性设置，即刻生效。 不需要 **重启** 服务器，也未执行该操作。 保存更改后，仅当 TLS 版本大于或等于门户上设置的最低 TLS 版本时，才接受到服务器的所有新连接。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL 的 TLS 配置成功":::

## <a name="next-steps"></a>后续步骤

- 了解[如何基于指标创建警报](howto-alert-on-metric.md)
