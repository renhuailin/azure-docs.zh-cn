---
title: 配置网络访问控制
titleSuffix: Azure SignalR Service
description: 为 Azure SignalR 服务配置网络访问控制。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143230"
---
# <a name="configure-network-access-control"></a>配置网络访问控制

使用 Azure SignalR 服务，可以根据所用网络的请求类型和子集来保护和控制对服务终结点的访问级别。 配置网络规则后，只有通过指定的网络集请求数据的应用程序才能访问 Azure SignalR 服务。

Azure SignalR 服务具有可通过 Internet 访问的公共终结点。 你还可以创建[用于 Azure SignalR 服务的专用终结点](howto-private-endpoints.md)。 专用终结点将 VNet 中的专用 IP 地址分配给 Azure SignalR 服务，并通过专用链接保护 VNet 与 Azure SignalR 服务之间的所有流量。 Azure SignalR 服务网络访问控制提供对公共终结点和专用终结点的访问控制。

或者，你可以选择允许或拒绝对公共终结点和每个专用终结点的特定类型的请求。 例如，可以阻止来自公共终结点的所有[服务器连接](signalr-concept-internals.md#server-connections)，并确保它们仅源自特定的VNet。

在网络访问控制规则生效的情况下访问 Azure SignalR 服务的应用程序仍需要在请求中提供适当的授权。

## <a name="scenario-a---no-public-traffic"></a>方案 A - 无公共流量

要完全拒绝所有公共流量，应该首先将公用网络规则配置为不允许任何请求类型。 然后，应配置允许访问特定 vnet 流量的规则。 借助此配置，可为应用程序生成安全网络边界。

## <a name="scenario-b---only-client-connections-from-public-network"></a>方案 B - 仅来自公共网络的客户端连接

在这种情况下，可以将公用网络规则配置为仅允许来自公用网络的[客户端连接](signalr-concept-internals.md#client-connections)。 然后，可以将专用网络规则配置为允许来自特定 VNet 的其他类型的请求。 此配置将从公用网络中隐藏应用服务器，并在应用服务器与 Azure SignalR 服务之间建立安全连接。

## <a name="managing-network-access-control"></a>管理网络访问控制

可以通过 Azure 门户管理 Azure SignalR 服务的网络访问控制。

### <a name="azure-portal"></a>Azure 门户

1. 转到要保护的 Azure SignalR 服务。

1. 单击名为“网络访问控制”的设置菜单。

    ![门户上的网络 ACL](media/howto-network-access-control/portal.png)

1. 要编辑默认操作，请切换“允许/拒绝”按钮。

    > [!TIP]
    > 默认操作是在没有匹配的 ACL 规则时执行的操作。 例如，如果默认操作为“拒绝”，则将拒绝以下未明确批准的请求类型。

1. 要编辑公用网络规则，请在“公用网络”下选择允许的请求类型。

    ![在门户上编辑公用网络 ACL ](media/howto-network-access-control/portal-public-network.png)

1. 要编辑专用终结点网络规则，请在“专用终结点连接”下的每行中选择允许的请求类型。

    ![在门户上编辑专用终结点 ACL ](media/howto-network-access-control/portal-private-endpoint.png)

1. 单击“保存”应用所做的更改。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用链接](../private-link/private-link-overview.md)。