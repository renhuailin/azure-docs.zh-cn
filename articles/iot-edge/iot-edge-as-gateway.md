---
title: 用于下游设备的网关 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 创建一个透明、不透明或代理网关设备，以将数据从多个下游设备发送到云或在本地对其进行处理。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536432"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何将 IoT Edge 设备用作网关

IoT Edge 设备可以作为网关运行，在网络和 IoT 中心的其他设备之间提供连接。

IoT Edge 中心模块的作用类似于 IoT 中心，因此可以处理与 IoT 中心具有标识的任何设备的连接，包括其他 IoT Edge 设备。 这种类型的网关模式称为 *透明* ，因为消息可以从下游设备传递到 IoT 中心，就好像它们之间没有网关。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
从 IoT Edge 1.2 版开始，透明网关可处理来自其他 IoT Edge 设备的下游连接。
::: moniker-end

对于不能或无法自行连接到 IoT 中心的设备，IoT Edge 网关可以提供该连接。 这种类型的网关模式称为 " *转换* "，因为 IoT Edge 设备必须先对传入的下游设备消息执行处理，然后才能将这些消息转发到 IoT 中心。 这些方案需要 IoT Edge 网关上的其他模块来处理处理步骤。

透明和平移网关模式并不相互排斥。 单个 IoT Edge 设备既可以充当透明网关，也可以充当翻译网关。

所有网关模式提供以下优势：

* **边缘分析** – 在本地使用 AI 服务处理来自下游设备的数据，而无需向云发送完全保真的遥测数据。 本地查找和响应见解，并仅将一部分数据发送到 IoT 中心。
* 下游设备隔离  – 网关设备可以屏蔽所有下游设备，而不对 Internet 公开。 它可以在一种运营技术 () 网络中，该网络没有连接，而 (它) 网络提供 web 访问。 同样，不能自行连接到 IoT 中心的设备可以改为连接到网关设备。
* **连接多路复用** - 通过 IoT Edge 网关连接到 IoT 中心的所有设备使用同一个基础连接。
* 流量平滑  - 在本地保存消息的同时，如果 IoT 中心对流量进行限制，IoT Edge 设备将自动执行指数回退。 此优点使解决方案能灵活应对流量高峰。
* **脱机支持** - 网关设备存储不能传递到 IoT 中心的消息和孪生更新。

## <a name="transparent-gateways"></a>透明网关

在透明网关模式下，理论上可以连接到 IoT 中心的设备可以改为连接到网关设备。 下游设备具有其自己的 IoT 中心标识，并使用 MQTT 或 AMQP 协议进行连接。 网关只是在设备与 IoT 中心之间传递通信。 设备和通过 IoT 中心与其交互的用户都不知道网关正在协调它们的通信。 这样缺乏感知意味着网关被认为是“透明”的。

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge 设备不能是 IoT Edge 网关的下游。

