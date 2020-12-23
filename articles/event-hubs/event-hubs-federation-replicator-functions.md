---
title: 事件复制任务和应用程序-Azure 事件中心 |Microsoft Docs
description: 本文概述了如何使用 Azure Functions 生成事件复制任务和应用程序。
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663582"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>具有 Azure Functions 的事件复制任务和应用程序

> [!TIP]
> 对于需要考虑事件的负载以及转换、聚合、充实或减少这些任务的所有有状态复制任务，请使用 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 而不是 Azure Functions。

如 [事件复制和跨区域联合](event-hubs-federation-overview.md) 本文中所述，事件中心对之间以及事件中心与事件中心之间以及其他事件流源和目标之间的事件流之间的无状态复制与 Azure Functions 上的倾向。

[Azure Functions](../azure-functions/functions-overview.md) 是一种可缩放且可靠的执行环境，用于配置和运行无服务器应用程序，包括事件复制和联合任务。

在本概述中，你将了解有关此类应用程序的 Azure Functions 内置功能，以及你可以为转换任务改编和修改的代码块，以及如何配置 Azure Functions 应用程序，以便它与事件中心和其他 Azure 消息传递服务完美集成。 对于许多详细信息，本文将指向 Azure Functions 文档。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









