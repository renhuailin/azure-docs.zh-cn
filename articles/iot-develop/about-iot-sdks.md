---
title: Azure IoT 设备 SDK 选项概述
description: 了解如何根据开发角色和任务选择要使用的 Azure IoT 设备 SDK。
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 2374755dc1874b1dcd421daa97fc77535ef99286
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455430"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT 设备 SDK 概述

Azure IoT 设备 SDK 是一组设备客户端库、开发人员指南、示例和文档。 设备 SDK 有助于以编程方式将设备连接到 Azure IoT 服务。

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="显示各种 Azure IoT SDK 的关系图":::

如关系图所示，有几种设备 SDK 可以满足你的设备和编程语言需求。 有关选择合适的设备 SDK 的指南，请参阅[我应该使用哪种 SDK](#which-sdk-should-i-use)。 在后端还有 Azure IoT 服务 SDK 可用于将基于云的应用程序与 Azure IoT 服务连接。 本文重点介绍设备 SDK，但是你可以在[此处](#service-sdks)了解有关 Azure 服务 SDK 的详细信息。

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>为什么我应该使用 Azure IoT 设备 SDK？

若要将设备连接到 Azure IoT，可以生成自定义连接层或使用 Azure IoT 设备 SDK。 使用 Azure IoT 设备 SDK 具有以下优点：

| 开发成本 &nbsp; &nbsp; &nbsp; &nbsp; | 自定义连接层 | Azure IoT 设备 SDK |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| 支持 | 需要支持和记录你生成的任何内容 | 有权访问 Microsoft 支持（GitHub、Microsoft Q&A、Microsoft Docs 和客户支持团队） |
| 新功能 | 需要将新的 Azure 功能添加到自定义中间件 | 可以立即使用 Microsoft 不断添加到 IoT SDK 中的新功能 |
| 投资额 | 投入数百小时的嵌入式开发来设计、生成、测试和维护自定义版本 | 可以使用免费的开源工具。 与 SDK 相关的唯一成本是学习曲线。 |

## <a name="which-sdk-should-i-use"></a>我该使用哪一个 SDK？

Azure IoT 设备 SDK 支持多种热门编程语言，包括 C、C#、Java、Node.js 和 Python。 选择 SDK 时有两个主要考虑因素：设备功能和团队对于编程语言的熟悉程度。

### <a name="device-capabilities"></a>设备功能

选择 SDK 时，你需要考虑所使用的设备的限制。 受限设备是指具有单个微控制器 (MCU) 和有限内存的设备。 如果你使用的是受限设备，我们建议你使用[嵌入式 C SDK](#embedded-c-sdk)。 此 SDK 旨在提供连接到 Azure IoT 的最小功能集。 还可以选择针对嵌入式设备进行了最佳优化的组件（MQTT 客户端、TLS 和套接字库）。 如果受限设备还运行 Azure RTOS，你可以使用 Azure RTOS 中间件来连接到 Azure IoT。 Azure RTOS 中间件会使用额外功能包装 Embedded C SDK，以简化 Azure RTOS 设备与云的连接。

不受限的设备是指具有更可靠的 CPU 的设备，它能够运行操作系统来支持 .NET 或 Python 等语言运行时。 如果你使用的是不受限的设备，则主要考虑因素是对语言的熟悉程度。

### <a name="your-teams-familiarity-with-the-programming-language"></a>你的团队对于编程语言的熟悉程度

Azure IoT 设备 SDK 可以由多种语言实现，因此你可以选择你喜欢的语言。 设备 SDK 还可以与其它熟悉的特定于语言的工具集成。 如果能够使用熟悉的开发语言和工具，你的团队就能够优化研究、原型设计、产品开发和日常维护的开发周期。

请尽可能选择开发团队熟悉的 SDK。 所有 Azure IoT SDK 都是开源的，并且有几个示例可供团队在提交到特定 SDK 之前进行评估和测试。

## <a name="how-can-i-get-started"></a>如何入门？

首先要了解 Azure 设备 SDK 的 GitHub 存储库。 还可以尝试[快速入门](quickstart-send-telemetry-central.md)，其中展示了如何快速使用 SDK 向 Azure IoT 发送遥测。

入门选项取决于你的设备类型：
- 对于受限设备，请使用 [Embedded C SDK](#embedded-c-sdk)。 
- 对于在 Azure RTOS 上运行的设备，可以使用 [Azure RTOS 中间件](#azure-rtos-middleware)进行开发。 
- 对于不受限的设备，可以根据所选语言来[选择 SDK](#unconstrained-device-sdks)。 

### <a name="constrained-device-sdks"></a>受限的设备 SDK
这些 SDK 专门用于在计算或内存资源有限的设备上运行。 有关常见设备类型的详细信息，请参阅 [Azure IoT 设备类型概述](concepts-iot-device-types.md)。

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub 存储库](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [示例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [参考文档](https://azure.github.io/azure-sdk-for-c/)
* [如何生成 Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [受限设备的大小图表](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS 中间件

* [GitHub 存储库](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [入门指南](https://github.com/azure-rtos/getting-started)和[更多示例](https://github.com/azure-rtos/samples)
* [参考文档](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>不受限的设备 SDK
这些 SDK 可以在支持高级语言运行时的任何设备上运行。 其中包括电脑、Raspberry Pi 和智能手机等设备。 它们主要由语言来区分，因此你可以选择最适合自己的团队和场景的库。

#### <a name="c-device-sdk"></a>C 设备 SDK
* [GitHub 存储库](https://github.com/Azure/azure-iot-sdk-c)
* [示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [包](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [参考文档](/azure/iot-hub/iot-c-sdk-ref/)
* [Edge 模块参考文档](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [编译 C 设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [将 C SDK 移植到其他平台](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [开发人员文档](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)，包含在不同平台上进行交叉编译和入门的信息
* [Azure IoT 中心 C SDK 资源消耗信息](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# 设备 SDK

* [GitHub 存储库](https://github.com/Azure/azure-iot-sdk-csharp)
* [示例](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [包](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [参考文档](/dotnet/api/microsoft.azure.devices)
* [Edge 模块参考文档](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java 设备 SDK

* [GitHub 存储库](https://github.com/Azure/azure-iot-sdk-java)
* [示例](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples)
* [包](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-device-sdk)
* [参考文档](/java/api/com.microsoft.azure.sdk.iot.device)
* [Edge 模块参考文档](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js 设备 SDK

* [GitHub 存储库](https://github.com/Azure/azure-iot-sdk-node)
* [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [包](https://www.npmjs.com/package/azure-iot-device)
* [参考文档](/javascript/api/azure-iot-device/)
* [Edge 模块参考文档](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python 设备 SDK

* [GitHub 存储库](https://github.com/Azure/azure-iot-sdk-python)
* [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [包](https://pypi.org/project/azure-iot-device/)
* [参考文档](/python/api/azure-iot-device)
* [Edge 模块参考文档](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>服务 SDK
Azure IoT 还提供服务 SDK，使你能够生成解决方案端应用程序来管理设备、获取见解、可视化数据等。 这些 SDK 特定于每种 Azure IoT 服务，并且支持 C#、Java、JavaScript 和 Python 以简化开发体验。 

#### <a name="iot-hub"></a>IoT 中心

通过 IoT 中心服务 SDK，可以生成可以与 IoT 中心轻松交互的应用程序，以管理设备和安全性。 可以使用这些 SDK 发送云到设备的消息、调用设备上的直接方法，以及更新设备属性等。

[详细了解 IoT 中心](https://azure.microsoft.com/services/iot-hub/) | [尝试控制设备](../iot-hub/quickstart-control-device.md) 

C# IoT 中心服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service) | [包](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service/samples) | [参考文档](/dotnet/api/microsoft.azure.devices)

Java IoT 中心服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-java/tree/main/service) | [包](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples) | [参考文档](/java/api/com.microsoft.azure.sdk.iot.service)

JavaScript IoT 中心服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [包](https://www.npmjs.com/package/azure-iothub) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [参考文档](/javascript/api/azure-iothub/)

Python IoT 中心服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [包](https://pypi.python.org/pypi/azure-iot-hub/) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [参考文档](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure 数字孪生

Azure 数字孪生是一个服务型平台 (PaaS)，可用于基于整个环境的数字模型创建知识图。 这些环境可能是建筑物、工厂、农场、能源网络、铁路，体育场等，甚至是整个城市。 这些数字模型可用于获取洞察力，以推动产品改进、运营优化、成本降低和客户体验突破。 Azure IoT 提供服务 SDK，可使你轻松生成使用 Azure 数字孪生的功能的应用程序。

[有关 Azure 数字孪生的详细信息](https://azure.microsoft.com/services/digital-twins/) | [对 ADT 应用程序进行编码](../digital-twins/tutorial-code.md) 

C# ADT 服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [包](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [参考文档](/dotnet/api/overview/azure/digitaltwins/client)

Java ADT 服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [包](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [参考文档](/java/api/overview/azure/digitaltwins/client)

Node.js ADT 服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [包](https://www.npmjs.com/package/@azure/digital-twins-core) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [参考文档](/javascript/api/@azure/digital-twins-core/)

Python ADT 服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [包](https://pypi.org/project/azure-digitaltwins-core/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [参考文档](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>设备预配服务

IoT 中心设备预配服务 (DPS) 是 IoT 中心的帮助器服务，支持零接触、实时预配到适当的 IoT 中心，不需要人为干预。 DPS 能够以安全且可缩放的方式预配数百万台设备。 通过 DPS 服务 SDK，可以生成可以通过创建注册组和执行批量操作来安全管理设备的应用程序。

[有关设备预配服务的详细信息](../iot-dps/index.yml) | [尝试为 X.509 设备创建组注册](../iot-dps/quick-enroll-device-x509.md) 

C# 设备预配服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service) | [包](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service/samples) | [参考文档](/dotnet/api/microsoft.azure.devices.provisioning.service)

Java 设备预配服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-java/tree/main/provisioning/provisioning-service-client/src) | [包](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [参考文档](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

Node.js 设备预配服务 SDK：[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [包](https://www.npmjs.com/package/azure-iot-provisioning-service) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [参考文档](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>后续步骤

* [快速入门：将遥测数据发送到 IoT Central](quickstart-send-telemetry-central.md)
* [快速入门：将遥测数据发送到 IoT 中心](quickstart-send-telemetry-iot-hub.md)
* [快速入门：将 MXCHIP AZ3166 Devkit 连接到 IoT Central](quickstart-devkit-mxchip-az3166.md)
* 详细了解[使用 Azure IoT SDK 进行开发的好处](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)