---
title: Azure DDoS 保护标准缓解的攻击类型
description: 了解 Azure DDoS 保护标准保护的攻击类型。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991768"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 攻击的类型概述

DDoS 保护标准可缓解以下攻击类型：

- **容量耗尽攻击**：这些攻击通过大量看似合法的流量淹没网络层。 它们包括 UDP 洪水、放大洪水以及其他欺骗数据包洪水。 对于 Azure 的全球网络规模，DDoS 保护标准通过吸收和清理这些潜在的多 gb 攻击，自动进行处理。
- **协议攻击**：这些攻击通过利用第3层和第4层协议堆栈中的漏洞，使目标无法访问。 它们包括 SYN 洪水攻击、反射攻击和其他协议攻击。 DDoS 保护标准通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。 
- **资源（应用程序）层攻击**：这些攻击以 Web 应用程序数据包为目标来中断主机之间的数据传输。 它们包括 HTTP 协议冲突、SQL 注入、跨站点脚本和其他第7层攻击。 使用 Web 应用程序防火墙（例如，Azure [应用程序网关 web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)）以及 DDoS 保护标准来防范这些攻击。 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中还提供了第三方 Web 应用程序防火墙产品。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS 保护标准

标准 DDoS 保护可保护虚拟网络中的资源，包括与虚拟机、负载均衡器和应用程序网关相关联的公共 IP 地址。 与应用程序网关 web 应用程序防火墙或使用公共 IP 在虚拟网络中部署的第三方 web 应用程序防火墙结合使用时，DDoS 保护标准可提供第3层到第7层缓解功能。

## <a name="next-steps"></a>后续步骤

- 了解如何 [创建 DDoS 保护计划](manage-ddos-protection.md)。