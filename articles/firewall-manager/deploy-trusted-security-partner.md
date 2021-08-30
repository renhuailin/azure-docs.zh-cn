---
title: 部署 Azure 防火墙管理器安全合作伙伴提供程序
description: 了解如何使用 Azure 门户部署 Azure 防火墙管理器安全合作伙伴提供程序。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/06/2021
ms.author: victorh
ms.openlocfilehash: 7b8dd13c5d2c3c080ca20115dfc41b23dd6e545e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725378"
---
# <a name="deploy-a-security-partner-provider"></a>部署安全合作伙伴提供程序

通过 Azure 防火墙管理器中的安全合作伙伴提供程序，你可以使用熟悉的同类最佳第三方安全即服务 (SECaaS) 产品/服务来保护用户的 Internet 访问。

要详细了解支持的场景和最佳做法指南，请参阅[什么是安全合作伙伴提供程序？](trusted-security-partners.md)


集成的第三方安全即服务 (SECaaS) 合作伙伴现已可用： 

- **Zscaler**
- **[检查点](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新中心部署第三方安全提供程序

如果要将第三方提供程序部署到现有中心，请跳过此部分。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在“搜索”中键入“防火墙管理器”，然后在“服务”下选择“防火墙管理器”  。
3. 导航到“入门”。 选择“查看安全虚拟中心”。
4. 选择“新建安全虚拟中心”。
5. 输入你的订阅和资源组，选择受支持的区域，并添加中心和虚拟 WAN 信息。 
6. 选择“包含 VPN 网关以启用安全合作伙伴提供程序”。
7. 根据要求选择适当的“网关缩放单元”。
8. 在完成时选择“下一步:Azure 防火墙”
   > [!NOTE]
   > 安全合作伙伴提供程序使用 VPN 网关隧道连接到你的中心。 如果删除 VPN 网关，与安全合作伙伴提供程序的连接将会丢失。
9. 如果要部署 Azure 防火墙来筛选专用流量，并部署第三方服务提供程序来筛选 Internet 流量，请选择 Azure 防火墙的策略。 请参阅[支持的场景](trusted-security-partners.md#key-scenarios)。
10. 如果只想在中心部署第三方安全提供程序，请选择“Azure 防火墙:启用/禁用”，并将其设置为“禁用”。 
11. 选择“下一步:安全合作伙伴提供程序”。
12. 将“安全合作伙伴提供程序”设置为“启用” 。 
13. 选择一个合作伙伴。 
14. 在完成时选择“下一步:查看 + 创建”。 
15. 查看内容，然后选择“创建”。

部署 VPN 网关可能需要超过 30 分钟的时间。

要验证是否已创建中心，请导航到“Azure 防火墙管理器”->“安全中心”。 选择“中心”>“概述页面”，以显示合作伙伴名称，状态为“安全连接挂起”。

创建中心并设置安全合作伙伴后，请继续将安全提供程序连接到中心。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在现有中心部署第三方安全提供程序

还可以选择虚拟 WAN 中的现有中心，并将其转换为安全虚拟中心。

1. 在“入门”中，选择“查看安全虚拟中心” 。
2. 选择“转换现有中心”。
3. 选择订阅和现有中心。 按照剩余步骤在新中心部署第三方安全提供程序。

请记住，必须部署 VPN 网关才能使用第三方提供程序将现有中心转换为安全中心。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>配置第三方安全提供程序以连接到安全中心

要设置连接虚拟中心 VPN 网关的隧道，第三方提供程序需要具有对中心的访问权限。 为此，请将服务主体与订阅或资源组关联，并授予访问权限。 然后，必须通过其门户将这些凭据分配给第三方。

> [!NOTE]
> 第三方安全提供程序代表你创建 VPN 站点。 此 VPN 站点不会显示在 Azure 门户中。

### <a name="create-and-authorize-a-service-principal"></a>创建服务主体并为其授权

1. 创建 Azure Active Directory (AD) 服务主体：可以跳过重定向 URL。 

   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. 为服务主体添加访问权限和作用域。
   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > 可以将访问权限限制为仅针对你的资源组，以便进行更精细的控制。

### <a name="visit-partner-portal"></a>访问合作伙伴门户

1. 按照合作伙伴提供的说明完成设置。 这包括提交 AAD 信息以检测并连接到中心、更新出口策略，以及检查连接状态和日志。

   - [Zscaler：配置 Microsoft Azure 虚拟 WAN 集成](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)。
   - [Check Point：配置 Microsoft Azure 虚拟 WAN 集成](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm)。
   - [iboss：配置 Microsoft Azure 虚拟 WAN 集成](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security)。 
   
2. 可以在 Azure 的 Azure 虚拟 WAN 门户中查看隧道创建状态。 Azure 门户和合作伙伴门户中的隧道状态均显示“已连接”后，请继续执行后续步骤，以设置路由，并选择哪些分支和 Vnet 应将 Internet 流量发送到合作伙伴。

## <a name="configure-security-with-firewall-manager"></a>使用防火墙管理器配置安全性

1. 浏览到“Azure 防火墙管理器”->“安全中心”。 
2. 选择一个中心。 现在，中心状态应显示“已预配”，而不是“安全连接挂起”。

   请确保第三方提供程序可以连接到中心。 VPN 网关上的隧道应处于“已连接”状态。 与之前的状态相比，此状态更能反映中心与第三方合作伙伴之间的连接运行状况。
3. 选择中心，并导航到“安全配置”。

   向中心部署第三方提供程序时，该第三方提供程序会将该中心转换为安全虚拟中心。 这可确保第三方提供程序向中心播发 0.0.0.0/0（默认）路由。 但是，除非你选择了哪些连接应获得此默认路由，否则连接到中心的 VNet 连接和站点不会获得此路由。
4. 通过 Azure 防火墙设置“Internet 流量”，并通过信任的安全合作伙伴设置“专用流量”来配置虚拟 WAN 安全性。 这会自动保护虚拟 WAN 中单个连接的安全。

   :::image type="content" source="media/deploy-trusted-security-partner/security-configuration.png" alt-text="安全配置":::
5. 此外，如果你的组织使用虚拟网络和分支机构中的公共 IP 范围，则需要使用“专用流量前缀”显式指定这些 IP 前缀。 公共 IP 地址前缀可以单独指定，也可以作为聚合来指定。

   如果将非 RFC1918 地址用于专用流量前缀，可能需要为防火墙配置 SNAT 策略，以对非 RFC1918 专用流量禁用 SNAT。 默认情况下，Azure 防火墙 SNAT 应用于所有非 RFC1918 流量。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>通过第三方服务的分支或 VNet Internet 流量

接下来，你可以检查 VNet 虚拟机或分支站点是否可以访问 Internet，并验证流量是否流向第三方服务。

完成路由设置步骤后，将 0/0 通过 VNet 虚拟机和分支站点发送到第三方服务路由。 无法通过 RDP 或 SSH 登录这些虚拟机。 若要登录，可以在互连 VNet 中部署 [Azure Bastion](../bastion/bastion-overview.md) 服务。

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure 门户中使用 Azure 防火墙管理器保护云网络](secure-cloud-network.md)