![关系图-透明网关模式](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

从版本1.2.0 开始，IoT Edge 设备可通过透明网关进行连接。

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>父关系和子关系

可以通过将 "IoT Edge 网关" 设置为连接到该节点的下游设备 *子* 网的 *父项* ，在 IoT 中心声明透明网关关系。

可在网关配置中的以下三个点建立父/子关系：

#### <a name="cloud-identities"></a>云标识

透明网关方案中的所有设备都需要云标识，以便能够在 IoT 中心进行身份验证。 创建或更新设备标识时，可以设置设备的父设备或子设备。 此配置授权父网关设备处理其子设备的身份验证。

子设备只能有一个父级。 每个父级最多可以有100个子级。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 设备既可以是父项，也可以是透明网关关系中的子项。 可以创建多个 IoT Edge 设备进行报告的层次结构。 网关层次结构的顶级节点最多可以有五个子代代。 例如，IoT Edge 设备在其下可能有五层 IoT Edge 设备链接为子节点。 但第五代中的 IoT Edge 设备不能具有任何子节点，IoT Edge 或其他设置。
::: moniker-end

#### <a name="gateway-discovery"></a>网关发现

子设备需要能够在本地网络上找到其父设备。 使用 **主机名** 、完全限定的域名 (FQDN) 或 IP 地址配置网关设备，其子设备将使用该名称来查找。

在下游 IoT 设备上，使用连接字符串中的 **gatewayHostname** 参数指向父设备。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
在下游 IoT Edge 设备上，使用 yaml 文件中的 **parent_hostname** 参数指向父设备。
::: moniker-end

#### <a name="secure-connection"></a>安全连接

父设备和子设备还需要彼此验证它们的连接。 每个设备都需要一个共享根 CA 证书的副本，子设备使用该证书来验证它们是否连接到正确的网关。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
当多个 IoT Edge 网关彼此连接到网关层次结构中的多个网关时，层次结构中的所有设备都应使用单个证书链。
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>透明网关后面的设备功能

所有与 IoT Edge 的消息传递管道一起使用的 IoT 中心基元还支持透明网关方案。 每个 IoT Edge 网关都具有用于通过它的消息的存储和转发功能。

使用下表查看与网关后的设备相比，设备支持不同 IoT 中心功能的方式。

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| 功能 | IoT 设备 | 网关后的 IoT |
| ---------- | ---------- | -------------------- |
| [设备到云 (D2C) 消息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [云到设备 (C2D) 消息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是-IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md) 和 [模块孪生](../iot-hub/iot-hub-devguide-module-twins.md) | ![是-IoT 孪生](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 孪生](./media/iot-edge-as-gateway/check-yes.png) |
| [文件上传](../iot-hub/iot-hub-devguide-file-upload.md) | ![是-IoT 文件上传](./media/iot-edge-as-gateway/check-yes.png) | ![无 IoT 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 功能 | IoT 设备 | 网关后的 IoT | IoT Edge 设备 | 网关后 IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [设备到云 (D2C) 消息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [云到设备 (C2D) 消息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge 子 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是-IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md) 和 [模块孪生](../iot-hub/iot-hub-devguide-module-twins.md) | ![是-IoT 孪生](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 孪生](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT Edge 孪生](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge 孪生](./media/iot-edge-as-gateway/check-yes.png) |
| [文件上传](../iot-hub/iot-hub-devguide-file-upload.md) | ![是-IoT 文件上传](./media/iot-edge-as-gateway/check-yes.png) | ![无 IoT 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) | ![无 IoT Edge 文件上传](./media/iot-edge-as-gateway/crossout-no.png) | ![不 IoT Edge 子文件上传](./media/iot-edge-as-gateway/crossout-no.png) |
| 容器映像提取 |   |   | ![是-IoT Edge 容器请求](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge 容器请求](./media/iot-edge-as-gateway/check-yes.png) |
| Blob 上传 |   |   | ![是-IoT Edge blob 上传](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge blob 上传](./media/iot-edge-as-gateway/check-yes.png) |

**容器映像** 可从父设备下载、存储和传送到子设备。

可以将 **blob** （包括支持捆绑和日志）从子设备上传到父设备。

::: moniker-end

## <a name="translation-gateways"></a>翻译网关

如果下游设备无法连接到 IoT 中心，则 IoT Edge 网关需要充当转换器。 通常，对于不支持 MQTT、AMQP 或 HTTP 的设备，此模式是必需的。 由于这些设备无法连接到 IoT 中心，因此，如果没有进行某些预处理，也无法连接到 IoT Edge 集线器模块。

需要将特定于下游设备硬件或协议的自定义或第三方模块部署到 IoT Edge 网关。 这些翻译模块会接收传入的消息，并将其转换为 IoT 中心可以理解的格式。

翻译网关有两种模式： *协议转换* 和 *标识转换* 。

![关系图-转换网关模式](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>协议转换

在协议转换网关模式下，只有 IoT Edge 网关具有 IoT 中心的标识。 翻译模块从下游设备接收消息，将其转换为受支持的协议，然后 IoT Edge 设备代表下游设备发送这些消息。 所有信息好像都来自一台设备，即网关。 如果云应用程序想要以设备位单位分析数据，则下游设备就必须在其消息中嵌入额外的标识信息。 此外，仅网关设备（而不是下游设备）支持 IoT 中心基元（如孪生和直接方法）。 此模式中的网关将被视为不 *透明* ，而不是透明网关，因为它们会掩盖下游设备的标识。

协议转换支持资源受限制的设备。 许多现有设备将生成能够为企业提供见解的数据；然而，它们的设计并未考虑云连接。 不透明的网关允许解锁这些数据，并在 IoT 解决方案中使用这些数据。

### <a name="identity-translation"></a>标识转换

身份转换网关模式是基于协议转换构建的，但 IoT Edge 网关还代表下游设备提供 IoT 中心设备标识。 翻译模块负责了解下游设备使用的协议，并为其提供标识并将其消息翻译为 IoT 中心基元。 下游设备作为一流设备出现在 IoT 中心，随附克隆和方法。 用户可以与 IoT 中心中的设备进行交互，而同时不了解中间网关设备。

身份转换提供协议转换的优点，还可以从云中完全管理下游设备。 IoT 解决方案中的所有设备都显示在 IoT 中心内，不管它们使用的是什么协议。

### <a name="device-capabilities-behind-translation-gateways"></a>翻译网关后的设备功能

下表说明了如何将 IoT 中心功能扩展到两种翻译网关模式下的下游设备。

| 功能 | 协议转换 | 标识转换 |
| ---------- | -------------------- | -------------------- |
| 存储在 IoT 中心标识注册表中的标识 | 仅网关设备的标识 | 所有已连接的设备的标识 |
| 设备孪生 | 仅网关具有设备和模块孪生 | 每个已连接的设备均有自己的设备孪生 |
| 直接方法和云到设备的消息 | 云只能对网关设备寻址 | 云可以对每个已连接的设备单独寻址 |
| [IoT 中心限制和配额](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 适用于网关设备 | 适用于每个设备 |

使用协议转换模式时，通过该网关连接的所有设备共享同一云到设备的队列，最多可包含50条消息。 仅当很少设备通过每个现场网关连接时，才使用此模式，并且其云到设备的流量较低。

IoT Edge 运行时不包括协议或标识转换功能。 这些模式需要自定义或第三方模块，这些模块通常特定于所使用的硬件和协议。 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)包含多个可供选择的协议转换模块。 有关使用标识转换模式的示例，请参阅 [Azure IoT Edge LoRaWAN 初学者工具包](https://github.com/Azure/iotedge-lorawan-starterkit)。

## <a name="next-steps"></a>后续步骤

了解设置透明网关的三个步骤：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
