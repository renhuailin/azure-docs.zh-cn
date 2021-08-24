---
title: 通过 5G 或 LTE 网络连接 Azure Percept
description: 本文介绍如何通过 5G 或 LTE 网络连接 Azure Percept DK。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 7388a50951137b45821bc80f2290900a00550395
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071533"
---
# <a name="connect-the-azure-percept-dk-over-5g-or-lte-networks"></a>通过 5G 或 LTE 网络连接 Azure Percept DK

通过 5G 或 LTE 网络连接 Edge AI 设备的优点有很多。 在 Wi-Fi 和 LAN 连接受限的场景中，边缘 AI 可以发挥最大作用，如智能城市、自动驾驶汽车和农业。 此外，5G/LTE 网络提供比 Wi-Fi 更高的安全性。 最后，使用在 Edge 上运行 AI 的 IoT 设备可以优化 5G/LTE 网络的带宽。 其中只有必要的信息被发送到云，而大部分的数据是在设备上处理的。 目前，Azure Percept DK 并不能直接连接到 5G/LTE 网络， 但可以使用内置的以太网和 Wi-Fi 功能连接到 5G/LTE 网关。 本文介绍了它的工作原理。

## <a name="options-for-connecting-the-azure-percept-dk-over-5g-or-lte-networks"></a>用于通过 5G 或 LTE 网络连接 Azure Percept DK 的选项
你可以使用其他硬件通过 5G/LTE 连接来连接 Azure Percept DK。 目前支持两个主要选项：
- 5G/LTE Wi-Fi 热点设备 - 开发工具包从此设备连接到 Wi-Fi 热点提供的 Wi-Fi 网络。 在这种情况下，开发工具包像任何其他 Wi-Fi 网络一样连接到网络。 请参阅 [Azure Percept DK 设置指南](./quickstart-percept-dk-set-up.md)以获取更多说明，并选择通过热点传播的 5G/LTE Wi-Fi 网络。
- 5G/LTE 以太网网关设备 - 开发工具包从此设备通过以太网连接到 5G/LTE 网关，与 Wi-Fi 连接相比，它具有更高的安全性。 本文的其余部分将更详细地介绍如何配置这样的网络。

## <a name="5glte-gateway-topology"></a>5G/LTE 网关拓扑
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="此示意图展示了 Azure Percept DK 如何通过以太网连接到 5G/LTE 网关。":::

在上面的示意图中，可以看到 5G/LTE 网关是如何轻松地与 Azure Percepept DK 配对的。

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>连接到 5G 或 LTE 网关时的注意事项
以下是将 Azure Percept DK 连接到 5G/LTE 网关时要考虑的一些要点。
- 首先设置网关，然后验证它是否通过 SIM 接收连接。 这样，就可以更轻松地排查在连接 Azure Percept DK 时发现的任何问题。
- 确保以太网线缆的两端与网关和 Azure Percept DK 牢固连接。
- 按照[默认说明](./how-to-connect-over-ethernet.md)通过以太网连接 Azure Percept DK。
- 如果 5G/LTE 计划具有配额，则建议优化 Azure Percept DK 模型发送到云的数据量。
- 确保已[正确配置防火墙](./concept-security-configuration.md)，阻止来自外部的入站流量。

## <a name="ssh-over-a-5g-or-lte-network"></a>通过 5G 或 LTE 网络使用 SSH 连接
若要通过 5G/LTE 以太网网关使用 SSH 连接到开发工具包，可以使用以下选项：
- 使用开发工具包的 Wi-Fi 访问点。 如果已禁用 Wi-Fi，可以通过重新启动开发工具包来重新启用它。 你可以从那里连接到开发工具包的 Wi-Fi 访问点，并按照[这些 SSH 过程](./how-to-ssh-into-percept-dk.md)操作。
- 使用以太网连接到本地网络 (LAN)。 利用此选项，可以将开发工具包从 5G/LTE 网关拔出并将其插入到 LAN 路由器。 有关详细信息，请参阅[如何通过以太网进行连接](./how-to-connect-over-ethernet.md)。 
- 使用网关的远程访问功能。 许多 5G/LTE 网关包含远程访问管理器，可用于通过 SSH 连接到网络上的设备。 请咨询你的 5G/LTE 网关制造商，看看它是否具有此功能。 下面是 [Cradlepoint 5G/LTE 网关](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)远程访问管理器的示例。
- 使用开发工具包的串行端口。 Azure Percept DK 包含一个串行连接端口，可用于直接连接到设备。 有关详细说明，请参阅[通过串行端口连接 Azure Percept DK](./how-to-connect-to-percept-dk-over-serial.md)。

## <a name="considerations-when-selecting-a-5g-or-lte-gateway-device"></a>选择 5G 或 LTE 网关设备时的注意事项
5G/LTE 网关支持影响下载和上传的最大数据速率的不同技术。 公布的数据速率为决策提供了指导，但通常从未达到。 下面是根据你的需求选择合适网关的一些指导。
 
- LTE CAT-1 提供高达 10 Mbps 的下行速率和 5 Mbps 的上行速率。 这对于默认的 Azure Percept Devkit 功能（如对象检测和创建语音助理）来说已经足够。 不过，对于需要将视频流数据上传到云的解决方案来说，这可能不够。
- LTE CAT 3 和 4 提供高达 100 Mbps 的下行速率和 50 Mbps 的上行速率，足以将视频流式传输到云。 不过，它不足以流式传输完整的高清视频。
- LTE CAT 5 及更高版本提供足够高的数据速率，可以为单个设备流式传输高清视频。 如果需要将多个设备连接到单个网关，则需要考虑使用 5G。
- 5G 网关将使你的方案在未来得到最佳定位。 它们的数据速率和带宽可同时支持多个设备的高数据吞吐量。 此外，还可以为数据传输提供更低的延迟。


## <a name="next-steps"></a>后续步骤
如果你有 5G/LTE 网关，想要将 Azure Percept DK 连接到该网关，请执行以下后续步骤。
- [如何通过以太网连接 Azure Percept DK](./how-to-connect-over-ethernet.md)
