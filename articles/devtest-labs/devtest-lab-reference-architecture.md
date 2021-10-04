---
title: 企业参考体系结构
description: 本文提供了面向企业中 Azure 开发测试实验室的参考体系结构指导。
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e76219222db1dbe6aa7cdc7d7a2ef5be995109e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652351"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>面向企业的 Azure 开发测试实验室参考体系结构
本文提供了参考体系结构，可帮助你根据企业中的 Azure 开发测试实验室部署解决方案。 包括以下各项：
- 通过 Azure ExpressRoute 建立的本地连接
- 远程桌面网关，用于远程登录到虚拟机
- 与工件存储库（用于存储专用工件）的连接
- 实验室中使用的其他 PaaS 服务

![参考体系结构示意图](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>体系结构
以下是参考体系结构的关键元素：

- Azure Active Directory (Azure AD)：开发测试实验室使用 [Azure AD 服务进行标识管理](../active-directory/fundamentals/active-directory-whatis.md)。 当你为用户授予基于开发测试实验室的环境的访问权限时，请考虑以下两个重要方面：
    - 资源管理：提供对 Azure 门户的访问权限以管理资源（创建虚拟机；创建环境；启动，停止，重新启动，删除和应用工件等等）。 资源管理通过使用基于 Azure 角色的访问控制 (Azure RBAC) 来完成。 你将角色分配给用户，并设置资源和访问级别权限。
    - 虚拟机（网络级别）：在默认配置中，虚拟机使用本地管理员帐户。 如果存在可用域（[Azure AD 域服务](../active-directory-domain-services/overview.md)、本地域或基于云的域），则可以将计算机加入该域。 然后，用户可以使用基于域的标识来连接到 VM。
- 本地连接：在我们体系结构图中，使用的是 [ExpressRoute](../expressroute/expressroute-introduction.md)。 但也可以使用[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 尽管开发测试实验室不需要 ExpressRoute，但它通常用于企业。 仅当需要访问企业资源时，才需要 ExpressRoute。 常见方案是：
    - 你有本地数据，不能移到云中。
    - 你更愿意将实验室的虚拟机加入本地域。
    - 你希望所有进出云环境的网络流量都要经过本地防火墙，以实现安全性/合规性。
- 网络安全组：要基于源 IP 地址和目标 IP 地址将流量限制到云环境（或云环境内），一种常用方法是使用[网络安全组](../virtual-network/network-security-groups-overview.md)。 例如，你希望只允许来自企业网络的流量进入实验室网络。
- 远程桌面网关：企业通常会阻止企业防火墙上的传出远程桌面连接。 有几个选项可实现与开发测试实验室中基于云的环境的连接，包括：
  - 使用[远程桌面网关](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，并允许使用网关负载均衡器的静态 IP 地址。
  - 通过 ExpressRoute/站点到站点 VPN 连接[定向所有传入的 RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 当企业规划开发测试实验室部署时，此功能是一个常见的考虑因素。
- 网络服务（虚拟网络，子网）：[Azure 网络](../networking/fundamentals/networking-overview.md)拓扑是开发测试实验室体系结构中的另一个关键元素。 它控制实验室的资源是否可以通信以及是否可以访问本地部署和 Internet。 我们的架构图包括客户使用开发测试实验室的最常见方式：所有实验室都通过[虚拟网络对等互联](../virtual-network/virtual-network-peering-overview.md)进行连接，方法是使用[中心辐射模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)进行 ExpressRoute/站点到站点 VPN 与本地的连接。 但开发测试实验室直接使用 Azure 虚拟网络，因此不会对设置网络基础结构的方式有任何限制。
- 开发测试实验室：开发测试实验室是整个体系结构的关键部分。 要了解有关该服务的详细信息，请参阅[关于开发测试实验室](devtest-lab-overview.md)。
- 虚拟机和其他资源 (SaaS、PaaS、IaaS)：虚拟机是开发测试实验室支持的关键工作负载以及其他 Azure 资源。 使用开发测试实验室，企业可以轻松、快速地提供对 Azure 资源的访问（包括 VM 和其他 Azure 资源）。 了解有关[开发人员](devtest-lab-developer-lab.md)和[测试人员](devtest-lab-test-env.md)访问 Azure 的更多信息。

## <a name="scalability-considerations"></a>可伸缩性注意事项
尽管开发测试实验室没有内置配额或限制，但在实验室典型操作中使用的其他 Azure 资源具有[订阅级别的配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。 因此，在典型的企业部署中，需要多个 Azure 订阅才能涵盖开发测试实验室的大型部署。 企业最常用到的配额包括：

- 资源组：在默认配置中，开发测试实验室为每个新虚拟机创建一个资源组，或者用户通过使用该服务来创建环境。 订阅[最多可包含 980 个资源组](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 因此，这是一个订阅中的虚拟机和环境数量的上限。 还应考虑另外两种配置：
    - **[所有虚拟机都属于同一个资源组](resource-group-control.md)** ：尽管此设置可以帮助您满足资源组限制，但会影响每个资源组的资源类型限制。
    - 使用共享公共 IP：大小相同且属于同一区域的所有 VM 都进入同一资源组。 如果允许虚拟机具有公共 IP 地址，则这是一个中间地带，介于“资源组配额”与“按资源组的资源类型配额”之间。
- 每种资源类型的每个资源组的资源：[每种资源类型的每个资源组的资源的默认限制为 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)。  使用“所有 VM 属于同一资源组”配置时，用户会更快达到此订阅限制，尤其是在 VM 具有许多额外磁盘的情况下。
- 存储帐户：开发测试实验室中的实验室附带存储帐户。 对于[每个订阅每个区域的存储帐户数量](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)，Azure 配额为 250。 同一区域中的开发测试实验室的最大数目也是 250。
- 角色分配：角色分配是你为用户或主体提供对资源的访问方式（所有者、资源、权限级别）。 在 Azure 中，[每个订阅的角色分配上限为 2,000](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)。 默认情况下，开发测试实验室服务为每个 VM 创建一个资源组。 所有者被授予开发测试实验室 VM 的“所有者”权限和资源组的“读者”权限。  这样，除了在你为用户授予实验室权限时使用的分配方式之外，你创建的每个新 VM 还会使用两种角色分配方式。
- API 读取/写入：可通过多种方式自动执行 Azure 和开发测试实验室，包括 REST API、PowerShell、Azure CLI 和 Azure SDK。 利用自动化时，您可能会遇到 API 请求的另一个限制：每个订阅每小时[最多允许 12,000 个读取请求和 1,200 个写入请求](../azure-resource-manager/management/request-limits-and-throttling.md)。 将开发测试实验室自动化时，请注意此限制。

## <a name="manageability-considerations"></a>可管理性注意事项
开发测试实验室提供了一个很好的管理用户界面来使用单个实验室。 但在企业中，你可能有多个 Azure 订阅和许多实验室。 要以一致方式对所有实验室进行更改，则需要脚本/自动化。 下面是开发测试实验室部署的一些示例和最佳管理实践：

- 更改实验室设置：常见的方案是在部署中的所有实验室中更新特定的实验室设置。 例如，新的 VM 实例大小可用，必须更新所有实验室以允许这一行为。 最好是使用 PowerShell 脚本、CLI 或 REST API 自动执行这些更改。  
- 项目存储库个人访问令牌：通常，Git 存储库的个人访问令牌将在 90 天、一年或两年后过期。 要确保连续性，必须扩展个人访问令牌或创建一个新令牌。 然后，使用自动化将新的个人访问令牌应用于所有实验室。
- 限制对实验室设置的更改：通常，必须限制特定设置（例如允许使用市场图像）。 可以使用 Azure Policy 来防止更改资源类型。 或者，你可以创建一个自定义角色，并为该用户授予这个自定义角色，而不是实验室的“所有者”角色。 可以对实验室中的大多数设置（内部支持、实验室公告、允许的 VM 大小等）执行此操作。
- 要求 VM 遵循命名约定：管理员通常希望轻松识别属于基于云的开发和测试环境的 VM。 可以使用 [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns) 来实现。

请务必注意，开发测试实验室使用相同方式管理的底层 Azure 资源：网络、磁盘、计算等。 例如，Azure Policy 适用于在实验室中创建的虚拟机。 Azure 安全中心可以报告 VM 合规性。 而且 Azure 备份服务可以为实验室中的 VM 提供常规备份。

## <a name="security-considerations"></a>安全注意事项
Azure 开发测试实验室使用 Azure 中的现有资源（计算、网络等）。 因此会自动受益于平台内置的安全功能。 例如，如果要求传入的远程桌面连接仅源自公司网络，只需将网络安全组添加到远程桌面网关上的虚拟网络。 唯一的附加安全注意事项是你授予每天使用实验室的团队成员的权限级别。 最常见的权限是[“所有者”和“用户”](devtest-lab-add-devtest-user.md)。  有关这些角色的详细信息，请参阅[在 Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>后续步骤
参阅本系列教程的下一篇文章：[纵向扩展 Azure 开发测试实验室基础结构](devtest-lab-guidance-scale.md)。