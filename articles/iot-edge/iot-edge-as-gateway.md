---
title: 用于下游设备的网关 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 创建一个透明、不透明或代理网关设备，以将数据从多个下游设备发送到云或在本地对其进行处理。
author: kgremban
ms.author: kgremban
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 85d93c46ca9ef397b9e2ae95cfc042e50ee10bd1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742256"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何将 IoT Edge 设备用作网关

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 设备可以作为网关运行，提供网络上其他设备和 IoT 中心之间的连接。

IoT Edge 中心模块的作用类似于 IoT 中心，因此可以处理来自具有相同 IoT 中心标识的其他设备的连接。 这种类型的网关模式称为“透明”，因为消息可以从下游设备传递到 IoT 中心，就像它们之间没有网关一样。

对于不能或无法自行连接到 IoT 中心的设备，IoT Edge 网关可以提供该连接。 这种类型的网关模式称为“转换”，因为 IoT Edge 设备必须对传入的下游设备消息执行处理，然后才能将这些消息转发到 IoT 中心。 这些场景需要 IoT Edge 网关上的其他模块来处理处理步骤。

透明和转换网关模式并不相互排斥。 单个 IoT Edge 设备既可以充当透明网关，也可以充当转换网关。

所有网关模式提供以下优势：

* **边缘分析** – 在本地使用 AI 服务处理来自下游设备的数据，而无需向云发送完全保真的遥测数据。 本地查找和响应见解，并仅将一部分数据发送到 IoT 中心。
* 下游设备隔离  – 网关设备可以屏蔽所有下游设备，而不对 Internet 公开。 它可以位于无连接的运营技术 (OT) 网络和提供 Web 访问权限的信息技术 (IT) 网络之间。 同样，无法自行连接到 IoT 中心的设备可以改为连接到网关设备。
* **连接多路复用** - 通过 IoT Edge 网关连接到 IoT 中心的所有设备可以使用同一个基础连接。 这种多路复用功能要求 IoT Edge 网关使用 AMQP 作为其上游协议。
* 流量平滑  - 在本地保存消息的同时，如果 IoT 中心对流量进行限制，IoT Edge 设备将自动执行指数回退。 此优点使解决方案能灵活应对流量高峰。
* **脱机支持** - 网关设备存储不能传递到 IoT 中心的消息和孪生更新。

## <a name="transparent-gateways"></a>透明网关

在透明网关模式下，在理论上可以连接到 IoT 中心的设备可以改为连接到网关设备。 下游设备有其自己的 IoT 中心标识，并使用 MQTT 或 AMQP 协议进行连接。 网关只是在设备与 IoT 中心之间传递通信。 设备和通过 IoT 中心与其交互的用户都不知道网关正在协调它们的通信。 这样缺乏感知意味着网关被认为是“透明”的。

