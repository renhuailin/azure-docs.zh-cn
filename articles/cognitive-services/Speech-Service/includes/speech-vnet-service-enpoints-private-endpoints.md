---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: db8ae4bba6717cb1c0f27befd0897b60c2760ed2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059125"
---
## <a name="private-endpoints-and-vnet-service-endpoints"></a>专用终结点和 VNet 服务终结点

Azure 提供专用终结点和 VNet 服务终结点，用于通过[专用 Azure 主干网络](https://azure.microsoft.com/global-infrastructure/global-network/)建立流量隧道。 这些终结点类型在用途及其基于的技术上都相似。 但是，这两种技术之间存在差异，因此建议详细了解每种技术的优点和缺点，再设计你的网络。

进行选择时，应考虑以下几点：
- 这两种技术都可确保 VNet 和语音资源之间的流量不通过公共 Internet。
- 专用终结点为语音资源提供专门的专用 IP 地址。 只有在特定的 VNet 和子网中可以访问此 IP 地址。 你可以完全控制你的网络基础结构中对此 IP 地址的访问权限。
- VNet 服务终结点不为语音资源提供专门的专用 IP 地址，而是封装发送到语音资源的所有数据包，并通过 Azure 主干网络直接传递它们。
- 这两种技术都支持本地方案。 默认情况下，在使用 VNet 服务终结点时，无法从本地网络访问在虚拟网络中受到保护的 Azure 服务资源，但[这是可以设置的](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises)。
- VNet 服务点常用于以下情况：当你想要基于流量源自的 VNet 限制对你的语音资源的访问权限时。
- 对于认知服务，启用 VNet 服务终结点会强制所有认知服务资源的流量通过专用主干网络。 这需要显式网络访问配置（可在[此处](../speech-service-vnet-service-endpoint.md#configure-vnets-and-the-speech-resource-networking-settings)了解详细信息）。 专用终结点没有此限制，可为你的网络配置提供更大的灵活性：你可以使用同一 VNet 的同一子网，通过专用主干访问一项资源，通过公共 Internet 访问另一项资源。
- 专用终结点会产生[额外的费用](https://azure.microsoft.com/pricing/details/private-link)。 VNet 服务终结点是免费的。
- 专用终结点需要[额外的 DNS 配置](../speech-services-private-link.md#turn-on-private-endpoints)。
- 一项语音资源可以同时用于专用终结点和 VNet 服务终结点。

建议在先试用这两种终结点类型，再决定生产设计。 

- [专用链接和专用终结点文档](../../../private-link/private-link-overview.md)
- [VNet 服务终结点文档](../../../virtual-network/virtual-network-service-endpoints-overview.md)
