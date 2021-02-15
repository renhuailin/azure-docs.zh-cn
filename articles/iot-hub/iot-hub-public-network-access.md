---
title: 管理 Azure IoT 中心的公用网络访问
description: 有关如何禁用和启用 IoT 中心的公用网络访问的文档
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 27552b2c39f2d1e5d9cc1719a9cc2944e088773a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517121"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>管理 IoT 中心的公用网络访问

若要仅允许访问 [VNet 中 IoT 中心的专用终结点](virtual-network-support.md)，请禁用公共网络访问。 为此，请使用 Azure 门户或 `publicNetworkAccess` API。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>使用 Azure 门户禁用公用网络访问

1. 访问 [Azure 门户](https://portal.azure.com)
2. 导航到 IoT 中心。
3. 从左侧菜单中选择“网络”。
4. 在“允许公用网络访问”下，选择“已禁用”
5. 选择“保存” 。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="显示在 Azure 门户中的何处禁用公用网络访问的图像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

若要启用公共网络访问权限，请选择 " **所有网络**"，然后选择 " **保存**"。

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>禁用公共网络访问后的 IoT 中心终结点、IP 地址和端口

IoT 中心是一种多租户的平台即服务 (PaaS) ，因此不同的客户共用相同的计算、网络和存储硬件资源池。 IoT 中心的主机名映射到一个公共终结点，该终结点具有通过 internet 通过公共方式路由的 IP 地址。 不同的客户将共享此 IoT 中心公共终结点，广域网和本地网络中的 IoT 设备均可对其进行访问。 

对特定 IoT 中心资源强制禁用公共网络访问可确保隔离。 为了使服务对使用公用路径的其他客户资源保持活动状态，其公共终结点仍可解析，IP 地址可发现，端口仍保持打开状态。 这不是导致问题的原因，因为 Microsoft 集成了多个安全层以确保租户之间完全隔离。 若要了解详细信息，请参阅 [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md#tenant-level-isolation)。

## <a name="ip-filter"></a>筛选器 

如果禁用了公用网络访问，则会忽略所有 [IP 筛选器](iot-hub-ip-filtering.md)规则。 这是因为系统会阻止公共 Internet 中的所有 IP。 若要使用 IP 筛选器，请使用“所选 IP 范围”选项。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>与事件中心兼容的内置终结点的 bug 修复

IoT 中心有一个 bug，即，禁用对 IoT 中心的公用网络访问后，仍可通过公共 Internet 访问[与事件中心兼容的内置终结点](iot-hub-devguide-messages-read-builtin.md)。 若要了解有关此 bug 的详细信息，并与我们联系以解决此 bug，请参阅[如果禁用 IoT 中心的公用网络访问，将禁止访问内置事件中心终结点](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)。
