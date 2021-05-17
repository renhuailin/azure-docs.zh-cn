---
title: 如何保护和控制对 Azure Web PubSub 服务终结点的网络访问
description: 概述如何控制 Azure Web PubSub 服务的网络访问
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 912de328332023e0e868a7ab0ac6088a2daeb33e
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166940"
---
# <a name="configure-network-access-control-for-azure-web-pubsub-service"></a>为 Azure Web PubSub 服务配置网络访问控制

使用 Azure Web PubSub 服务，可以根据所用网络的请求类型和子集来保护和控制对服务终结点的访问级别。 配置网络规则后，只有通过指定的网络集请求数据的应用程序才能访问 Azure Web PubSub 服务。

Azure Web PubSub 服务具有可通过 Internet 访问的公共终结点。 你还可以创建[用于 Azure Web PubSub 服务的专用终结点](howto-secure-private-endpoints.md)。 专用终结点将 VNet 中的专用 IP 地址分配给 Azure Web PubSub 服务，并通过专用链接保护 VNet 与 Azure Web PubSub 服务之间的所有流量。 Azure Web PubSub 服务网络访问控制提供对公共终结点和专用终结点的访问控制。

或者，你可以选择允许或拒绝对公共终结点和每个专用终结点的特定类型的请求。 

在网络访问控制规则生效的情况下访问 Azure Web PubSub 服务的应用程序仍需要在请求中进行适当的授权。

## <a name="scenario-a---no-public-traffic"></a>方案 A - 无公共流量

要完全拒绝所有公共流量，应该首先将公用网络规则配置为不允许任何请求类型。 然后，应配置允许访问特定 vnet 流量的规则。 借助此配置，可为应用程序生成安全网络边界。

## <a name="scenario-b---only-client-connections-from-public-network"></a>方案 B - 仅来自公共网络的客户端连接

在这种情况下，可以将公用网络规则配置为仅允许来自公用网络的客户端连接。 然后，可以将专用网络规则配置为允许来自特定 VNet 的其他类型的请求。 此配置将从公用网络中隐藏应用服务器，并在应用服务器与 Azure Web PubSub 服务之间建立安全连接。

## <a name="managing-network-access-control"></a>管理网络访问控制

可以通过 Azure 门户管理 Azure Web PubSub 服务的网络访问控制。

### <a name="azure-portal"></a>Azure 门户

1. 转到要保护的 Azure Web PubSub 服务。

1. 选择名为“网络访问控制”的设置菜单。

    :::image type="content" source="./media/howto-secure-network-access-control/portal-network-access-control.png" alt-text="Azure 门户中的网络访问控制。":::

1. 要编辑默认操作，请切换“允许/拒绝”按钮。

    > [!TIP]
    > 默认操作是在没有匹配的 ACL 规则时执行的操作。 例如，如果默认操作为“拒绝”，则将拒绝以下未明确批准的请求类型。

1. 要编辑公用网络规则，请在“公用网络”下选择允许的请求类型。

    :::image type="content" source="./media/howto-secure-network-access-control/portal-public-network.png" alt-text="在 Azure 门户中编辑公用网络 ACL。":::

1. 要编辑专用终结点网络规则，请在“专用终结点连接”下的每行中选择允许的请求类型。

1. 单击“保存”应用所做的更改。
