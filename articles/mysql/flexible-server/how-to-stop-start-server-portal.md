---
title: 启动/停止 Azure 门户- Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 中执行停止/启动操作。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241916"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>停止/启动 Azure Database for MySQL 灵活服务器（预览）

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供了执行灵活服务器停止和启动的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   必须具备 Azure Database for MySQL 灵活服务器。

## <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要停止的灵活服务器。

2.  从 **概述** 页上，单击工具栏中的“**停止**”按钮。
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="停止灵活服务器。"::: 

3.  单击“**是**”以确认停止服务器。

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="确认停止灵活的服务器。"::: 

> [!NOTE]
> 停止服务器后，其他管理操作将不再适用于灵活的服务器。

## <a name="start-a-stopped-server"></a>启动已停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要停止的灵活服务器。

2.  从 **概述** 页上，单击工具栏中的“**启动**”按钮。

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="启动灵活服务器。":::  

> [!NOTE]
> 停止服务器后，其他管理操作将不再适用于灵活的服务器。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。

