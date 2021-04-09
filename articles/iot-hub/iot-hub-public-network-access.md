---
title: 管理 Azure IoT 中心的公用网络访问
description: 有关如何禁用和启用 IoT 中心的公用网络访问的文档
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: fbbdaeb796dfa23906c8010a54af14eff6df0b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026634"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>管理 IoT 中心的公用网络访问

若要[仅允许专用终结点访问 VNet 中的 IoT 中心](virtual-network-support.md)，请禁用公用网络访问。 为此，请使用 Azure 门户或 `publicNetworkAccess` API。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>使用 Azure 门户禁用公用网络访问

1. 访问 [Azure 门户](https://portal.azure.com)
2. 导航到 IoT 中心。
3. 从左侧菜单中选择“网络”。
4. 在“允许公用网络访问”下，选择“已禁用”
5. 选择“保存” 。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="显示在 Azure 门户中的何处禁用公用网络访问的图像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

若要启用公用网络访问，请选择“所有网络”，然后选择“保存” 。

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>禁用公共网络访问后访问 IoT 中心

禁用公共网络访问后，只能[使用 Azure 专用链接通过其 VNet 专用终结点](virtual-network-support.md)访问 IoT 中心。 此限制包括通过 Azure 门户进行访问，因为对 IoT 中心服务的 API 调用是使用你的浏览器和凭据直接进行的。

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>禁用公共网络访问后的 IoT 中心终结点、IP 地址和端口

IoT 中心是一种多租户平台即服务 (PaaS)，因此不同的客户共用相同的计算、网络和存储硬件资源池。 IoT 中心的主机名会映射到一个公共终结点，该终结点具有可通过 Internet 以公开方式路由的 IP 地址。 不同的客户将共享此 IoT 中心公共终结点，广域网和本地网络中的 IoT 设备均可对其进行访问。 

强制禁用对特定 IoT 中心资源的公共网络访问，以确保隔离。 为了使服务对其他使用公共路径的客户资源保持活动状态，其公共终结点需保持可解析状态，IP 地址需保持可发现状态，端口需保持打开状态。 不必为此担忧，因为 Microsoft 集成了多层安全保护，可确保租户间完全隔离。 若要了解详细信息，请参阅 [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md#tenant-level-isolation)。

## <a name="ip-filter"></a>筛选器 

如果禁用了公用网络访问，则会忽略所有 [IP 筛选器](iot-hub-ip-filtering.md)规则。 这是因为系统会阻止公共 Internet 中的所有 IP。 若要使用 IP 筛选器，请使用“所选 IP 范围”选项。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>与事件中心兼容的内置终结点的 bug 修复

IoT 中心有一个 bug，即，禁用对 IoT 中心的公用网络访问后，仍可通过公共 Internet 访问[与事件中心兼容的内置终结点](iot-hub-devguide-messages-read-builtin.md)。 若要了解有关此 bug 的详细信息，并与我们联系以解决此 bug，请参阅[如果禁用 IoT 中心的公用网络访问，将禁止访问内置事件中心终结点](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)。
