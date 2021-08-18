---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 03080e4518fdc1997740dd156135202dfc24f778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179870"
---
> [!NOTE]
> Azure 会为未获得公共 IP 地址的或位于内部基本 Azure 负载均衡器的后端池中的 Azure 虚拟机提供默认出站访问 IP。 默认出站访问 IP 机制会提供不可配置的出站 IP 地址。 
>
>在将公共 IP 地址分配到虚拟机时，或者在将虚拟机置于具有或不具有出站规则的标准负载均衡器的后端池中时，会禁用默认出站访问 IP。 如果向虚拟机的子网分配 [Azure 虚拟网络 NAT](../articles/virtual-network/nat-gateway/nat-overview.md) 网关资源，则会禁用默认出站访问 IP。
>
> 有关 Azure 中出站连接的详细信息，请参阅[为出站连接使用源网络地址转换 (SNAT)](../articles/load-balancer/load-balancer-outbound-connections.md)。