---
title: Azure DDoS 防护参考体系结构
description: 了解 Azure DDoS 防护参考体系结构。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 429702adaccf5731292192f1b3cb6a7d42859a00
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867577"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS 防护参考体系结构

标准 DDoS 防护面向[虚拟网络中部署的服务](../virtual-network/virtual-network-for-azure-services.md)。 对于其他服务，将会应用默认的基本 DDoS 防护服务。 以下参考体系结构按场景进行整理，体系结构模式已分组在一起。

## <a name="virtual-machine-windowslinux-workloads"></a>虚拟机 (Windows/Linux) 工作负荷

### <a name="application-running-on-load-balanced-vms"></a>负载均衡的 VM 上运行的应用程序

针对如何通过在负载均衡器后的规模集中运行多个 Windows VM 以提高可用性和可伸缩性，此参考体系结构显示了一组已经过验证的做法。 可对任何无状态工作负荷（例如 Web 服务器）使用此体系结构。

![负载均衡 VM 上运行的应用程序的参考体系结构示意图](./media/ddos-best-practices/image-9.png)

在此体系结构中，工作负荷分布于多个 VM 实例上。 有单个公共 IP 地址，并且 Internet 流量通过负载均衡器分布到这些 VM。 与公共 IP 关联的 Azure (Internet) 负载均衡器的虚拟网络上已启用标准 DDoS 防护。

负载均衡器将传入的 Internet 请求分配到 VM 实例。 虚拟机规模集允许手动或者基于预定义规则自动扩展或缩减 VM 的数量。 如果资源遭受 DDoS 攻击，此功能非常重要。 有关此参考体系结构的详细信息，请参阅[此文](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)。

### <a name="application-running-on-windows-n-tier"></a>在 Windows N 层上运行的应用程序

有许多方法可用来实现 N 层体系结构。 下图显示了典型的三层 Web 应用程序。 此体系结构是基于[运行负载均衡的 VM 以实现可伸缩性和可用性](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)一文构建的。 Web 层和业务层都使用负载均衡的 VM。

![Windows N 层上运行的应用程序的参考体系结构示意图](./media/ddos-best-practices/image-10.png)

在此体系结构中，已在虚拟网络上启用标准 DDoS 防护。 虚拟网络中的所有公共 IP 将得到第 3 层和第 4 层 DDoS 防护。 要获得第 7 层防护，请部署 WAF SKU 中的应用程序网关。 有关此参考体系结构的详细信息，请参阅[此文](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)。

> [!NOTE]
> 不支持在公共 IP 后运行单个 VM 的情况。

### <a name="paas-web-application"></a>PaaS Web 应用程序

此参考体系结构显示了在单个区域中运行 Azure 应用服务应用程序。 此体系结构显示了针对使用 [Azure 应用服务](https://azure.microsoft.com/documentation/services/app-service/)和 [Azure SQL 数据库](https://azure.microsoft.com/documentation/services/sql-database/)的 Web 应用程序运用的一套经过证实的做法。
已针对故障转移场景设置了备用区域。

![PaaS Web 应用程序的参考体系结构示意图](./media/ddos-best-practices/image-11.png)

Azure 流量管理器将传入的请求路由到某个区域中的应用程序网关。 在正常操作期间，它会将请求路由到活动区域中的应用程序网关。 如果该区域不可用，流量管理器会故障转移到备用区域中的应用程序网关。

从 Internet 发往 Web 应用程序的所有流量通过流量管理器路由到[应用程序网关公共 IP 地址](../application-gateway/application-gateway-web-app-overview.md)。 在此场景中，应用服务（Web 应用）本身不直接面向外部，且受应用程序网关的保护。 

我们建议配置应用程序网关 WAF SKU（预防模式）来帮助防范第 7 层（HTTP/HTTPS/Web 套接字）攻击。 此外，Web 应用配置为[仅接受来自应用程序网关 IP 地址的流量](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)。

有关此参考体系结构的详细信息，请参阅[此文](/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

## <a name="protecting-on-premises-resources"></a>保护本地资源

可以通过在 Azure 中托管公共 IP 地址并将流量重定向到本地环境的后端源，利用 Azure DDoS 防护标准版的规模、容量和效率来保护本地资源。

![保护本地资源](./media/reference-architectures/ddos-on-prem.png)

如果你的 Web 应用程序从 Internet 接收流量，则可以将 Web 应用程序托管在应用程序网关之后，然后使用 WAF 为其防范第 7 层 Web 攻击（例如 SQL 注入）。 应用程序的后端源将位于本地环境中，该环境通过 VPN 进行连接。 

本地环境中的后端资源不会公开到公共 Internet。 只有 AppGW/WAF 公共 IP 会向 Internet 公开，而应用程序的 DNS 名称会映射到该公共 IP 地址。 

在包含 AppGW/WAF 的虚拟网络上启用 DDoS 防护标准版后，DDoS 防护标准版将通过减少不良流量并将预期的清洁流量路由到应用程序来保护应用程序。 

[本文](../azure-vmware/protect-azure-vmware-solution-with-application-gateway.md)介绍如何一起使用应用程序网关和 DDoS 防护标准版，以保护在 Azure VMware 解决方案上运行的 Web 应用。

## <a name="mitigation-for-non-web-paas-services"></a>针对非 Web PaaS 服务的缓解措施

### <a name="hdinsight-on-azure"></a>Azure 上的 HDInsight

此参考体系结构显示如何为 [Azure HDInsight 群集](../hdinsight/index.yml)配置标准 DDoS 防护。 确保 HDInsight 群集已链接到虚拟网络，并在该虚拟网络上启用了 DDoS 防护。

![“HDInsight”和“高级设置”窗格，其中包含虚拟网络设置](./media/ddos-best-practices/image-12.png)

![用于启用 DDoS 防护的选项](./media/ddos-best-practices/image-13.png)

在此体系结构中，从 Internet 发往 HDInsight 群集的流量路由到与 HDInsight 网关负载均衡器关联的公共 IP。 然后，网关负载均衡器直接将流量发送到头节点或工作节点。 由于已在 HDInsight 虚拟网络上启用标准 DDoS 防护，虚拟网络中的所有公共 IP 将得到第 3 层和第 4 层 DDoS 防护。 此参考体系结构可与 N 层和多区域参考体系结构相结合。

有关此参考体系结构的详细信息，请参阅[使用 Azure 虚拟网络扩展 Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 文档。


> [!NOTE]
> 虚拟网络中使用公共 IP 的 Power Apps 或 API 管理的 Azure 应用服务环境都原生不受支持。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 DDoS 防护计划](manage-ddos-protection.md)。
