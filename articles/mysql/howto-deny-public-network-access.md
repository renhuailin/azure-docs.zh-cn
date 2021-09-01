---
title: 拒绝公用网络访问 - Azure 门户 - Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 配置拒绝公用网络访问
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 9dad137791da3d90fda14382b14d6efbed7ba609
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651724"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中拒绝公用网络访问

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何将 Azure Database for MySQL 服务器配置为拒绝所有公用网络访问，并仅允许通过专用终结点进行连接，从而进一步增强网络安全性。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* 使用“常规用途”或“内存优化”定价层的 [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公用网络访问

按照以下步骤设置 MySQL 服务器的“拒绝公用网络访问”：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 MySQL 服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在“拒绝公用网络访问”中，选择“是” ，以便为 MySQL 服务器启用拒绝公用访问。

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database for MySQL - 拒绝网络访问":::

1. 单击“保存”以保存更改。

1. 此时将显示一则通知，确认已成功启用了连接安全性设置。

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL -“拒绝网络访问”已成功启用":::

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。
