---
title: 拒绝公用网络访问 - Azure 门户 - Azure Database for PostgreSQL（单一服务器）
description: 了解如何使用 Azure 门户为 Azure Database for PostgreSQL 单一服务器配置“拒绝公用网络访问”
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727101"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>使用 Azure 门户在 Azure Database for PostgreSQL 单一服务器中拒绝公用网络访问

本文介绍如何将 Azure Database for PostgreSQL 单一服务器配置为拒绝所有公共配置，仅允许通过专用终结点进行连接，从而进一步增强网络安全性。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* Azure Database for PostgreSQL 具有常规用途或内存优化定价层的 [单一服务器](quickstart-create-server-database-portal.md) 。

## <a name="set-deny-public-network-access"></a>设置“拒绝公用网络访问”

按照以下步骤设置 PostgreSQL 单一服务器的“拒绝公用网络访问”：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for PostgreSQL 单一服务器。

1. 在 PostgreSQL 单一服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在“拒绝公用网络访问”中，选择“是”，以便为 PostgreSQL 单一服务器启用“拒绝公用访问”功能。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL 单一服务器的“拒绝网络访问”":::

1. 单击“保存”以保存更改。

1. 此时将显示一则通知，确认已成功启用了连接安全性设置。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL 单一服务器的“拒绝网络访问”成功":::

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。
