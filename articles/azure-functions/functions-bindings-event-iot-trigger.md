---
title: 适用于 Azure Functions 的 Azure IoT 中心触发器
description: 了解如何在 Azure Functions 中响应发送到 IoT 中心事件流的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612280"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure IoT 中心触发器

本文介绍如何使用 IoT 中心的 Azure Functions 绑定。 IoT 中心支持基于 [Azure 事件中心绑定](functions-bindings-event-hubs.md)。

有关设置和配置详细信息，请参阅[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 虽然下述代码示例使用事件中心 API，但给定的语法适用于 IoT 中心函数。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.json 属性

[host.json](functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。 有关可用设置的详细信息，请参阅 [host.json 设置](functions-bindings-event-iot.md#hostjson-settings)部分。

## <a name="next-steps"></a>后续步骤

- [将事件写入事件流（输出绑定）](./functions-bindings-event-iot-output.md)
