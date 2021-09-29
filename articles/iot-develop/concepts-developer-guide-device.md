---
title: 设备开发人员指南 (C) - IoT 即插即用 | Microsoft Docs
description: 介绍了适用于 C 设备开发人员的 IoT 即插即用
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-twenty-seven
ms.openlocfilehash: 528f1270be0065622ba7c79db33f8e4eed93bd74
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580441"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT 即插即用设备开发人员指南

借助 IoT 即插即用，你可以生成将功能播发到 Azure IoT 应用程序的 IoT 设备。 当客户将 IoT 即插即用设备连接到已启用 IoT 即插即用的应用程序时，无需手动配置。

IoT 设备可以直接实现，也可以使用[模块](../iot-hub/iot-hub-devguide-module-twins.md)或 [IoT Edge 模块](../iot-edge/about-iot-edge.md)。

本指南介绍了创建遵循 [IoT 即插即用约定](../iot-develop/concepts-convention.md)的设备、模块或 IoT Edge 模块所需的基本步骤。

若要生成 IoT 即插即用设备、模块或 IoT Edge 模块，请按以下步骤操作：

1. 确保你的设备使用 MQTT 或 MQTT over WebSockets 协议连接到 Azure IoT 中心。
1. 创建[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 模型来描述你的设备。 若要了解详细信息，请参阅[了解 IoT 即插即用模型中的组件](concepts-modeling-guide.md)。
1. 更新设备或模块，以宣布 `model-id` 作为设备连接的一部分。
1. 遵循 [IoT 即插即用约定](concepts-convention.md)实现遥测、属性和命令

在设备或模块实现就绪后，使用 [Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)来验证设备是否遵循 IoT 即插即用约定。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-embedded-c"

[!INCLUDE [iot-pnp-device-devguide-embedded-c](../../includes/iot-pnp-device-devguide-embedded-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>后续步骤

知悉，你已经了解了 IoT 即插即用设备开发，下面是一些额外的资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [了解 IoT 即插即用模型中的组件](concepts-modeling-guide.md)
- [IoT 即插即用服务开发人员指南](concepts-developer-guide-service.md)
