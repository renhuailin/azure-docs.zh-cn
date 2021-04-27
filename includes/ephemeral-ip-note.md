---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529588"
---
> [!NOTE]
> Azure 为未获得公共 IP 地址或位于内部基本 Azure 负载均衡器后端池中的 Azure 虚拟机提供临时 IP。 临时 IP 机制可提供无法配置的出站 IP 地址。 
>
>如果将公共 IP 地址分配给某个虚拟机或将该虚拟机置入具有或不具有出站规则的标准负载均衡器的后端池中时，将禁用其原有的临时 IP。 如果向虚拟机的子网分配 [Azure 虚拟网络 NAT](../articles/virtual-network/nat-overview.md) 网关资源，也会禁用其临时 IP。
>
> 有关 Azure 中出站连接的详细信息，请参阅[为出站连接使用源网络地址转换 (SNAT)](../articles/load-balancer/load-balancer-outbound-connections.md)。