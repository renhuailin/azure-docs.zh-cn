---
title: 适用于表 API 的 Azure Cosmos DB 全局分发教程
description: 了解全局分布如何在 Azure Cosmos DB 表 API 帐户中工作以及如何配置首选区域列表
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 21d06e0459508c290939dc22e728fbe26adbf349
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238262"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>使用表 API 设置 Azure Cosmos DB 全局分发
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用[表 API](table-introduction.md) 配置全局分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用表 API 连接到首选区域

为了利用[全局分发](distribute-data-globally.md)，客户端应用程序应当指定其应用程序在其中运行的当前位置。 这是通过设置 `CosmosExecutorConfiguration.CurrentRegion` 属性实现的。 `CurrentRegion` 属性应当包含单个位置。 每个客户端实例都可以指定其自己的区域，以实现低延迟读取。 区域必须使用其[显示名称](/previous-versions/azure/reference/gg441293(v=azure.100))命名，例如“美国西部”。 

Azure Cosmos DB 表 API SDK 将基于帐户配置和当前的区域可用性自动选取要与之通信的最佳终结点。 它优先选择最靠近的区域来使客户端实现更低的延迟。 在设置当前的 `CurrentRegion` 属性后，读取和写入请求将如下所述进行定向：

* **读取请求：** 所有读取请求都将发送到所配置的 `CurrentRegion`。 SDK 会基于邻近性自动选择回退异地复制区域以实现高可用性。

* **写入请求：** SDK 会自动将所有写入请求发送到当前写入区域。 在具有多区域写入功能的帐户中，当前区域也将为写入请求提供服务。 SDK 会基于邻近性自动选择回退异地复制区域以实现高可用性。

如果未指定 `CurrentRegion` 属性，则 SDK 会将当前写入区域用于所有操作。

例如，如果某个 Azure Cosmos 帐户位于“美国西部”和“美国东部”区域中。 如果“美国西部”是写入区域，并且应用程序位于“美国东部”。 如果未配置 CurrentRegion 属性，则所有读取和写入请求始终会定向到“美国西部”区域。 如果配置了 CurrentRegion 属性，则所有读取请求都将在“美国东部”区域中接受服务。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 Azure Cosmos DB 表 API 配置全局分发