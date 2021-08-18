---
title: 使用专用终结点以私密方式连接到 Azure Web 应用
description: 使用 Azure 专用终结点以私密方式连接到 Web 应用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 07/01/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: da26939d6973792c237c84777daf2254ae27699d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105626"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>为 Azure Web 应用使用专用终结点

> [!IMPORTANT]
> 专用终结点适用于托管在以下应用服务计划中的 Windows 和 Linux Web 应用（无论是否容器化）：PremiumV2、PremiumV3、Functions Premium（有时称为“Elastic Premium”计划）。 

可以为 Azure Web 应用使用专用终结点，以允许位于专用网络中的客户端通过专用链接安全地访问应用。 专用终结点使用你的 Azure VNet 地址空间中的 IP 地址。 专用网络上客户端与 Web 应用之间的网络流量将通过 VNet 以及 Microsoft 主干网络上的专用链接，因此不会从公共 Internet 公开。

对 Web 应用使用专用终结点的目的如下：

- 通过配置专用终结点来保护 Web 应用，避免公开。
- 从那些使用 VPN 或 ExpressRoute 专用对等互连连接到 VNet 的本地网络安全地连接到 Web 应用。
- 避免从 VNet 泄露任何数据。 

如果你只是在 VNet 与 Web 应用之间需要安全连接，则服务终结点是最简单的解决方案。 如果还需要从本地通过 Azure 网关、区域对等互连 VNet 或全局对等互连 VNet 来访问 Web 应用，请使用专用终结点作为解决方案。  

