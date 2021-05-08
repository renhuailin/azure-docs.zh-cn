---
title: 远程工作：用于远程工作网络的虚拟设备 (NVA) 注意事项 |Azure VPN 网关
description: 本文有助于了解在新冠肺炎疫情期间使用 Azure 中的网络虚拟设备 (NVA) 时应考虑的事项。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499673"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>远程工作：用于远程工作的网络虚拟设备 (NVA) 注意事项

>[!NOTE]
>本文介绍如何利用网络虚拟设备、Azure、Microsoft 网络和 Azure 合作伙伴生态系统进行远程工作，以及如何缓解因新冠肺炎危机而面临的网络问题。
>

有些 Azure 客户利用 Azure 市场中的第三方网络虚拟设备 (NVA) 来为他们在新冠肺炎疫情期间居家办公的员工提供点到站点 VPN 等关键服务。 本文概述了在 Azure 中利用 NVA 来提供远程访问解决方案时需要考虑的一些简要指南。

## <a name="nva-performance-considerations"></a>NVA 性能注意事项

Azure 市场中的所有主要 NVA 供应商都应该会有针对 VM 大小和部署其解决方案时使用的实例数方面的建议。  虽然几乎所有的 NVA 供应商都能让你选择在某个给定区域中可供你使用的任意大小，但请遵循供应商针对 Azure VM 实例大小提出的建议，这一点很重要，因为这些建议是供应商已在 Azure 中完成了相关性能测试的 VM 大小。  

### <a name="consider-the-following"></a>请注意以下几点

- 容量和并发用户数 - 此数字对于点到站点 VPN 用户尤为重要，因为每个已连接的用户都将创建一个加密 (IPSec 或 SSL VPN) 隧道。  
- 聚合吞吐量 -为了容纳需要为其提供远程访问的用户数而需要的聚合带宽是多少。
- 你将需要的 VM 大小 - 应始终使用 NVA 供应商建议的 VM 大小。  对于点到站点 VPN，如果你将会有很多并发的用户连接，则应使用更大的 VM 大小，如 [Dv2 和 DSv2 系列](../virtual-machines/dv2-dsv2-series.md "Dv2 和 DSv2 系列") VM。 这些 VM 往往会具有更多的 vCPU，并且可以处理更多的并发 VPN 会话。  Azure 中更大的 VM 大小相比于较小的 VM 大小，除了具有更多的虚拟核心之外，还会有更多的聚合带宽容量。
    > 重要：每个供应商利用资源的方式是不同的。  如果你不清楚应该使用哪种实例大小来容纳预计的用户负载，则应直接联系软件供应商，请他们提供建议。
- 实例数 -如果你预计会有大量的用户和连接，则会存在纵向扩展 NVA 实例大小可能会达到的限制。  请考虑部署多个 VM 实例。
- IPSec VPN 与 SSL VPN - 一般而言，实现 IPSec VPN 比实现 SSL VPN 会有更好的性能。  
- 许可 - 请确保已为 NVA 解决方案购买的软件许可证将能够保障在新冠肺炎疫情期间可能会出现的突然增长。  许多 NVA 许可计划会限制解决方案能够提供的连接数或带宽。
- 加速网络 -请考虑能够支持加速网络的 NVA 解决方案。  使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，在受支持的 VM 类型上处理最苛刻的网络工作负荷时降低延迟、抖动和 CPU 利用率。 大多数常规用途实例以及具有 2 个或更多 vCPU 的计算优化实例都支持加速网络。

## <a name="monitoring-resources"></a>监视资源

每个 NVA 解决方案都具有自己的工具和资源，用于监视其 NVA 的性能。  请查阅供应商文档，以确保了解性能限制，并且能够检测到 NVA 何时接近或达到容量限制。  除此之外，你还可以查看 Azure Monitor 网络见解，并了解有关网络虚拟设备的基本性能信息，例如：

- CPU 使用率
- 网络传入
- 网络传出
- 入站流数
- 出站流

## <a name="next-steps"></a>后续步骤

大多数主要 NVA 合作伙伴都发布了有关如何在新冠肺炎疫情期间针对突然、意外增长进行缩放的指导。 以下是一些有用的合伙伙伴资源链接。

[Barracuda 在新冠肺炎疫情期间实现居家办公，而同时保护数据](https://www.barracuda.com/covid-19/work-from-home "在新冠肺炎疫情期间实现居家办公，而同时保护数据")

[Check Point 在新冠肺炎疫情期间保护远程工作人员](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "在新冠肺炎疫情期间保护远程工作人员")

[应对新冠肺炎疫情的 Cisco AnyConnect 实现和性能/缩放参考](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "应对新冠肺炎疫情的 Cisco AnyConnect 实现和性能/缩放参考")

[Citrix 新冠肺炎疫情响应支持中心](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix 新冠肺炎疫情响应支持中心")

[F5 为解决远程工作者大幅增加的问题提供的指导](https://www.f5.com/business-continuity "F5 为解决远程工作者大幅增加的问题提供的指导")

[Fortinet 为客户和合作伙伴提供的新冠肺炎疫情更新](https://www.fortinet.com/covid-19.html "为客户和合作伙伴提供的新冠肺炎疫情更新")

[Palo Alto Networks 新冠肺炎疫情响应中心](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks 新冠肺炎疫情响应中心")

[Kemp 为业务连续性实现远程办公和始终在线的应用体验](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp 为业务连续性实现远程办公和始终在线的应用体验")

