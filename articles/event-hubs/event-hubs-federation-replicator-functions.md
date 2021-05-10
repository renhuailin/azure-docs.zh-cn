---
title: 事件复制任务和应用程序 - Azure 事件中心 | Microsoft Docs
description: 本文概述了如何使用 Azure Functions 来生成事件复制任务和应用程序
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97663582"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>使用 Azure Functions 来生成事件复制任务和应用程序

> [!TIP]
> 对于需要考虑事件的有效负载并对其进行转换、聚合、扩充或约简操作的所有有状态复制任务，请使用 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)（而不是 Azure Functions）。

如[事件复制和跨区域联合](event-hubs-federation-overview.md)一文所述，事件中心对之间以及事件中心与其他事件流源和目标之间的事件流无状态复制倾向于使用 Azure Functions。

[Azure Functions](../azure-functions/functions-overview.md) 是一种可缩放且可靠的执行环境，用于配置和运行无服务器应用程序，包括事件复制和联合任务。

在本概述中，你将了解 Azure Functions 针对此类应用程序提供的内置功能、可为转换任务调整和修改的代码块，以及如何配置 Azure Functions 应用程序，使其与事件中心和其他 Azure 消息传送服务完美集成。 有关更多详细信息，本文将指向 Azure Functions 文档。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









