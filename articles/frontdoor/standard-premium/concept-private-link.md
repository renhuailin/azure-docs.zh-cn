---
title: 在 Azure 前门标准/高级 () 预览版中，通过专用链接保护源
description: 本页提供有关如何使用专用链接确保与源的连接安全的信息。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 6a1ec6e0b8862c6ad2b884b019e908e7d2a59a1e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715507"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>在 Azure 前门标准/高级 () 预览版中，通过专用链接保护源

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [Azure 前门文档](../front-door-overview.md)。

## <a name="overview"></a>概述

使用[Azure 专用链接](../../private-link/private-link-overview.md)，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 azure 托管服务。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 前门高级 SKU 可以使用专用链接服务连接到 Web 应用和存储帐户的源，从而无需公开访问源。

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="前门专用终结点体系结构":::

当你在 Azure 前门 Premium 配置中启用到你的来源的专用链接时，前门会根据你的前门区域专用网络创建专用终结点。 此终结点由 Azure 前门管理。 你将收到源的 Azure 前门专用终结点请求，用于审批消息。 批准请求后，将从前门的虚拟网络分配一个专用 IP 地址，Azure 前门与源之间的流量将通过 Azure 网络主干遍历已建立的专用链接。 当来自你的 Azure 前门时，源的传入流量现在受到保护。

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="启用专用终结点":::

> [!NOTE]
> 启用专用链接来源并批准专用终结点之间后，建立连接需要几分钟时间。 在此期间，对源的请求将收到前门错误消息。 建立连接后，错误消息将消失。

## <a name="limitations"></a>限制

公共预览版期间，Azure 前门专用终结点在以下区域提供：美国东部、美国西部和美国中南部。

为获得最佳延迟，选择启用前门专用链接终结点时，应始终选取离源最近的 Azure 区域。

Azure 前门专用终结点由平台和 Azure 前门的订阅进行管理。 Azure 前门允许将专用链接连接到用于创建前门配置文件的同一客户订阅。

## <a name="next-steps"></a>后续步骤

* 若要通过专用链接服务将 Azure 前门高级版连接到 Web 应用，请参阅 [使用专用终结点连接到 web 应用](../../private-link/tutorial-private-endpoint-webapp-portal.md)。
* 若要通过专用链接服务将 Azure 前门高级版连接到存储帐户，请参阅 [使用专用终结点连接到存储帐户](../../private-link/tutorial-private-endpoint-storage-portal.md)。
