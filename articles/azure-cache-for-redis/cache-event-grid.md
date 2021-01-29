---
title: Azure Cache for Redis 事件网格概述
description: 使用 Azure 事件网格发布 Redis 事件的 Azure 缓存。
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055722"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Azure Cache for Redis 事件网格概述 

Azure Cache for Redis 事件（例如修补、缩放、导入/导出 (RDB) 事件使用 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/) 推送到 Azure Functions、Azure 逻辑应用，甚至是自己的 http 侦听器。 事件网格通过丰富的重试策略和死信向应用程序提供可靠的事件传递。

若要查看 Azure Cache for Redis 支持的事件的完整列表，请参阅 [Azure cache For Redis 事件架构](../event-grid/event-schema-azure-cache.md) 一文。

若要试用 Azure Cache for Redis 事件，请参阅以下任一快速入门：

|若要使用此工具：    |请参阅此快速入门： |
|--|-|
|Azure 门户    |[快速入门：将 Azure Cache for Redis 事件路由到具有 Azure 门户的 web 终结点](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[快速入门：通过 PowerShell 将 Azure Cache for Redis 事件路由到 web 终结点](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[快速入门：将 Azure Cache for Redis 事件路由到 Azure CLI 的 web 终结点](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>事件模型

事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 此图展示了事件发布者、事件订阅和事件处理程序之间的关系。

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="事件网格模型。":::

首先，将终结点订阅到事件。 然后，在触发某个事件时，事件网格服务会将有关该事件的数据发送到终结点。

若要查看，请参阅 [Azure Cache For Redis 事件架构](../event-grid/event-schema-azure-cache.md) 一文：

> [!div class="checklist"]
> * Azure Cache for Redis 事件的完整列表，以及每个事件的触发方式。
> * 事件网格会针对每个此类事件发送的数据的示例。
> * 显示在数据中的每个键值对的用途。


## <a name="best-practices-for-consuming-events"></a>使用事件的最佳实践

处理 Redis 事件的 Azure 缓存的应用程序应遵循以下建议做法：
> [!div class="checklist"]
> * 由于可以将多个订阅配置为将事件路由到同一个事件处理程序，因此不要假设事件来自特定的源，而是检查消息的主题，以确保它来自于所需的 Redis 实例的 Azure Cache。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 适用于 Redis 事件的 Azure 缓存保证至少一次传递到订阅服务器，这可确保输出所有消息。 但是，由于订阅重试或可用性，有时可能会出现重复消息。 若要详细了解消息传递和重试，请参阅[事件网格消息传递和重试](../event-grid/delivery-and-retry.md)。


## <a name="next-steps"></a>后续步骤

详细了解事件网格，并为 Redis 事件提供 Azure 缓存，尝试：

- [关于事件网格](../event-grid/overview.md)
- [Azure Cache for Redis 事件架构](../event-grid/event-schema-azure-cache.md)
- [通过 Azure CLI 将 Redis 事件的 Azure Cache 路由到 web 终结点](cache-event-grid-quickstart-cli.md)
- [通过 Azure 门户将 Redis 事件的 Azure Cache 路由到 web 终结点](cache-event-grid-quickstart-portal.md)
- [通过 PowerShell 将 Redis 事件的 Azure Cache 路由到 web 终结点](cache-event-grid-quickstart-powershell.md)
