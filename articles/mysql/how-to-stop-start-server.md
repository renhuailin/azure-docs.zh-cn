---
title: 停止/启动 - Azure 门户- Azure Database for MySQL 服务器
description: 本文介绍了如何在 Azure Database for MySQL 中停止/启动操作。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: ed784f38c5c297e06c62c2bb7f801a4f0aac0799
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652701"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>停止/启动 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!IMPORTANT]
> 当你停止服务器时，它将在接下来的 7 天中一直保持该状态。 如果在此期间未手动启动服务器，则服务器将在 7 天结束时自动启动。 如果不使用服务器，则可以选择再次停止它。

本文逐步介绍了停止和启动单一服务器的过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   必须有 Azure Database for MySQL 单一服务器。

> [!NOTE]
> 请参阅执行[停止/启动](concepts-servers.md#limitations-of-stopstart-operation)操作的限制

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>如何使用 Azure 门户停止/启动 Azure Database for MySQL

### <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的 MySQL 服务器。

2.  在“概述”页中，单击工具栏中的“停止”按钮。

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL“停止服务器”":::

    > [!NOTE]
    > 在服务器停止后，将无法对单一服务器执行其他管理操作。

### <a name="start-a-stopped-server"></a>启动已停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的单一服务器。

2.  在“概述”页中，单击工具栏中的“启动”按钮。

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL“启动服务器”":::

    > [!NOTE]
    > 在服务器启动后，现在就可以对单一服务器执行所有管理操作。

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>如何使用 CLI 停止/启动 Azure Database for MySQL

### <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的 MySQL 服务器。

2.  在“概述”页中，单击工具栏中的“停止”按钮。

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 在服务器停止后，将无法对单一服务器执行其他管理操作。

### <a name="start-a-stopped-server"></a>启动已停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的单一服务器。

2.  在“概述”页中，单击工具栏中的“启动”按钮。

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 在服务器启动后，现在就可以对单一服务器执行所有管理操作。

## <a name="next-steps"></a>后续步骤
了解[如何基于指标创建警报](howto-alert-on-metric.md)。
