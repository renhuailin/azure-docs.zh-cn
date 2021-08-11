---
title: 教程：将事件中心数据发送到数据仓库 - 事件网格
description: 介绍如何通过 Azure Functions 和事件网格触发器将事件中心捕获的数据存储在 Azure Synapse Analytics 中。
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2887cdc1b4666daf73a21a76907f80d50d345d82
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413961"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>教程：将大数据流式传输到数据仓库
Azure [事件网格](overview.md)是一项智能事件路由服务，可用于对应用和服务的通知或事件作出响应。 例如，它可以触发 Azure Function 来处理已捕获到 Blob 存储或 Data Lake Store 的事件中心数据。 本[示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)演示了如何使用事件网格和 Azure Functions 将捕获的事件中心数据从 Blob 存储迁移到 Azure Synapse Analytics（特别是专用的 SQL 池）。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](./includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 消息传递服务之间的差异，请参阅[在提供消息的 Azure 服务之间进行选择](compare-messaging-services.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 有关事件中心捕获的介绍，请参阅[使用 Azure 门户启用事件中心捕获](../event-hubs/event-hubs-capture-enable-through-portal.md)。
* 若要详细了解如何设置和运行示例，请参阅[事件中心捕获和事件网格示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。
