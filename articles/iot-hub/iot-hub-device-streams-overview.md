---
title: Azure IoT 中心设备流 | Microsoft Docs
description: Azure IoT 中心设备流概览，可促进各种云到设备通信方案的安全双向 TCP 隧道。
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 37dfd5adcfa00377980933e67c8881aaef6d8dd8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746098"
---
# <a name="iot-hub-device-streams-preview"></a>IoT 中心设备流（预览版）

Azure IoT 中心 *设备流* 可促进为各种云到设备通信方案创建安全的双向 TCP 隧道。 设备流由 IoT 中心 *流式处理终结点* 进行调解，该终结点充当设备和服务终结点之间的代理。 此设置如下图所示，当设备位于网络防火墙之后或驻留在专用网络内部时特别有用。 因此，IoT 中心设备流有助于满足客户以防火墙友好方式访问 IoT 设备的需求，而无需广泛开放传入或传出网络防火墙端口。

![“IoT 中心设备流概述”](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

使用 IoT 中心设备流可让设备保持安全状态，同时只需打开端口 443 上到 IoT 中心流式处理终结点的出站 TCP 连接。 建立设备流后，服务端和设备端应用程序将各自具有针对 WebSocket 客户端对象的编程访问权限，以便相互发送和接收原始字节。 此隧道提供的可靠性和顺序保证与 TCP 提供的可靠性和顺序保证相当。

## <a name="benefits"></a>好处

IoT 中心设备流具有以下优点：

* 防火墙友好的安全连接：可以从服务终结点访问 IoT 设备，而无需在设备或网络外围打开入站防火墙端口（仅需要端口 443 上到 IoT 中心的出站连接）。

* 身份验证：隧道的设备端和服务端都需要使用相应的凭据对 IoT 中心进行身份验证。

* 加密：默认情况下，IoT 中心设备流使用启用了 TLS 的连接。 这可确保始终加密流量，而无论应用程序是否使用加密。

* 连接的简洁性：在许多情况下，使用设备流可消除对虚拟专用网络进行复杂设置以连接到 IoT 设备的需求。

* 与 TCP/IP 堆栈的兼容性：IoT 中心设备流可以接纳 TCP/IP 应用程序流量。 这意味着各种专有协议和标准协议均可利用此功能。

* 专用网络设置的易用性：服务可以通过引用设备 ID（而不是设备的 IP 地址）与设备通信。 在设备位于专用网络内并拥有专用 IP 地址，或其 IP 地址由系统动态分配且对于服务端未知的情况下，这很有用。

## <a name="device-stream-workflows"></a>设备流工作流

当服务请求通过提供设备 ID 连接到设备时，设备流会启动。 此工作流特别适合客户端/服务器通信模型，其中包括 SSH 和 RDP，即用户打算使用 SSH 或 RDP 客户端程序远程连接到设备上运行的 SSH 或 RDP 服务器。

设备流创建过程涉及设备、服务、IoT 中心的主要终结点和流式处理终结点之间的协商。 IoT 中心的主要终结点协调设备流的创建，流式处理终结点则处理在服务和设备之间流动的流量。

### <a name="device-stream-creation-flow"></a>设备流创建流程

使用 SDK 以编程方式创建设备流涉及以下步骤，如下图所示：

![“设备流握手过程”](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. 设备应用程序会预先注册回叫，以便在向设备发起新设备流时收到通知。 此步骤通常在设备启动并连接到 IoT 中心时发生。

2. 服务端程序在需要时通过提供设备 ID（_而不是_ IP 地址）来发起设备流。

3. IoT 中心通过调用步骤 1 中注册的回叫来通知设备端程序。 设备可以接受或拒绝流启动请求。 此逻辑可以特定于应用程序方案。 如果设备拒绝了流请求，则 IoT 中心会相应地通知服务；否则，请执行以下步骤。

4. 设备会在端口 443 上创建到流式处理终结点的安全出站 TCP 连接，并将连接升级为 WebSocket。 流式处理终结点的 URL 以及用于进行身份验证的凭据都由 IoT 中心在步骤 3 发送的请求中向设备提供。

5. 服务获得设备接受流这一结果的通知，并继续创建自己的到流式处理终结点的 WebSocket 客户端。 同样，它从 IoT 中心接收流式处理终结点 URL 和身份验证信息。

在上述握手过程中：

* 握手过程必须在 60 秒内完成（步骤 2 到 5），否则握手会因超时而失败且服务将相应获得通知。

* 上述流创建流程完成后，流式处理终结点将充当代理，并通过其各自的 WebSocket 在服务和设备之间传输流量。

* 设备和服务都需要端口 443 上的到 IoT 中心主要终结点和流式处理终结点的出站连接。 IoT 中心门户的“概览”选项卡上提供这些终结点的 URL。

* 已建立的流的可靠性和排序保证与 TCP 的可靠性和排序保证相当。

* 到 IoT 中心和流式处理终结点的所有连接都使用 TLS 并加密。

### <a name="termination-flow"></a>终止流

当到网关的任一 TCP 连接（由服务或设备）断开时，建立的流会终止。 此情况可通过关闭设备或服务程序上的 WebSocket 来自愿发生，也可能在出现网络连接超时或进程失败的情况下非自愿发生。 当设备或服务与流式处理终结点的连接终止时，另一个 TCP 连接也将被（强制）终止，服务和设备负责重新创建流（如果需要）。

## <a name="connectivity-requirements"></a>连接要求

设备流的设备端和服务端都必须能够建立到 IoT 中心及其流式处理终结点的启用 TLS 的连接。 这需要在端口 443 上建立到这些终结点的出站连接。 可以在 IoT 中心的“概述”选项卡上找到与这些终结点关联的主机名，如下图所示：

![“设备流终结点”](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

或者，可以使用中心“属性”部分下的 Azure CLI 检索终结点信息，具体而言是 `property.hostname` 和 `property.deviceStreams` 键。

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

输出是中心的设备和服务可能需要连接的所有终结点的 JSON 对象，以便建立设备流。

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> 确保已安装 Azure CLI 2.0.57 或更高版本。 可从[安装 Azure CLI](/cli/azure/install-azure-cli) 页下载最新版本。
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>允许以出站方式连接到设备流式处理终结点

如本文开头所述，设备在设备流启动过程中创建到 IoT 中心流式处理终结点的出站连接。 设备或其网络上的防火墙必须允许端口 443 上到流式传输网关的出站连接（请注意，通信发生在使用 TLS 加密的 WebSocket 连接上）。

可以在 Azure IoT 中心门户的“概述”选项卡下找到设备流式处理终结点的主机名。!["设备流终结点"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

或者，可以使用 Azure CLI 找到此信息：

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> 确保已安装 Azure CLI 2.0.57 或更高版本。 可从[安装 Azure CLI](/cli/azure/install-azure-cli) 页下载最新版本。
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>通过设备流资源日志排除故障

可以设置 Azure Monitor 来收集 IoT 中心发出的[设备流的资源日志](monitor-iot-hub-reference.md#device-streams-preview)。 这在故障排除方案中非常有用。

按照以下步骤创建诊断设置，以将 IoT 中心的设备流日志发送到 Azure Monitor 日志：

1. 在 Azure 门户中，导航到 IoT 中心。 在左窗格中的“监视”下，选择“诊断设置”。  然后选择“添加诊断设置”。

2. 提供诊断设置的名称，并从日志列表中选择“DeviceStreams”。 选择“发送到 Log Analytics”。 系统将引导你选择现有的 Log Analytics 工作区或创建新的资源。

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="启用设备流日志":::

3. 创建诊断设置以将设备流日志发送到 Log Analytics 工作区后，可以通过在 Azure 门户的 IoT 中心的左窗格中的“监视”下选择“日志”来访问日志。  设备流日志将在 `AzureDiagnostics` 表中显示并具有 `Category=DeviceStreams`。 请注意，可能需要几分钟时间后，日志才会出现在表中。

   如下所示，日志中还提供目标设备的标识和操作的结果。

   ![“访问设备流日志”](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

要了解有关将 Azure Monitor 与 IoT 中心一起使用的更多信息，请参阅 [Monitor IoT 中心](monitor-iot-hub.md)。 有关可用于 IoT 中心的所有资源日志、指标和表的信息，请参阅[监视 Azure IoT 中心数据参考](monitor-iot-hub-reference.md)。

## <a name="regional-availability"></a>区域可用性

在公开预览版发布期间，IoT 中心设备流在“美国中部”、“美国中部 EUAP”、“北欧”和“东南亚”区域提供。 请确保在这其中的一个区域创建中心。

## <a name="sdk-availability"></a>SDK 可用性

每个流的两端（设备端和服务端）使用 IoT 中心 SDK 建立隧道。 在公开预览版中，客户可以选择以下 SDK 语言：

* C 和 C# SDK 支持设备端上的设备流。

* NodeJS 和 C# SDK 支持服务端上的设备流。

## <a name="next-steps"></a>后续步骤

使用以下链接详细了解设备流。

> [!div class="nextstepaction"]
> [IoT 演示中的设备流（第 9 频道）](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
