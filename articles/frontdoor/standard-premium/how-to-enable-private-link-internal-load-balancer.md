---
title: 使用专用链接将 Azure Front Door Premium 连接到内部负载均衡器源
titleSuffix: Azure Private Link
description: 了解如何将 Azure Front Door Premium 连接到内部负载均衡器。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: duau
ms.openlocfilehash: e902c8d3a1662f83328ab39a85a11009c58da24b
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606575"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>使用专用链接将 Azure Front Door Premium 连接到内部负载均衡器源

本文将指导你配置 Azure Front Door Premium SKU，以使用 Azure 专用链接服务连接到内部负载均衡器源。

## <a name="prerequisites"></a>先决条件

创建[专用链接服务](../../private-link/create-private-link-service-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="enable-private-link-to-an-internal-load-balancer"></a>启用到内部负载均衡器的专用链接
 
在本节中，你将把专用链接服务映射到在 Azure Front Door 专用网络中创建的专用终结点。 

1. 在 Azure Front Door Premium 配置文件中的“设置”下，选择“源组”。

1. 选择要为内部负载均衡器启用专用链接的原始组。

1. 选择“+ 添加源”来添加内部负载均衡器源。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="启用到内部负载均衡器的专用链接的屏幕截图。":::

1. 对于“选择 Azure 资源”，选择“在我的目录中”。 选择或输入以下设置，以配置希望 Azure Front Door Premium 进行专用连接的站点。

    | 设置 | 值 |
    | ------- | ----- |
    | 区域 | 选择与源相同或最近的区域。 |
    | 资源类型 | 选择“Microsoft.Network/privateLinkServices”。 |
    | 资源 | 选择绑定到内部负载均衡器的专用链接。 |
    | 目标子资源 | 留空。 |
    | 请求消息 | 自定义消息或选择默认消息。 |

1. 然后依次选择“添加”和“更新”以保存配置。

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-private-link-service"></a>批准来自专用链接服务的 Azure Front Door Premium 专用终结点连接

1. 转到专用链接中心，并选择“专用链接服务”。 然后选择专用链接名称。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="专用链接列表的屏幕截图。":::

1. 选择“设置”下的“专用终结点连接”。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="专用连接概述页的屏幕截图。":::

1. 选择 Azure Front Door Premium 中的“挂起”专用终结点请求，然后选择“批准”。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="专用链接的挂起审批屏幕截图。":::

1. 批准后应类似于下面的屏幕截图。 完全建立连接需要几分钟时间。 现在可以从 Azure Front Door Premium 访问内部负载均衡器。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="已批准的专用链接请求的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

详细了解[专用链接服务](../../private-link/private-link-service-overview.md)。
