---
title: Azure DDoS 保护标准可缓解的攻击类型
description: 了解 Azure DDoS 保护标准可防护的攻击类型。
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94991768"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 攻击类型概述

DDoS 保护标准可缓解以下攻击类型：

- **容量耗尽攻击**：这些攻击的目标是借助大量看似合法的流量涌入网络层。 其中包括 UDP 洪水、放大洪水以及其他欺骗性数据包洪水。 借助 Azure 的全球网络规模，DDoS 保护标准通过自动吸收和清理这些潜在的数千兆字节攻击，从而缓解这些攻击。
- **协议攻击**：这些攻击通过利用第 3 层和第 4 层协议堆栈中的漏洞，使目标无法访问。 其中包括 SYN 淹没攻击、反射攻击和其他协议攻击。 DDoS 保护标准通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。 
- **资源（应用程序）层攻击**：这些攻击以 Web 应用程序数据包为目标来中断主机之间的数据传输。 其中包括 HTTP 协议冲突、SQL 注入、跨站脚本和其他第 7 层攻击。 使用 Azure [应用程序网关 Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)等 Web 应用程序防火墙以及 DDoS 保护标准可提供对这些攻击的防御。 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中还提供了第三方 Web 应用程序防火墙产品。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS 保护标准

标准 DDoS 保护可保护虚拟网络中的资源，包括与虚拟机、负载均衡器和应用程序网关相关联的公共 IP 地址。 与应用程序网关 Web 应用程序防火墙或使用公共 IP 在虚拟网络中部署的第三方 Web 应用程序防火墙结合使用时，DDoS 保护标准可提供第 3 层到第 7 层的完整缓解功能。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 DDoS 防护计划](manage-ddos-protection.md)。