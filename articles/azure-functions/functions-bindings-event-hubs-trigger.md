---
title: 适用于 Azure Functions 的 Azure 事件中心触发器
description: 了解如何在 Azure Functions 中使用 Azure 事件中心触发器。
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608899"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure 事件中心触发器

本文介绍如何使用适用于 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-about.md)触发器。 Azure Functions 支持事件中心的触发器和[输出绑定](functions-bindings-event-hubs-output.md)。

有关设置和配置详细信息，请参阅[概述](functions-bindings-event-hubs.md)。

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.json 设置

[host.json](functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。 有关可用设置的详细信息，请参阅 [host.json 设置](functions-bindings-event-hubs.md#hostjson-settings)部分。

## <a name="next-steps"></a>后续步骤

- [将事件写入事件流（输出绑定）](./functions-bindings-event-hubs-output.md)
