---
title: 配置 Microsoft OPC 发布服务器
description: 在本教程中，您将了解如何在独立模式下配置 OPC 发布服务器。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 3667a201e293a844fbcd60b4f00c43aee03ca550
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015645"
---
# <a name="tutorial-configure-the-opc-publisher"></a>教程：配置 OPC 发布服务器

在本教程中，包含有关 OPC 发布服务器配置的信息。 可以使用多个接口对其进行配置。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 通过配置文件配置 OPC 发布服务器
> * 通过命令行参数配置 OPC 发布服务器
> * 通过 IoT 中心直接方法配置 OPC 发布服务器
> * 通过基于云的辅助 REST 微服务配置 OPC 发布服务器

## <a name="configuring-security"></a>配置安全性

IoT Edge 为 OPC 发布服务器提供了自动访问 IoT 中心所用的安全性配置。 OPC 发布服务器还可以通过 `dc` 命令行参数指定用于访问 IoT 中心的设备连接字符串，以独立 Docker 容器的方式运行。 可以创建用于 IoT 中心的设备及其通过 Azure 门户检索的连接字符串。

为了访问启用 OPC UA 的资产，OPC UA 使用 X.509 证书及其关联的私钥。 这称为 OPC UA 应用程序身份验证，以及 OPC UA 用户身份验证。 OPC 发布服务器使用基于文件系统的证书存储来管理所有应用程序证书。 在启动过程中，OPC 发布服务器检查是否有可在此证书存储中使用的证书，并创建新的自签名证书和新的关联私钥（如果没有）。 自签名证书提供弱身份验证，因为它们不是由受信任的证书颁发机构签名的，但至少可以使用这种方式对启用了 OPC UA 的资产的通信进行加密。

通过 `"UseSecurity": true,` 标志在配置文件中启用安全性。 OPC 发布服务器应连接到的 OPC UA 服务器上提供的最安全的终结点会自动选择。
默认情况下，OPC 发布服务器使用匿名用户身份验证（以及上述应用程序身份验证）。 但是，OPC 发布服务器还支持使用用户名和密码进行用户身份验证。 这可以通过下述 REST API 配置接口或如下所示的配置文件来指定：
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
此外，OPC 发布服务器版本 2.5 及更低版本会加密配置文件中的用户名和密码。 版本 2.6 及更高版本仅支持纯文本形式的用户名和密码。 这将在 OPC 发布服务器的下一个版本中得到改进。

若要在重新启动时持久保存 OPC 发布服务器的安全性配置，则必须将位于证书存储目录中的证书和私钥映射到 IoT Edge 主机操作系统文件系统。 请参阅上面的“在 Azure 门户中指定容器创建选项”。

## <a name="configuration-via-configuration-file"></a>通过配置文件配置

配置 OPC 发布服务器的最简单方法是通过配置文件。 示例配置文件以及有关其格式的文档是通过此存储库中的 [`publishednodes.json`](https://raw.githubusercontent.com/Azure/Industrial-IoT/main/components/opc-ua/src/Microsoft.Azure.IIoT.OpcUa.Edge.Publisher/tests/Engine/publishednodes.json) 文件提供的。
配置文件语法随时间推移而更改，OPC 发布服务器仍可读取旧格式，但在保存配置时将它们转换为最新格式，并定期以自动方式进行。

基本配置文件如下所示：
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA 资产在数据更改后仅向 OPC 发布服务器发送数据更改，从而优化网络带宽。 如果需要更频繁地或按固定间隔发布数据更改，OPC 发布服务器将对每个已配置的数据项支持“检测信号”，通过在数据项的配置中另外指定 HeartbeatInterval 键可启用此检测信号。 以秒为单位指定此间隔：
```
 "HeartbeatInterval": 3600,
```

OPC UA 资产在 OPC 发布服务器第一次连接到数据项时，始终发送数据项的当前值。 若要阻止将这些数据发布到 IoT 中心，还可以在数据项的配置中另外指定 SkipFirst 键：
```
 "SkipFirst": true,
```

也可以通过命令行选项全局启用这两种设置。

## <a name="configuration-via-command-line-arguments"></a>通过命令行参数配置

可以使用多个命令行参数设置 OPC 发布服务器的全局设置。 [此处](reference-command-line-arguments.md)对它们进行了说明。


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>通过内置 OPC UA 服务器接口配置

>[!NOTE] 
> 此功能仅在 OPC 发布服务器版本 2.5 及更低版本中提供。

OPC 发布服务器具有在端口 62222 上运行的内置 OPC UA 服务器。 它实现了三个 OPC UA 方法：

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

此接口可使用 OPC UA 客户端应用程序进行访问，例如 [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html)。

## <a name="configuration-via-iot-hub-direct-methods"></a>通过 IoT 中心直接方法配置

>[!NOTE] 
> 此功能仅在 OPC 发布服务器版本 2.5 及更低版本中提供。

OPC 发布服务器实现了以下 [IoT 中心直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)，可通过使用 [IoT 中心设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 从应用程序（从世界各地的任何地方）调用：

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

我们提供了一个[示例配置应用程序](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration)和一个用于通过此接口从 OPC 发布服务器开放源代码[读取诊断信息的应用程序](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)。

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>通过基于云的辅助 REST 微服务配置

>[!NOTE] 
> 此功能仅在 OPC 发布服务器版本 2.6 及更高版本中提供。

[此处](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)介绍了一种具有 REST 接口的基于云的辅助微服务。 它可用于通过 OpenAPI 兼容的接口（例如通过 Swagger）配置 OPC 发布服务器。

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>通过单独的配置文件配置简单的 JSON 遥测格式

>[!NOTE] 
> 此功能仅在 OPC 发布服务器版本 2.5 及更低版本中提供。

OPC 发布服务器允许通过一个单独的配置文件（可通过 tc 命令行选项指定）来筛选非标准化的简单遥测格式部分。 如果未指定配置文件，则会将完整的 JSON 遥测格式发送到 IoT 中心。 [此处](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)描述了单独遥测配置文件的格式。

## <a name="next-steps"></a>后续步骤
现在已配置 OPC 发布服务器，下一步是了解如何优化 Edge 模块的性能和内存：

> [!div class="nextstepaction"]
> [性能和内存优化](tutorial-publisher-performance-memory-tuning-opc-publisher.md)
