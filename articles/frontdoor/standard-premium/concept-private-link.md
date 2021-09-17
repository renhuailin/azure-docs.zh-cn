---
title: 在 Azure Front Door 标准版/高级版（预览版）中使用专用链接保护源
description: 本页提供有关如何使用专用链接保护与源的连接的信息。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 53c719bb451b6bc8239fbd0f68bb6ad423b37b11
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607136"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>在 Azure Front Door 标准版/高级版（预览版）中使用专用链接保护源

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看 [Azure Front Door 文档](../front-door-overview.md)。

## <a name="overview"></a>概述

使用 [Azure 专用链接](../../private-link/private-link-overview.md)，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 Azure 托管服务。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Front Door 高级版 SKU 可以通过专用链接服务连接到源。 应用程序可以托管在专用 VNet 中，也可以托管在 PaaS 服务（如 Web 应用和存储帐户）之后，因此无需公开访问源。

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Front Door 专用终结点体系结构":::

在 Azure Front Door 高级版配置中启用到源的专用链接时，Front Door 将代表你从 Front Door 的区域专用网络创建一个专用终结点。 此终结点由 Azure Front Door 管理。 你将在源收到 Azure Front Door 专用终结点请求批准的消息。 批准请求后，会从 Front Door 的虚拟网络分配一个专用 IP 地址，Azure Front Door 和源之间的流量将遍历与 Azure 网络主干间建立的专用链接。 现在，从 Azure Front Door 传入你的源的流量将得到保护。

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="启用专用终结点":::

> [!NOTE]
> 启用专用链接源并批准专用终结点连接后，需要几分钟时间才能建立连接。 在此期间，对源的请求将收到 Front Door 错误消息。 建立连接后，错误消息将消失。

## <a name="limitations"></a>限制

Azure Front Door 公共预览版期间，以下区域提供专用终结点：美国东部、美国西部 2、美国中南部和英国南部。

为获得最佳延迟，在选择启用 Front Door 专用链接终结点时，应始终选择一个最接近源的 Azure 区域。

Azure Front Door 专用终结点由平台管理，位于 Azure Front Door 订阅下。 通过 Azure Front Door，可以将专用链接连接到用于创建 Front Door 配置文件的同一客户订阅。

## <a name="next-steps"></a>后续步骤

* 若要通过专用链接服务将 Azure Front Door 高级版连接到 Web 应用，请参阅[使用专用链接将 Azure Front Door Premium 连接到 Web 应用源](../../frontdoor/standard-premium/how-to-enable-private-link-web-app.md)。
* 若要通过专用链接服务将 Azure Front Door 高级版连接到存储帐户，请参阅[使用专用链接将 Azure Front Door Premium 连接到存储帐户源](../../frontdoor/standard-premium/how-to-enable-private-link-storage-account.md)。
* 要使用专用链接服务将 Azure Front Door Premium 连接到内部负载均衡器源，请参阅[使用专用链接将 Azure Front Door Premium 连接到内部负载均衡器源](../../frontdoor/standard-premium/how-to-enable-private-link-internal-load-balancer.md)。
