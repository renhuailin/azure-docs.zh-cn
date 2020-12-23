---
title: 服务开发人员指南-IoT 即插即用 |Microsoft Docs
description: 面向服务开发人员的 IoT 即插即用说明
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521354"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 即插即用服务开发人员指南

IoT 即插即用允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

IoT 即插即用允许你使用已通过 IoT 中心宣布其模型 ID 的设备。 例如，你可以直接访问设备的属性和命令。

若要使用已连接到 IoT 中心的 IoT 即插即用设备，请使用 IoT 服务 Sdk 之一：

## <a name="service-sdks"></a>服务 SDK

使用解决方案中的 Azure IoT 服务 Sdk 与设备和模块交互。 例如，你可以使用服务 Sdk 来读取和更新克隆属性和调用命令。 支持的语言包括 c #、Java、Node.js 和 Python。

服务 Sdk 允许您从解决方案（如桌面或 web 应用程序）访问设备信息。 服务 Sdk 包括两个可用于检索模型 ID 的命名空间和对象模型：

- Iot 中心服务客户端。 此服务将模型 ID 公开为设备克隆的属性。

- 数字孪生客户端。 新的数字孪生 API 操作 [数字孪生定义语言 (DTDL) ](concepts-digital-twin.md) 模型构造，如组件、属性和命令。 数字克隆 Api 使解决方案构建者可以更轻松地创建 IoT 即插即用解决方案。

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
- [安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)