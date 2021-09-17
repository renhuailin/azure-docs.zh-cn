---
title: 服务开发人员指南 - IoT 即插即用 | Microsoft Docs
description: 介绍了适用于服务开发人员的 IoT 即插即用
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 15a381f847082e0853db307cc60893eaeeb43e96
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604157"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 即插即用服务开发人员指南

借助 IoT 即插即用，你可以生成将功能播发到 Azure IoT 应用程序的 IoT 设备。 当客户将 IoT 即插即用设备连接到已启用 IoT 即插即用的应用程序时，无需手动配置。

借助 IoT 即插即用，你可以使用已通过 IoT 中心公布其模型 ID 的设备。 例如，你可以直接访问设备的属性和命令。

若要使用连接到 IoT 中心的 IoT 即插即用设备，请使用 IoT 服务 SDK 之一：

## <a name="service-sdks"></a>服务 SDK

使用解决方案中的 Azure IoT 服务 SDK 与设备和模块进行交互。 例如，可以使用服务 SDK 来读取和更新孪生体属性以及调用命令。 支持的语言包括 C#、Java、Node.js 和 Python。

服务 SDK 允许你从解决方案（如桌面或 Web 应用程序）访问设备信息。 服务 SDK 包括两个可用于检索模型 ID 的命名空间和对象模型：

- IoT 中心服务客户端。 此服务将模型 ID 公开为设备孪生体属性。

- 数字孪生体客户端。 新的数字孪生体 API 在[数字孪生体定义语言 (DTDL)](concepts-digital-twin.md) 模型构造（例如组件、属性和命令）上运行。 数字孪生体 API 使解决方案开发者可以更轻松地创建 IoT 即插即用解决方案。

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
- [IoT 即插即用建模指南](concepts-modeling-guide.md)