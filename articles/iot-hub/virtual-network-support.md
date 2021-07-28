---
title: Azure IoT 中心的虚拟网络支持
description: 如何将虚拟网络连接模式与 IoT 中心配合使用
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: 726e482f64f7d9c1513f5ce362c232e225b9ee27
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109712849"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT 中心支持具有专用链接和托管标识的虚拟网络

默认情况下，IoT 中心的主机名会映射到一个公共终结点，该终结点具有可通过 internet 以公开方式路由的 IP 地址。 不同的客户将共享此 IoT 中心公共终结点，广域网和本地网络中的 IoT 设备均可对其进行访问。

![IoT 中心公共终结点](./media/virtual-network-support/public-endpoint.png)

IoT 中心的功能，包括[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)，还需要配置有从 IoT 中心到客户拥有的 Azure 资源的连接（通过其公共终结点）。 这些连接路径上的流量共同构成了从 IoT 中心到客户资源的出口流量。

你可能会想通过自己拥有和运行的某台 VNet 来限制与你的 Azure 资源（包括 IoT 中心）之间的连接。 其原因包括：

* 通过阻止与公共 internet 的连接，为 IoT 中心引入网络隔离。

* 提供来自本地网络资产的专用连接体验，确保数据和流量直接传输到 Azure 主干网络。

* 阻止来自敏感的本地网络的渗透攻击。 

* 采用 Azure 范围内常见的[专用终结点](../private-link/private-endpoint-overview.md)连接模式。

本文介绍如何通过在与 IoT 中心的入口连接中使用 [Azure 专用链接](../private-link/private-link-overview.md)以及在从 IoT 中心到其他 Azure 资源之间的出口连接中使用受信任的 Microsoft 服务异常来实现上述目标。

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>使用 Azure 专用链接实现到 IoT 中心的出口连接

专用终结点是在客户拥有的 VNet 内部分配的专用 IP 地址，可通过该地址访问 Azure 资源。 通过 Azure 专用链接，可以为 IoT 中心设置专用终结点，以允许 VNet 中的服务访问 IoT 中心，而无需将流量发送到 IoT 中心的公共终结点。 同样，本地设备可以使用[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 对等互连，通过其专用终结点，连接到 VNet 和 IoT 中心。 这样，便可以通过使用 [IoT 中心 IP 筛选器](./iot-hub-ip-filtering.md)或[公共网络访问切换](iot-hub-public-network-access.md)，来限制或完全阻止与 IoT 中心公共终结点的连接。 此方法使用设备的专用终结点来保持与中心的连接。 此设置主要适用于本地网络中的设备。 对于广域网中部署的设备，不建议使用此设置。

![IoT 中心虚拟网络出口](./media/virtual-network-support/virtual-network-ingress.png)

在继续操作之前，请确保满足以下先决条件：

* 已[创建了一个 Azure VNet](../virtual-network/quick-create-portal.md)，其中配置了要创建专用终结点的子网。

* 对于在本地网络中运行的设备，在 Azure VNet 中设置[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 专用对等互连。

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>设置一个用于 IoT 中心入口的专用终结点

专用终结点适用于 IoT 中心设备 API（例如设备到云的消息）和服务 API（如创建和更新设备）。

1. 在 Azure 门户中，依次选择“网络”、“专用终结点连接”，然后单击 “+专用终结点”  。

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="屏幕截图显示了为 IoT 中心添加专用终结点的位置":::

1. 提供订阅、资源组、名称和要在其中创建新专用终结点的区域。 理想情况下，创建专用终结点的区域应与中心所在区域相同。

1. 单击“下一步:资源”，提供用于 IoT 中心资源的订阅，选择“Microsoft.Devices/IotHubs”作为资源类型，对于“资源”，选择你的 IoT 中心名称，然后选择“iotHub”作为目标子资源  。

1. 单击“下一步:配置”，提供要在其中创建专用终结点的虚拟网络和子网。 如果需要，可选择“与 Azure 专用 DNS 区域集成”选项。

1. 单击“下一步:标记”，（可选）为资源提供标记。

1. 单击“查看 + 创建”以创建专用链接资源。

### <a name="built-in-event-hub-compatible-endpoint"></a>内置的与事件中心兼容的终结点 

[内置的与事件中心兼容的终结点](iot-hub-devguide-messages-read-builtin.md)也可以通过专用终结点进行访问。 配置专用链接后，你应该会看到内置终结点的其他专用终结点连接。 此连接的 FQDN 中包含 `servicebus.windows.net`。

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="显示提供每个 IoT 中心专用链接的两个专用终结点的图像":::

IoT 中心的 [IP 筛选器](iot-hub-ip-filtering.md)可以视情况控制对内置终结点的公共访问。 

若要完全阻止公共网络访问 IoT 中心，请[关闭公共网络访问](iot-hub-public-network-access.md)或使用 IP 筛选器以阻止所有 IP，并选择将规则应用于内置终结点的选项。

### <a name="pricing-for-private-link"></a>专用链接定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的出口连接

IoT 中心可以通过（后述资源的）公共终结点连接到 Azure blob 存储、事件中心、服务总线资源，以进行[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)操作。 默认情况下，将资源绑定到 VNet 会阻止与资源的连接。 因此，此配置会阻止 IoT 中心将数据发送给资源。 若要解决此问题，可通过“受信任的 Microsoft 服务”选项，启用从 IoT 中心资源到存储帐户、事件中心或服务总线资源的连接。 

若要允许其他服务能够查找到 IoT 中心（作为受信任的 Microsoft 服务），该中心必须使用托管标识。 预配托管标识后，需要向中心的托管标识授予 Azure RBAC 权限，以便其访问自定义终结点。 按照 [IoT 中心的托管标识支持](./iot-hub-managed-identity.md)一文，使用 Azure RBAC 权限预配托管标识，并将自定义终结点添加到 IoT 中心。 请确保启用受信任的 Microsoft 第一方例外，以允许 Azure IoT 中心访问自定义终结点（如果已准备好防火墙配置）。

### <a name="pricing-for-trusted-microsoft-service-option"></a>受信任的 Microsoft 服务选项的定价
受信任的 Microsoft 第一方服务异常功能是免费的。 配置存储帐户、事件中心或服务总线资源是单独收费的。
## <a name="next-steps"></a>后续步骤

使用以下链接详细了解 IoT 中心功能：

* [消息路由](./iot-hub-devguide-messages-d2c.md)
* [文件上传](./iot-hub-devguide-file-upload.md)
* [批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)
