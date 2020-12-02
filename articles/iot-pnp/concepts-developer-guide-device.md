---
title: 设备开发人员指南 (C) IoT 即插即用 |Microsoft Docs
description: 适用于 C 设备开发人员的 IoT 即插即用说明
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 3aa236570e518b142adb8382387a8cdea4fc08a0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453275"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT 即插即用设备开发人员指南

IoT 即插即用允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

智能设备可以直接实现、使用 [模块](../iot-hub/iot-hub-devguide-module-twins.md)或使用 [IoT Edge 模块](../iot-edge/about-iot-edge.md)。

本指南介绍了按照 [IoT 即插即用约定](../iot-pnp/concepts-convention.md)创建设备、模块或 IoT Edge 模块所需的基本步骤。

若要构建 IoT 即插即用设备、模块或 IoT Edge 模块，请执行以下步骤：

1. 确保你的设备使用 MQTT 或 MQTT over Websocket 协议连接到 Azure IoT 中心。
1. 创建 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 模型来描述你的设备。 若要了解详细信息，请参阅 [了解 IoT 即插即用模型中的组件](concepts-components.md)。
1. 更新设备或模块，以便在 `model-id` 设备连接过程中通告。
1. 使用[IoT 即插即用约定](concepts-convention.md)实现遥测、属性和命令

设备或模块实现准备就绪后，请使用 [Azure iot 浏览器](howto-use-iot-explorer.md) 验证设备是否遵循 IoT 即插即用约定。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

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

现在，你已了解 IoT 即插即用设备开发，以下是一些其他资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [模型组件](concepts-components.md)
- [安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)
- [IoT 即插即用服务开发人员指南](concepts-developer-guide-service.md)