要详细了解 IoT Edge 中心如何管理下游设备与云之间的通信，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"
![关系图 - 透明网关模式](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

>[!NOTE]
>在 IoT Edge 版本 1.1 和更低版本中，IoT Edge 设备不能是 IoT Edge 网关的下游。
>
>从 IoT Edge 版本 1.2 开始，透明网关可处理来自下游 IoT Edge 设备的连接。 有关详细信息，请切换到本文的 [IoT Edge 1.2](?view=iotedge-2020-11&preserve-view=true) 版本。

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

从 IoT Edge 版本 1.2 开始，透明网关可处理来自下游 IoT Edge 设备的连接。

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>父子关系

通过将 IoT Edge 网关设置为与之连接的下游设备子级的父级，可以在 IoT 中心中声明透明的网关关系 。

可在网关配置中的以下三个点建立父/子关系：

#### <a name="cloud-identities"></a>云标识

透明网关方案中的所有设备都需要云标识，以便能够在 IoT 中心进行身份验证。 创建或更新设备标识时，可以设置设备的父设备或子设备。 此配置授权父网关设备处理其子设备的身份验证。

>[!NOTE]
>对于使用对称密钥身份验证的下游设备来说，在 IoT 中心中设置父设备曾是一个可选步骤。 但从版本 1.1.0 开始，每个下游设备都必须分配给父设备。
>
>可以通过将环境变量 AuthenticationMode 设置为值 CloudAndScope 来将 IoT Edge 中心配置为返回到以前的行为 。

子设备只能有一个父级。 每个父级最多可以有 100 个子级。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 设备在透明网关关系中可以是父级，也可以是子级。 可以创建相互报告的多个 IoT Edge 设备的层次结构。 网关层次结构的顶端节点最多可以有五代子节点。 例如，一个 IoT Edge 设备在其下可能有五层链接为子级的 IoT Edge 设备。 但是第五代的 IoT Edge 设备不能有任何子级、IoT Edge 或其他。
::: moniker-end

#### <a name="gateway-discovery"></a>网关发现

子设备需要能够在本地网络上找到其父设备。 使用主机名（完全限定的域名 (FQDN) 或 IP 地址）配置网关设备，其子设备将使用该主机名来定位它。

在下游 IoT 设备上，使用连接字符串中的 gatewayHostname 参数指向父设备。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
在下游 IoT Edge 设备上，使用配置文件中的 parent_hostname 参数指向父设备。
::: moniker-end

#### <a name="secure-connection"></a>安全连接

父设备和子设备还需要对彼此的连接进行身份验证。 每个设备都需要一个共享根 CA 证书的副本，子设备使用该副本来验证它们是否连接到正确的网关。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
当多个 IoT Edge 网关在网关层次结构中相互连接时，层次结构中的所有设备应使用单个证书链。
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>透明网关后面的设备功能

与 IoT Edge 的消息传递管道一起使用的所有 IoT 中心基元也支持透明网关方案。 每个 IoT Edge 网关都具备存储和转发通过它传入的消息的能力。

使用下表查看设备与网关后的设备对不同 IoT 中心功能的支持情况。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| 功能 | IoT 设备 | 网关后的 IoT |
| ---------- | ---------- | -------------------- |
| [设备到云 (D2C) 的消息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是 - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [云到设备 (C2D) 的消息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是 - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是 - IoT 子级 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是 - IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)和[模块孪生](../iot-hub/iot-hub-devguide-module-twins.md) | ![是 - IoT 孪生体](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT 孪生体](./media/iot-edge-as-gateway/check-yes.png) |
| [文件上传](../iot-hub/iot-hub-devguide-file-upload.md) | ![是 - IoT 文件上传](./media/iot-edge-as-gateway/check-yes.png) | ![否 - IoT 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 功能 | IoT 设备 | 网关后的 IoT | IoT Edge 设备 | 网关后的 IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [设备到云 (D2C) 的消息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是 - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是 - IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [云到设备 (C2D) 的消息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是 - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是 - IoT 子级 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![否 - IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![否 - IoT Edge 子级 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是 - IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是 - IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)和[模块孪生](../iot-hub/iot-hub-devguide-module-twins.md) | ![是 - IoT 孪生体](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT 孪生体](./media/iot-edge-as-gateway/check-yes.png) | ![是 - IoT Edge 孪生体](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT Edge 孪生体](./media/iot-edge-as-gateway/check-yes.png) |
| [文件上传](../iot-hub/iot-hub-devguide-file-upload.md) | ![是 - IoT 文件上传](./media/iot-edge-as-gateway/check-yes.png) | ![否 - IoT 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) | ![否 - IoT Edge 文件上传](./media/iot-edge-as-gateway/crossout-no.png) | ![否 - IoT Edge 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) |
| 容器映像拉取 |   |   | ![是 - IoT Edge 容器拉取](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT Edge 容器拉取](./media/iot-edge-as-gateway/check-yes.png) |
| Blob 上传 |   |   | ![是 - IoT Edge Blob 上传](./media/iot-edge-as-gateway/check-yes.png) | ![是 - 子级 IoT Edge Blob 上传](./media/iot-edge-as-gateway/check-yes.png) |

可以将容器映像从父设备下载、存储和传递到子设备。

可以将 Blob（包括支持包和日志）从子设备上传到父设备。

::: moniker-end

## <a name="translation-gateways"></a>转换网关

如果下游设备无法连接到 IoT 中心，则 IoT Edge 网关需要充当转换器。 通常，不支持 MQTT、AMQP 或 HTTP 的设备需要此模式。 由于这些设备无法连接到 IoT 中心，因此，如果不进行一些预处理，它们也将无法连接到 IoT Edge 中心模块。

对于通常特定于下游设备的硬件或协议的自定义或第三方模块，需要将它们部署到 IoT Edge 网关。 这些转换模块将传入的消息转换为 IoT 中心可以理解的格式。

转换网关有两种模式：协议转换和标识转换 。

![关系图 - 转换网关模式](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>协议转换

在协议转换网关模式下，只有 IoT Edge 网关具有 IoT 中心的标识。 转换模块从下游设备接收消息，将其转换为受支持的协议，然后 IoT Edge 设备代表下游设备发送这些消息。 所有信息好像都来自一台设备，即网关。 如果云应用程序想要以设备位单位分析数据，则下游设备就必须在其消息中嵌入额外的标识信息。 此外，IoT 中心基元（例如孪生和直接方法）仅受网关设备支持，而不受下游设备支持。 与透明网关相比，这种模式中的网关被视为不透明，因为它们掩盖了下游设备的标识。

协议转换支持资源受限的设备。 许多现有设备将生成能够为企业提供见解的数据；然而，它们的设计并未考虑云连接。 不透明的网关允许解锁这些数据，并在 IoT 解决方案中使用这些数据。

### <a name="identity-translation"></a>标识转换

标识转换网关模式基于协议转换，但是 IoT Edge 网关还代表下游设备提供 IoT 中心设备标识。 转换模块负责理解下游设备使用的协议，为其提供标识，并将其消息转换为 IoT 中心基元。 下游设备作为一流设备出现在 IoT 中心，随附克隆和方法。 用户可以与 IoT 中心中的设备进行交互，而同时不了解中间网关设备。

标识转换具备协议转换的优势，并且还允许从云完全管理下游设备。 IoT 解决方案中的所有设备都显示在 IoT 中心内，不管它们使用的是什么协议。

### <a name="device-capabilities-behind-translation-gateways"></a>转换网关后面的设备功能

下表说明了如何在两种转换网关模式下将 IoT 中心功能扩展到下游设备。

| 功能 | 协议转换 | 标识转换 |
| ---------- | -------------------- | -------------------- |
| 存储在 IoT 中心标识注册表中的标识 | 仅网关设备的标识 | 所有已连接的设备的标识 |
| 设备孪生 | 仅网关具有设备和模块孪生 | 每个已连接的设备均有自己的设备孪生 |
| 直接方法和云到设备的消息 | 云只能对网关设备寻址 | 云可以对每个已连接的设备单独寻址 |
| [IoT 中心限制和配额](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 适用于网关设备 | 适用于每个设备 |

使用协议转换模式时，通过该网关连接的所有设备共享同一个可包含最多 50 条消息的云到设备的队列。 仅当很少设备通过各字段网关进行连接以及云到设备的流量较低时，才使用该模式。

IoT Edge 运行时不包含协议或标识转换功能。 这些模式需要自定义模块或第三方模块，这些模块通常特定于所使用的硬件和协议。 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)包含多个可供选择的协议转换模块。 有关使用标识转换模式的示例，请参阅 [Azure IoT Edge LoRaWAN 初学者工具包](https://github.com/Azure/iotedge-lorawan-starterkit)。

## <a name="next-steps"></a>后续步骤

了解设置透明网关的三个步骤：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
