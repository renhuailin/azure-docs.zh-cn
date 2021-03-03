---
title: Azure 安全基准 V2 - 网络安全
description: Azure 安全基准 V2 网络安全
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbe71b0ae7bf711d740cbba38c84b68de553f031
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698546"
---
# <a name="security-control-v2-network-security"></a>安全控制 V2：网络安全

网络安全涵盖了用于保护 Azure 网络的控制措施。 其中包括保护虚拟网络、建立专用连接、阻止和减少外部攻击以及保护 DNS。

若要查看适用的内置 Azure 策略，请参阅 [Azure 安全基准管理法规符合性内置计划的详细信息：网络安全](../../governance/policy/samples/azure-security-benchmark#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-1 | 9.2、9.4、14.1、14.2、14.3 | AC-4、CA-3、SC-7 |

确保所有 Azure 虚拟网络都遵循与业务风险相匹配的企业分段原则。 任何可能会给组织带来更高风险的系统都应隔离在其自己的虚拟网络中，并通过网络安全组 (NSG) 和/或 Azure 防火墙进行充分保护。

考虑应用程序和企业分段策略的实际情况，根据网络安全组规则限制或允许内部资源之间的流量传递。 对于明确定义的特定应用程序（例如 3 层应用），可采用高度安全的“默认拒绝，允许例外”方法。 如果有多个应用程序和终结点彼此交互，那么这种方法的扩展性可能并不好。 如果需要在大量企业分段或分支（中心辐射型拓扑）上进行集中管理，也可使用 Azure 防火墙。

请使用 Azure 安全中心自适应网络强化功能，针对网络安全组配置提出建议来根据外部流量规则限制端口和源 IP。

使用 Azure Sentinel 发现旧的不安全协议的使用，如 SSL/TLSv1、SMBv1、LM/NTLMv1、wDigest、未签名的 LDAP 绑定和 Kerberos 中的弱密码。

- [如何创建包含安全规则的网络安全组](../../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 安全中心内的自适应网络强化](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Sentinel 不安全协议工作簿](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-2 | 空值 | CA-3、AC-17、MA-4 |

在共置环境中，使用 Azure ExpressRoute 或 Azure 虚拟专用网 (VPN) 在 Azure 数据中心与本地基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 internet，它们可提供更高的可靠性、更快的速度和比典型 internet 连接更少的延迟。 对于点到站点 VPN 和站点到站点 VPN，可使用这些 VPN 选项的任意组合以及 Azure ExpressRoute 将本地设备或网络连接到虚拟网络。

若要将 Azure 中的两个或更多虚拟网络连接在一起，请使用虚拟网络对等互连或专用链接。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。

- [什么是 ExpressRoute 连接模型](../../expressroute/expressroute-connectivity-models.md)

- [Azure VPN 概述](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [虚拟网络对等](../../virtual-network/virtual-network-peering-overview.md)

- [Azure 专用链接](../../private-link/private-link-service-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-3 | 14.1 | AC-4、CA-3、SC-7 |

使用 Azure 专用链接，无需通过 Internet 即可从虚拟网络对 Azure 服务进行专用访问。 在 Azure 专用链接尚不可用的情况下，请使用 Azure 虚拟网络服务终结点。 借助 Azure 虚拟网络服务终结点，可通过 Azure 主干网络上的优化路由对服务进行安全访问。

专用访问是除 Azure 服务提供的身份验证和流量安全性之外的另一项深度防护措施。

- [了解 Azure 专用链接](../../private-link/private-link-overview.md)

- [了解虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-4 | 9.5、12.3、12.9 | SC-5、SC-7 |

针对外部网络的攻击保护 Azure 资源，包括分布式拒绝服务 (DDoS) 攻击、特定于应用程序的攻击，以及未经请求的恶意 internet 流量。 为此， Azure 提供了一些原生功能：
-   使用 Azure 防火墙保护应用程序和服务免受来自 Internet 和其他外部位置的潜在恶意流量的侵害。 

-   使用 Azure 应用程序网关、Azure Front Door 和 Azure 内容分发网络 (CDN) 中的 Web 应用程序防火墙 (WAF) 功能来保护应用程序、服务和 API，使之免受应用程序层攻击。

-   通过在 Azure 虚拟网络上启用 DDoS 标准保护，保护资产免受 DDoS 攻击。
-   使用 Azure 安全中心来检测与上述内容相关的配置错误风险。

- [Azure 防火墙文档](../../firewall/index.yml)

- [如何部署 Azure WAF](../../web-application-firewall/overview.md)

- [使用 Azure 门户管理 Azure DDoS 防护标准](../../ddos-protection/manage-ddos-protection.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-5 | 12.6、12.7 | SI-4 |

使用基于 Azure 防火墙威胁情报的筛选功能，针对进出已知恶意 IP 地址和域的流量发出警报并/或阻止该流量。 IP 地址和域源自 Microsoft 威胁智能源。 需要进行负载检查时，可以使用负载检查功能从 Azure Marketplace 部署第三方入侵检测/入侵防护系统 (ID/IPS) 。 另外，还可将基于主机的 ID/IPS 或基于主机的终结点检测和响应 (EDR) 解决方案与基于网络的 ID/IPS 结合使用，或者替代基于网络的 ID/IPS。

注意：如果在 IDS/IPS 的使用方面存在法规或其他要求，请确保始终对其进行优化，以便为 SIEM 解决方案提供高质量的警报。 

- [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace 包含第三方 ID 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender for Endpoint 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

利用服务标记和应用程序安全组 (ASG) 简化网络安全规则。

在网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的“源”或“目标”字段中指定服务标记名称，可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

还可使用应用程序安全组来帮助简化复杂的安全配置。 应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，而不是基于网络安全组中的显式 IP 地址来定义策略，从而能对虚拟机进行分组，并基于这些组定义网络安全策略。

- [了解并使用服务标记](../../virtual-network/service-tags-overview.md)

- [了解并使用应用程序安全组](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全域名服务 (DNS)

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-7 | 空值 | SC-20、SC-21 |

遵循 DNS 安全的最佳做法来防范常见攻击，例如无关联 DNS、DNS 放大攻击、DNS 中毒和欺骗等。

将 Azure DNS 用作权威 DNS 服务时，请确保使用 Azure RBAC 和资源锁保护 DNS 区域和记录，使之免受意外或恶意的修改。

- [Azure DNS 概述](../../dns/dns-overview.md)

- [安全域名系统 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [阻止无关联的 DNS 项并避免子域接管](../fundamentals/subdomain-takeover.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
