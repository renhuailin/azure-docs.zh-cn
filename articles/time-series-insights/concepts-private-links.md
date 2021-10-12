---
title: 专用链接 - Azure 时序见解第 2 代 | Microsoft Docs
description: Azure 时序见解第 2 代中的专用链接和公共访问限制概述。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207766"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>使用 Azure 专用链接进行专用网络访问（预览） 

使用 [Azure 专用链接](/azure/private-link/private-link-overview)服务，可以通过  [Azure 虚拟网络 (VNet)](/azure/virtual-network/virtual-networks-overview) 中的专用终结点访问 Azure 资源（例如  [Azure 事件中心](/azure/event-hubs/event-hubs-about)、 [Azure 存储](/azure/storage/common/storage-introduction)和  [Azure Cosmos DB](/azure/cosmos-db/introduction)）以及 Azure 托管的客户服务和合作伙伴服务。 

同样，可以将专用终结点用于时序见解实例，允许位于虚拟网络中的客户端通过专用链接安全地访问实例。 

## <a name="about-the-private-endpoints"></a>关于专用终结点

专用终结点使用 Azure VNet 地址空间的 IP 地址。 专用网络上的客户端与时序见解实例之间的网络流量将通过 VNet 以及 Microsoft 主干网络上的专用链接，因此不会向公共 Internet 公开。 下方直观地展示了此系统： 

[![时序专用链接 DNS](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

客户还可以阻止对 TSI 环境进行的公共访问，因此只能从 VNET 访问它。 通过为时序见解实例配置专用终结点，可以保护时序见解实例，消除曝光危险，以及避免 VNet 数据外泄。 

[![时序专用链接网络](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

启用专用终结点并限制公共访问后，客户必须使用另一地址让 TSI 资源管理器访问 TSI 环境。 可以在 Azure 门户的“概述”部分找到该地址。 

## <a name="next-steps"></a>后续步骤

* 有关详细信息，请参阅[如何为 TSI 环境配置专用终结点](./how-to-private-links.md) 