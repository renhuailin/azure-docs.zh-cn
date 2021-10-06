---
title: Azure IoT 中心 SDK | Microsoft Docs
description: 指向可用于生成设备应用和后端应用的 Azure IoT 中心 SDK 的链接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: e8bfc95d80aa58b9781258c1b87902c2e8d0c37f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458579"
---
# <a name="azure-iot-hub-sdks"></a>Azure IoT 中心 SDK

有两种类别的软件开发工具包 (SDK) 适用于 IoT 中心：

* [IoT 中心服务 SDK](#azure-iot-hub-service-sdks) 可用于生成后端应用程序来管理 IoT 中心，并视需要发送消息、计划作业、调用直接方法或向 IoT 设备或模块发送相应属性更新。

* [IoT 中心设备 SDK](../iot-develop/about-iot-sdks.md) 可用于使用设备客户端或模块客户端生成在 IoT 设备上运行的应用。 这些应用将遥测发送到 IoT 中心，并可以选择从 IoT 中心接收消息、作业、方法或孪生更新。 可以使用这些 SDK 构建使用 [Azure IoT 即插即用](../iot-develop/overview-iot-plug-and-play.md)约定和型号的设备应用，以便将其功能播发到启用了 IoT 即插即用的应用程序。 还可以使用模块客户端为 [Azure IoT Edge 运行时](../iot-edge/about-iot-edge.md)创建[模块](../iot-edge/iot-edge-modules.md)。

此外，我们还提供了一组 SDK 来处理[设备预配服务](../iot-dps/about-iot-dps.md)。

* **预配设备 SDK**：可用于构建在 IoT 设备上运行的与设备预配服务进行通信的应用。

* **预配服务 SDK**：可用于构建后端应用程序来管理设备预配服务中的注册。

了解[使用 Azure IoT SDK 执行开发的益处](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)。

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT 中心服务 SDK

Azure IoT 服务 SDK 包含的代码可帮助生成直接与 IoT 中心进行交互以管理设备和安全性的应用程序。

| 平台  | 程序包 | 代码存储库 | 示例 |  参考 |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [引用](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | [引用](/java/api/com.microsoft.azure.sdk.iot.service) |
| 节点 | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [引用](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [引用](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [引用](/javascript/api/azure-iothub/) |

适用于 iOS 的 Azure IoT 中心服务 SDK：

* 通过 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) 安装
* [示例](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure 预配 SDK

**Microsoft Azure 预配 SDK** 使你可以使用 [设备预配服务](../iot-dps/about-iot-dps.md)将设备预配到 IoT 中心。

| 平台 | 程序包 | 源代码 | 参考 |
| -----|-----|-----|-----|
| .NET|[设备 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/)、[服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[引用](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get、MBED、Arduino IDE 或 iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[引用](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/main/provisioning)|[引用](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[设备 SDK](https://badge.fury.io/js/azure-iot-provisioning-device)、[服务 SDK](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)|[引用](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[设备 SDK](https://pypi.org/project/azure-iot-device/)、[服务 SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[设备引用](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient)、[服务引用](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT 中心设备 SDK

Microsoft Azure IoT 设备 SDK 包含的代码可帮助构建连接到 Azure IoT 中心服务并由这些服务管理的应用程序。

有关 IoT 中心设备 SDK 的详细信息，请参阅 [IoT 设备开发文档](../iot-develop/about-iot-sdks.md)。

## <a name="sdk-and-hardware-compatibility"></a>SDK 和硬件兼容性

有关选择设备 SDK 的详细信息，请参阅 [Azure IoT 设备 SDK 概述](../iot-develop/about-iot-sdks.md)。

有关与特定硬件设备的 SDK 兼容性的详细信息，请参阅 [Azure IoT 认证设备目录](https://devicecatalog.azure.com/)或个体存储库。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>后续步骤

与使用 Azure IoT SDK 进行开发相关的文档：

* 了解如何使用 IoT 中心 SDK [管理连接和可靠的消息传送](iot-hub-reliability-features-in-sdks.md)。
* 了解如何[针对移动平台进行开发](iot-hub-how-to-develop-for-mobile-devices.md)，例如 iOS 和 Android。
* [IoT 设备开发文档](../iot-develop/about-iot-sdks.md)

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)
* [用于设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)
* [配额和限制](iot-hub-devguide-quotas-throttling.md)
* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)
* [IoT 中心 REST API 参考](/rest/api/iothub/)
