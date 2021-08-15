---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: d6ee1a0c747681d6da85ae1275a09d6ad5443b27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472411"
---
> [!NOTE]
> Azure 会为未获得公共 IP 地址的或位于内部基本 Azure 负载均衡器的后端池中的 Azure 虚拟机提供默认出站访问 IP。 默认出站访问 IP 机制会提供不可配置的出站 IP 地址。 
>
>在将公共 IP 地址分配到虚拟机时，或者在将虚拟机置于具有或不具有出站规则的标准负载均衡器的后端池中时，会禁用默认出站访问 IP。 如果向虚拟机的子网分配 [Azure 虚拟网络 NAT](../articles/virtual-network/nat-overview.md) 网关资源，则会禁用默认出站访问 IP。
>
> 有关 Azure 中出站连接的详细信息，请参阅[为出站连接使用源网络地址转换 (SNAT)](../articles/load-balancer/load-balancer-outbound-connections.md)。