有关详细信息，请参阅[服务终结点][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概述

专用终结点是一个特殊的网络接口 (NIC)，适用于虚拟网络 (VNet) 的子网中的 Azure Web 应用。
为 Web 应用创建专用终结点时，它会在专用网络上的客户端与 Web 应用之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。
专用终结点与 Web 应用之间的连接使用安全的[专用链接][privatelink]。 专用终结点仅用于到 Web 应用的传入流。 传出流将不使用此专用终结点，但你可以通过 [VNet 集成功能][vnetintegrationfeature]将传出流注入到你的网络中的另一个子网。

应用的每个槽都将进行单独配置。 每个槽最多可以插入 100 个专用终结点。 不能在槽之间共享专用终结点。

在其中插入专用终结点的子网中可以有其他资源，不需要是专用的空子网。
你还可以在与 Web 应用不同的区域中部署专用终结点。 

> [!Note]
>VNet 集成功能不能使用与专用终结点相同的子网，这是 VNet 集成功能的局限性。

从安全角度来看：

- 当你启用到 Web 应用的专用终结点时，将禁用所有公共访问权限。
- 可以在其他 VNet 和子网（包括其他区域中的 VNet）中启用多个专用终结点。
- 专用终结点 NIC 的 IP 地址必须是动态的，但在你删除专用终结点之前，该地址会保持不变。
- 专用终结点的 NIC 不能有关联的 NSG。
- 承载专用终结点的子网可以有一个关联的 NSG，但你必须禁用专用终结点的网络策略实施：请参阅[禁用专用终结点的网络策略][disablesecuritype]。 因此，不能按任何 NSG 来筛选对专用终结点的访问权限。
- 当你启用到 Web 应用的专用终结点时，系统不会评估对 Web 应用的[访问限制][accessrestrictions]配置。
- 可以通过删除目标为标记“Internet”或“Azure 服务”的所有 NSG 规则来消除 VNet 中的数据泄露风险。 为 Web 应用部署专用终结点时，只能通过专用终结点访问此特定 Web 应用。 如果有另一个 Web 应用，则必须为该 Web 应用部署另一个专用终结点。

在 Web 应用的 Web HTTP 日志中，可找到客户端源 IP。 此功能是使用 TCP 代理协议实现的，它将客户端 IP 属性转发到 Web 应用。 有关详细信息，请参阅[使用 TCP 代理 v2 获取连接信息][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 应用专用终结点全局概述](media/private-endpoint/global-schema-web-app.png)


## <a name="dns"></a>DNS

为 Web 应用使用专用终结点时，请求的 URL 必须与 Web 应用的名称匹配。 默认情况下为 mywebappname.azurewebsites.net。

默认情况下，如果没有专用终结点，则 Web 应用的公共名称是群集的规范名称。
例如，名称解析将是：

|名称 |类型 |值 |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


部署专用终结点时，将更新 DNS 条目以指向规范名称 mywebapp.privatelink.azurewebsites.net。
例如，名称解析将是：

|名称 |类型 |值 |备注 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<--此公共 IP 不是专用终结点，你将收到 403 错误|

必须设置专用 DNS 服务器或 Azure DNS 专用区域，对于测试，可以修改测试计算机的主机条目。
需要创建的 DNS 区域是：privatelink.azurewebsites.net。 使用 A 记录和专用终结点 IP 为 Web 应用注册记录。
例如，名称解析将是：

|名称 |类型 |值 |备注 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<--Azure 会在 Azure 公共 DNS 中创建此条目，将应用服务指向 privatelink，这由我们管理|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<--在 DNS 系统中管理此条目以指向专用终结点 IP 地址|

在配置此 DNS 后，可以通过默认名称 mywebappname.azurewebsites.net 以私密方式访问 Web 应用。 必须使用此名称，因为为 *.azurewebsites.net 颁发了默认证书。


如果你需要使用自定义 DNS 名称，则必须在 Web 应用中添加自定义名称。 与任何自定义名称一样，必须使用公共 DNS 解析来验证该自定义名称。 有关详细信息，请参阅[自定义 DNS 验证][dnsvalidation]。

对于 Kudu 控制台或 Kudu REST API（例如，使用 Azure DevOps 自托管代理进行部署），必须在 Azure DNS 专用区域或自定义 DNS 服务器中创建两个记录。 

| 名称 | 类型 | 值 |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.ne | A | PrivateEndpointIP | 



## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价][pricing]。

## <a name="limitations"></a>限制

将弹性高级计划中的 Azure 函数与专用终结点配合使用时，若要在 Azure Web 门户中运行或执行函数，你必须具有直接网络访问权限，否则会收到 HTTP 403 错误。 换句话说，你的浏览器必须能够访问专用终结点，以便从 Azure Web 门户执行该函数。 

最多可以将 100 个专用终结点连接到特定 Web 应用。

为 Web 应用启用专用终结点时，远程调试功能不可用。 建议将代码部署到槽并在那里对其进行远程调试。

FTP 访问是通过入站公共 IP 地址提供的。 专用终结点不支持对 Web 应用的 FTP 访问。

我们会定期改进专用链接功能和专用终结点。若要了解有关限制的最新信息，请查看[此文][pllimitations]。

## <a name="next-steps"></a>后续步骤

- 若要通过门户为 Web 应用部署专用终结点，请参阅[如何通过门户以私密方式连接到 Web 应用][howtoguide1]
- 若要使用 Azure CLI 为 Web 应用部署专用终结点，请参阅[如何通过 Azure CLI 以私密方式连接到 Web 应用][howtoguide2]
- 若要使用 PowerShell 为 Web 应用部署专用终结点，请参阅[如何通过 PowerShell 以私密方式连接到 Web 应用][howtoguide3]
- 若要使用 Azure 模板为 Web 应用部署专用终结点，请参阅[如何通过 Azure 模板以私密方式连接到 Web 应用][howtoguide4]
- 端到端示例，如何通过 ARM 模板使用 VNet 注入和专用终结点将前端 Web 应用连接到受保护的后端 Web 应用，请参阅此[快速入门][howtoguide5]
- 端到端示例，如何通过 terraform 使用 VNet 注入和专用终结点将前端 Web 应用连接到受保护的后端 Web 应用，请参阅此[示例][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
[TiP]: ../deploy-staging-slots.md#route-traffic
