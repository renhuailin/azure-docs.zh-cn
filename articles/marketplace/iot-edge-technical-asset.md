---
title: 在 Azure 市场上准备 IoT Edge 模块技术资产
description: 在 Azure 市场上准备 IoT Edge 模块技术资产。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 8ab847eee55eb9f2fc4d0d4ee7de1fa9eef3bcf0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294294"
---
# <a name="prepare-iot-edge-module-technical-assets"></a>准备 IoT Edge 模块技术资产

本文介绍在 Azure 市场中发布物联网 (IoT) Edge 模块技术资产之前，这些资产必须满足的要求。

## <a name="get-started"></a>入门

IoT Edge 模块是可在 IoT Edge 设备上运行的与 Docker 兼容的容器。

- 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](../iot-edge/iot-edge-modules.md)。
- 若要开始进行 IoT Edge 模块开发，请参阅[开发自己的 IoT Edge 模块](../iot-edge/module-development.md)。

## <a name="technical-requirements"></a>技术要求

IoT Edge 模块必须满足以下技术要求才能在 Azure 市场中认证和发布。

### <a name="platform-support"></a>平台支持

IoT Edge 模块必须支持以下平台选项之一：

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的第 1 层平台

你的模块必须支持 IoT Edge 所支持的全部第 1 层平台（已在 [Azure IoT Edge 支持](../iot-edge/support.md)中记录）。 我们之所以推荐此选项，是因为它提供更好的客户体验。 后面将会展示满足此条件的模块。 使用此平台选项的模块必须：

- 提供一个最新标记和一个版本标记（例如 1.0.1），这些标记是使用 [GitHub Manifest-tool](https://github.com/estesp/manifest-tool) 生成的清单标记。

- 使用[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的“产品/服务列表”选项卡，在“有用链接”部分下添加指向 [Azure IoT Edge 认证设备目录](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible)的链接。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的一部分第 1 层平台

你的模块必须支持 IoT Edge 所支持的一部分（至少一个）第 1 层平台（已在 [Azure IoT Edge 支持](../iot-edge/support.md)中记录）。 使用此平台选项的模块必须：

- 如果支持多个平台，请提供一个最新标记和一个版本标记（例如 1.0.1），这些标记是使用 GitHub [manifest-tool](https://github.com/estesp/manifest-tool) 生成的清单标记。 仅当只支持一个平台时，清单标记才是可选的。
- 使用 [合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的“产品/服务列表”选项卡，在 **有用链接** 部分下添加指向 [Azure IoT Edge 认证设备目录](https://devicecatalog.azure.com/)中的至少一个 IoT Edge 设备的链接。

:::image type="content" source="media/iot-edge/iot-edge-module-technical-assets-offer-listing.png" alt-text="这是合作伙伴中心内“产品/服务列表”部分的图像":::

### <a name="device-dimensions"></a>设备规格

目标 IoT Edge 设备上的 IoT Edge 模块规格（如 CPU、RAM、存储和 GPU）必须满足以下要求：

- 该模块必须适用于 [Azure IoT Edge 认证设备目录](https://devicecatalog.azure.com/)中的至少一个 IoT Edge 认证设备。

- 必须在产品/服务（在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的“产品/服务列表”选项卡下）说明中的最后一个段落阐述最低硬件要求。 （可选）如果模块有明显的差异，则还可以列出建议的硬件要求。 例如，在套餐说明的末尾添加以下部分：

复制此 HTML 文本或在编辑窗口中使用相应的格式文本函数。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>配置

你的模块必须包括默认配置设置，尽量使部署到 IoT Edge 设备的过程变得简单直接。 此信息可在[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)的计划的“技术配置”页中提供。 容器还可以包含 IoT Edge 模块 SDK，以便与边缘中心和 IoT 中心通信。

#### <a name="default-configuration"></a>默认配置

IoT Edge 模块必须能够以[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)的计划的“技术配置”页中提供的默认设置开始。 可使用以下默认设置：

- 默认 **路由**
- 默认模块孪生所需属性
- 默认 **环境变量**
- 默认容器创建选项

如果默认值所需的参数（例如，客户服务器的 IP 地址）不起作用，请添加一个参数作为默认值。 此值采用大写并括在方括号中。 此示例设置以下默认环境变量：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>配置文档

必须明确阐述 IoT Edge 模块的所有配置设置。 例如，必须记录如何使用其路由、孪生所需属性、环境变量、createOptions 等。 必须提供文档的链接或使其成为产品/服务或计划说明的一部分。 可以在[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)的“产品/服务列表”和“计划列表”页中提供此信息。

#### <a name="tags-and-versioning"></a>标记和版本控制

客户必须能够轻松地部署模块和从市场自动获取更新（在开发人员方案中）。 他们还必须能够使用和冻结已测试的确切版本（在生产方案中）。

为了达到这些客户的期望并在市场中发布，IoT Edge 模块必须满足以下要求

- 包含一个指向所有受支持平台上最新版本的清单最新标记。
- 进行版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- 包含一个指向所有受支持平台上特定版本的“版本”标记，例如 1.0.1。
- 请勿更新“版本”标记，例如 1.0.1，因为不得更改它们。

> [!NOTE]
> （可选）版本控制可以包含“滚动更新版本”标记，例如 2.0 和 1.0。 这样就可以支持同时维护多个主要版本。

### <a name="telemetry"></a>遥测

出于遥测目的，使用 IoT 模块 SDK 的模块必须将唯一模块标识符设置为 PublisherId.OfferId.SkuId。 唯一标识符有助于 Azure 市场识别正在运行的模块实例数。

在 IoT 模块 SDK 中使用以下方法之一将 ProductInfo 设置为此标识符：

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

对于不使用 IoT 模块 SDK 的模块，将通过合作伙伴中心提供不太精确的深入信息，例如下载次数。

### <a name="security"></a>安全

IoT Edge 模块必须避免[具有特权的模块](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 而是尽量请求提供对主机的最低特权访问权限。

### <a name="module-iot-sdk"></a>模块 IoT SDK

认证过程并不要求包含 IoT 模块 SDK。 但是，包含 IoT 模块 SDK 可以提供更好的用户体验。 例如，可支持将消息路由或发送到云中。

需要使用 IoT 模块 SDK 来获取有关正在运行的模块实例数的遥测数据。

## <a name="recertification-process"></a>重新认证过程

每当发生会影响模块的重大更改时，合作伙伴会收到通知，例如：

- IoT Edge 支持的第 1 层 OS/体系结构支持矩阵
- IoT 模块 SDK
- IoT Edge 运行时
- IoT Edge 模块认证准则

合作伙伴必须通过在[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)重新发布其产品/服务来更新和再次验证其产品/服务。

如果更新产品/服务（如添加新的图像标记），也会再次对其进行验证。

## <a name="host-module-in-azure-container-registry"></a>Azure 容器注册表中的主机模块

若要将 IoT Edge 模块上传到 Azure 市场，首先需要将其托管在 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) (ACR) 中。 该模块必须包含要发布的所有标记，包括清单标记引用的映像标记。 有关详细信息，请参阅教程[创建 Azure 容器注册表和推送容器映像](../container-instances/container-instances-tutorial-prepare-acr.md)。

## <a name="next-steps"></a>后续步骤

- [创建 IoT Edge 模块产品/服务](iot-edge-offer-setup.md)
