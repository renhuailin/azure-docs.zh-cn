---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453971"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>专用终结点和虚拟网络服务终结点

Azure 提供专用终结点和虚拟网络服务终结点，用于通过[专用 Azure 主干网络](https://azure.microsoft.com/global-infrastructure/global-network/)建立流量隧道。 这些终结点类型的用途和基础技术类似。 但这两种技术之间存在差异。 建议在设计网络之前了解这两种方法的优缺点。

确定要使用的技术时，需要考虑以下事项：
- 这两种技术都可确保虚拟网络和语音资源之间的流量不会通过公共 Internet 传输。
- 专用终结点为语音资源提供专门的专用 IP 地址。 只有在特定的虚拟网络和子网中可以访问此 IP 地址。 你可以完全控制你的网络基础结构中对此 IP 地址的访问权限。
- 虚拟网络服务终结点不为语音资源提供专门的专用 IP 地址。 相反，它们会封装发送到语音资源的所有数据包，并直接通过 Azure 主干网络传送这些数据包。
- 这两种技术都支持本地方案。 默认情况下，当使用虚拟网络服务终结点时，无法从本地网络访问在虚拟网络中受到保护的 Azure 服务资源。 但你可以[更改此行为](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises)。
- 虚拟网络服务终结点常用于根据流量源自的虚拟网络限制对语音资源的访问。
- 对于认知服务，启用虚拟网络服务终结点会强制所有认知服务资源的流量通过专用主干网络。 这需要显式网络访问配置。 （有关详细信息，请参阅[配置虚拟网络和语音资源网络设置](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings)。）专用终结点没有此限制，可为你的网络配置提供更大的灵活性。 通过使用同一虚拟网络的同一子网，可以实现通过专用主干访问一项资源，同时可以通过公共 Internet 访问另一项资源。
- 专用终结点会产生[额外的费用](https://azure.microsoft.com/pricing/details/private-link)。 虚拟网络服务终结点是免费的。
- 专用终结点需要[额外的 DNS 配置](../speech-services-private-link.md#turn-on-private-endpoints)。
- 一个语音资源可以同时用于专用终结点和虚拟网络服务终结点。

建议先试用这两种终结点类型，再决定生产设计。 

有关详细信息，请参阅以下资源：

- [Azure 专用链接和专用终结点文档](../../../private-link/private-link-overview.md)
- [虚拟网络服务终结点文档](../../../virtual-network/virtual-network-service-endpoints-overview.md)
