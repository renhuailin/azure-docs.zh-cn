---
title: 利用 Azure API 管理连接至内部虚拟网络
description: 了解如何使用内部模式在虚拟网络中设置和配置 Azure API 管理
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 83c0e0a7e04130a25267f7a9dafc1cdd888eb53a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537218"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>在内部模式下使用 Azure API 管理连接到虚拟网络 
借助 Azure 虚拟网络 (VNET)，Azure API 管理可以使用多种 VPN 技术来管理无法访问 Internet 的 API，以建立连接。 你可以通过[外部](./api-management-using-with-vnet.md)或内部模式来部署 API 管理。 有关 VNET 连接选项、要求和注意事项，请参阅[将虚拟网络与 Azure API 管理配合使用](virtual-network-concepts.md)。

通过本文，你将了解如何在内部 VNET 模式中部署 API 管理。 在此模式下，只能在访问权限由你控制的 VNET 中查看以下服务终结点。
* API 网关
* 开发人员门户
* 直接管理
* Git 

> [!NOTE]
> 这些服务终结点均未在公共 DNS 上注册。 在为 VNET [配置 DNS](#dns-configuration) 之前，服务终结点将保持不可访问状态。

在内部模式中使用 API 管理可以：

* 通过使用 Azure VPN 连接或 Azure ExpressRoute，使专用数据中心内托管的 API 可由其外部的第三方安全地访问。
* 通过公共网关公开基于云的 API 和本地 API，以便启用混合云方案。
* 使用单一网关终结点管理托管在多个地理位置的 API。

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="连接到内部 VNET":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

某些先决条件因 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。 

> [!TIP]
> 使用门户创建或更新 API 管理实例的网络配置时，该实例将托管在 `stv2` 计算平台上。

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **API 管理实例。** 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

* 虚拟网络和子网位于 API 管理实例所在的区域和订阅中。 子网可以包含其他 Azure 资源。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > 在 `stv2` 平台上的内部虚拟网络中部署 API 管理服务时，该服务将使用公共 IP 地址资源托管在[标准 SKU](../load-balancer/skus.md) 中的内部负载均衡器后面。

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **API 管理实例。** 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

* 虚拟网络和子网位于 API 管理实例所在的区域和订阅中。

    子网必须专用于 API 管理实例。 若尝试将 Azure API 管理实例部署到包含其他资源的资源管理器 VNET 子网，则部署将失败。

   > [!NOTE]
   > 在 `stv1` 平台上的内部虚拟网络中部署 API 管理服务时，该服务将托管在[基本 SKU](../load-balancer/skus.md) 中的内部负载均衡器后面。

---

## <a name="enable-vnet-connection"></a>启用 VNET 连接

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>使用 Azure 门户启用 VNET 连接（`stv2` 平台）

1. 转到 [Azure 门户](https://portal.azure.com)，查找 API 管理实例。 搜索并选择“API 管理服务”。
1. 选择你的 API 管理实例。
1. 选择“虚拟网络”。
1. 选择“内部”访问类型。
1. 在预配了 API 管理服务的位置（区域）列表中： 
    1. 选择“位置”  。
    1. 选择“虚拟网络”、“子网”和“IP 地址”  。 
        * 系统将在 VNET 列表中填充你所配置区域内设置的 Azure 订阅中的可用资源管理器虚拟网络。
1. 选择“应用”。 API 管理实例的“虚拟网络”页将使用新的 VNET 和子网选项进行更新。
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="在 Azure 门户中设置内部 VNET":::
1. 继续为 API 管理实例的其余位置配置 VNET 设置。
1. 在顶部导航栏中，选择“保存”，然后选择“应用网络配置”。

    更新 API 管理实例可能需要 15 到 45 分钟。

成功部署后，你应该能在“概述”边栏选项卡中查看你 API 管理服务的专用虚拟 IP 地址和公共虚拟 IP 地址。 有关 IP 地址的详细信息，请参阅本文中的[路由](#routing)。

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Azure 门户中的公共和专用 IP 地址"::: 

> [!NOTE]
> 由于网关 URL 未在公共 DNS 上注册，Azure 门户上提供的测试控制台不适用于内部 VNET 部署的服务。 应改用开发人员门户上提供的测试控制台。

### <a name="enable-connectivity-using-a-resource-manager-template"></a>使用资源管理器模板启用连接

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>API 版本 2021-01-01-preview（`stv2` 平台）

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>API 版本 2020-12-01（`stv1` 平台）

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>使用 Azure PowerShell cmdlet 启用连接（`stv1` 平台）

在 VNET 中[创建](/powershell/module/az.apimanagement/new-azapimanagement)或[更新](/powershell/module/az.apimanagement/update-azapimanagementregion) API 管理实例。

## <a name="dns-configuration"></a>DNS 配置

在外部 VNET 模式中，DNS 由 Azure 负责管理。 在内部 VNET 模式中，你必须自行管理 DNS。 我们建议：
1. 配置 Azure [DNS 专用区域](../dns/private-dns-overview.md)。
1. 将 Azure DNS 专用区域链接到已部署 API 管理服务的 VNET。 

了解如何[在 Azure DNS 中设置专用区域](../dns/private-dns-getstarted-portal.md)。

> [!NOTE]
> API 管理服务不会侦听其 IP 地址上的请求。 它只响应到发往其服务终结点上配置的主机名的请求。 这些终结点包括：
> * API 网关
> * Azure 门户
> * 开发人员门户
> * 直接管理终结点
> * Git

### <a name="access-on-default-host-names"></a>基于默认主机名的访问权限
当你创建 API 管理服务（例如，`contosointernalvnet`）时，将默认配置以下服务终结点：

| 端点 | 终结点配置 |
| ----- | ----- |
| API Gateway | `contosointernalvnet.azure-api.net` |
| 开发人员门户 | `contosointernalvnet.portal.azure-api.net` |
| 新的开发人员门户 | `contosointernalvnet.developer.azure-api.net` |
| 直接管理终结点 | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

若要访问这些 API 管理服务终结点，你可以在连接到 VNET（其中部署了 API 管理）的子网中创建虚拟机。 假设服务的内部虚拟 IP 地址为 10.1.0.5，可按下面所示映射 hosts 文件。 在 Windows 上，此文件位于 `%SystemDrive%\drivers\etc\hosts`。 

| 内部虚拟 IP 地址 | 终结点配置 |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

然后即可从创建的虚拟机访问所有服务终结点。

如果你在 VNET 中使用自定义 DNS 服务器，则还可创建 DNS 记录并从 VNET 中的任何位置访问这些终结点。

### <a name="access-on-custom-domain-names"></a>基于自定义域名的访问权限

如果你不想通过默认主机名访问 API 管理服务： 

1. 为你的所有服务终结点设置[自定义域名](configure-custom-domain.md)，如下图所示：

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="设置自定义域名":::

2. 在你的 DNS 服务器中创建记录，以访问可从 VNET 中访问的终结点。

## <a name="routing"></a>路由

为内部虚拟网络中的 API 管理实例配置以下虚拟 IP 地址。 详细了解 [API 管理的 IP 地址](api-management-howto-ip-addresses.md)。

| 虚拟 IP 地址 | 说明 |
| ----- | ----- |
| 专用虚拟 IP 地址 | 来自 API 管理实例子网范围内部的负载均衡 IP 地址 (DIP)，通过该地址可以访问 API 网关、开发人员门户、管理和 Git 终结点。<br/><br/>用 VNET 使用的 DNS 服务器注册此地址。     |  
| 公共虚拟 IP 地址 | 仅用于通过端口 3443 传输到管理终结点的控制平面流量。 可以锁定到 [ApiManagement][ServiceTags] 服务标记。 | 

负载均衡的公共和专用 IP 地址可以在 Azure 门户的“概述”边栏选项卡上找到。

> [!NOTE]
> 在以下情况下，API 管理实例的 VIP 地址将会更改：
> * VNET 处于启用或禁用状态。 
> * API 管理从“外部”转为“内部”虚拟网络模式，反之亦然 。
> * 在实例所在的某个位置启用、更新或禁用[区域冗余](zone-redundancy.md)设置（仅适用于高级 SKU）。
>
> 你可能需要更新 VNET 中的 DNS 注册、传递规则和 IP 限制列表。

### <a name="vip-and-dip-addresses"></a>VIP 和 DIP 地址

DIP 地址将分配给服务中每个基础虚拟机，用于访问 VNET 中的资源。 VIP 地址将用于访问 VNET 外部的资源。 如果使用 IP 限制列表保护 VNET 内的资源，那么你必须指定部署了 API 管理服务的子网的整个范围以授予或限制该服务的访问权限。

详细了解[建议的子网大小](virtual-network-concepts.md#subnet-size)。

#### <a name="example"></a>示例

如果在高级层上的内部 VNET 中部署 1 个[容量单位](api-management-capacity.md)的 API 管理，则会使用 3 个 IP 地址：1 个地址用于专用 VIP，另外两个地址用于两个 VM 的 DIP。 如果横向扩展到 4 个单位，则会将子网中的其他 IP 用于附加的 DIP。  

如果目标终结点上只有固定的一组 DIP 加入了允许列表，则将来添加新单位会导致连接失败。 出于此原因，并且由于子网完全由你控制，我们建议将后端中的整个子网加入允许列表。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [在 VNET 中设置 Azure API 管理时使用的网络配置][Common network configuration problems]
* [VNET 常见问题解答](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中创建记录](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

