---
title: 消息复制任务和应用程序-Azure 服务总线 |Microsoft Docs
description: 本文概述了如何使用 Azure Functions 生成消息复制任务和应用程序。
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657363"
---
# <a name="message-replication-tasks-and-applications"></a>消息复制任务和应用程序

如 [消息复制和跨区域联合一](service-bus-federation-overview.md) 文中所述，在服务总线实体和服务总线以及其他消息源和目标之间进行的消息序列的复制通常在 Azure Functions 上倾向。

[Azure Functions](../azure-functions/functions-overview.md) 是一种可缩放且可靠的执行环境，用于配置和运行无服务器应用程序，包括 [消息复制和联合](service-bus-federation-overview.md) 任务。

在本概述中，你将了解有关此类应用程序的 Azure Functions 内置功能，以及你可以对转换任务进行修改和修改的代码块，以及如何配置 Azure Functions 应用程序，使其与服务总线和其他 Azure 消息传递服务完美集成。 对于许多详细信息，本文将指向 Azure Functions 文档。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
