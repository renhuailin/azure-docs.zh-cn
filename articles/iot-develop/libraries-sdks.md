---
title: IoT 即插即用库和 SDK
description: 有关可用于开发启用 IoT 即插即用的解决方案的设备和服务库的信息。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 7993dce658f6146df183a95808926ec22ddef931
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457727"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>适用于 IoT 即插即用的 Microsoft SDK

通过 IoT 即插即用库和 SDK，开发人员能够在多个平台上使用各种编程语言来构建 IoT 解决方案。 下表提供了一些链接，这些链接指向可帮助你入门的示例和快速入门：

## <a name="device-sdks"></a>设备 SDK

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| C - 设备 | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [连接到 IoT 中心](tutorial-connect-device.md) | [引用](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - 设备 | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/) | [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples) | [连接到 IoT 中心](tutorial-connect-device.md) | [引用](/dotnet/api/microsoft.azure.devices.client) |
| Java - 设备 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/main/) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample) | [连接到 IoT 中心](tutorial-connect-device.md) | [引用](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python - 设备 | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [连接到 IoT 中心](tutorial-connect-device.md) | [引用](/python/api/azure-iot-device/azure.iot.device) |
| Node - 设备 | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [连接到 IoT 中心](tutorial-connect-device.md) | [引用](/javascript/api/azure-iot-device/) |
| Embedded C - 设备 | 空值 | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [示例](tutorial-connect-device.md?pivots=programming-language-embedded-c#samples) | [如何使用 Embedded C](tutorial-connect-device.md?pivots=programming-language-embedded-c) | 空值

## <a name="service-sdks"></a>服务 SDK

| 平台  | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| .NET - IoT 中心服务 | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/service/PnpServiceSamples) | 空值 | [引用](/dotnet/api/microsoft.azure.devices) |
| Java - IoT 中心服务 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | 空值 | [引用](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node - IoT 中心服务 | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | 空值 | [引用](/javascript/api/azure-iothub/) |
| Python - 数字孪生服务 | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [与 IoT 中心数字孪生 API 交互](tutorial-service.md) | 空值 |
| Node - 数字孪生服务 | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [与 IoT 中心数字孪生 API 交互](tutorial-service.md) | 空值 |

## <a name="next-steps"></a>后续步骤

若要尝试使用 SDK 和库，请参阅[开发人员指南](concepts-developer-guide-device.md)以及[设备教程](tutorial-connect-device.md)和[服务教程](tutorial-service.md)。