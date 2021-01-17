---
title: 对于 MongoDB 操作，防止 Azure Cosmos DB API 的速率限制。
description: 了解如何通过 SSR (服务器端重试) 功能防止 MongoDB 操作的 Azure Cosmos DB API 处理命中速率限制错误。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540510"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>阻止用于 MongoDB 操作的 Azure Cosmos DB API 的速率限制错误
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

如果 Azure Cosmos DB API for MongoDB 操作可能会失败，并出现速率限制 (16500/429) 错误，前提是它们超过了集合的吞吐量限制)  (： 

你可以启用服务器端重试 (SSR) 功能，并让服务器自动重试这些操作。 在帐户中的所有集合的短暂延迟后，会重试请求。 此功能是在客户端应用程序中处理速率限制错误的一种简便方法。


## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 MongoDB 帐户的 Azure Cosmos DB API。

1. 中转到 "**设置**" 部分下的 "**功能**" 窗格。

1. 选择 " **服务器重试**"。

1. 单击 " **启用** " 可为帐户中的所有集合启用此功能。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="适用于 MongoDB Azure Cosmos DB API 的服务器端重试功能的屏幕截图":::


## <a name="next-steps"></a>后续步骤

若要详细了解常见错误的疑难解答，请参阅以下文章：

* [解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题](mongodb-troubleshoot.md)
