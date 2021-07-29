---
title: 停止/启动 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中执行停止/启动操作。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: a08c9244644228bd6d9154bdc4882fc91334bc62
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797367"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview-using-azure-portal"></a>使用 Azure 门户停止/启动 Azure Database for PostgreSQL 灵活服务器（预览版）

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器当前为预览版。

本文提供了有关停止和启动灵活服务器的分步说明。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   必须有 Azure Database for PostgreSQL 灵活服务器。

## <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要停止的灵活服务器。

2.  从“概述”页上，单击工具栏中的“停止”按钮。

> [!NOTE]
> 停止服务器后，其他管理操作将立即不可用于灵活服务器。

请注意，已停止的服务器将在七天后自动重启。 下次启动服务器时，将应用所有挂起的维护更新。

## <a name="start-a-stopped-server"></a>启动已停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要启动的灵活服务器。

2.  在“概述”页上，单击工具栏中的“启动”按钮。

> [!NOTE]
> 启动服务器后，所有管理操作将立即可用于灵活服务器。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Database for PostgreSQL 灵活服务器中的计算和存储选项](./concepts-compute-storage.md)。
