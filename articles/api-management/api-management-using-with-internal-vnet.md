---
title: 利用 Azure API 管理连接至内部虚拟网络
titleSuffix: Azure API Management
description: 了解如何在内部虚拟网络中设置和配置 Azure API 管理
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0f67862352f2354dbdb5439542229dbee67ae00
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107462"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>利用 Azure API 管理连接至内部虚拟网络 
借助 Azure 虚拟网络 (VNET)，Azure API 管理可以使用多种 VPN 技术来管理无法访问 Internet 的 API，以建立连接。 你可以通过[外部](./api-management-using-with-vnet.md)或内部模式来部署 API 管理。 通过本文，你将了解如何在内部 VNET 模式中部署 API 管理。

API 管理在内部 VNET 模式中部署时，你只能在你控制访问权限的 VNET 中查看以下服务终结点。
* 代理网关
* 开发人员门户
* 直接管理
* Git 

> [!NOTE]
> 这些服务终结点均未在公共 DNS 上注册。 在为 VNET [配置 DNS](#apim-dns-configuration) 之前，服务终结点将保持不可访问状态。

在内部模式中使用 API 管理可以：

* 通过使用 Azure VPN 连接或 Azure ExpressRoute，使专用数据中心内托管的 API 可由其外部的第三方安全访问。
* 通过公共网关公开基于云的 API 和本地 API，以便启用混合云方案。
* 使用单一网关终结点管理托管在多个地理位置的 API。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

+ **一个有效的 Azure 订阅**。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 一个 Azure API 管理实例（支持的 SKU：开发人员 SKU、高级 SKU 和独立 SKU）。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

在 VNET 中部署 API 管理服务时，会用到[端口列表](./api-management-using-with-vnet.md#required-ports)，且需要打开该列表。

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"> </a>在内部 VNET 中创建 API 管理
内部虚拟网络中的 API 管理服务托管在内部负载均衡器后面。 负载均衡器 SKU 取决于创建服务所用的管理 API。 有关详细信息，请参阅 [Azure 负载均衡器 SKU](../load-balancer/skus.md)。

| API 版本 | 托管在后方 |
| ----------- | ------------- |
| 2020-12-01 | 基本 SKU 中的内部负载均衡器 |
| 2020-01-01- 预览版，订阅中有公共 IP 地址 | 内部负载均衡器 标准 SKU |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>使用 Azure 门户启用 VNET 连接

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 Azure API 管理实例。
1. 选择“虚拟网络”。
1. 配置“内部”访问类型。 有关详细步骤，请参阅[使用 Azure 门户启用 VNET 连接](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)。

    ![用于在内部 VNET 中设置 Azure API 管理的菜单][api-management-using-internal-vnet-menu]

4. 选择“保存”。 

成功部署后，你应该能在“概述”边栏选项卡中查看你 API 管理服务的专用虚拟 IP 地址和公共虚拟 IP 地址。 

| 虚拟 IP 地址 | 说明 |
| ----- | ----- |
| 专用虚拟 IP 地址 | 负载均衡的 IP 地址来自 API 管理委托的子网中，可通过该地址访问 `gateway`、`portal`、`management` 和 `scm` 终结点。 |  
| 公共虚拟 IP 地址 | 仅用于控制通过 `port 3443` 到达终结点 `management` 的流量。 可以锁定到 [ApiManagement][ServiceTags] 服务标记。 |  

![配置了内部 VNET 的 API 管理仪表板][api-management-internal-vnet-dashboard]

> [!NOTE]
> 由于网关 URL 未在公共 DNS 上注册，Azure 门户上提供的测试控制台不适用于内部 VNET 部署的服务。 应改用开发人员门户上提供的测试控制台。

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"> </a>将 API 管理部署到 VNET

你也可以使用以下方法来启用 VNET 连接性。


### <a name="api-version-2020-12-01"></a>API 版本 2020-12-01

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet - 在 VNET 中[创建](/powershell/module/az.apimanagement/new-azapimanagement)或[更新](/powershell/module/az.apimanagement/update-azapimanagementregion) API 更新实例

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS 配置
在外部 VNET 模式中，DNS 由 Azure 负责管理。 在内部 VNET 模式中，你必须自行管理 DNS。 我们建议：
1. 配置 Azure DNS 专用区域。
1. 将 Azure DNS 专用区域链接到已部署 API 管理服务的 VNET。 

了解如何[在 Azure DNS 中设置专用区域](../dns/private-dns-getstarted-portal.md)。

> [!NOTE]
> API 管理服务不会侦听来自 IP 地址的请求， 它只响应到发往其服务终结点上配置的主机名的请求。 这些终结点包括：
> * 网关
> * Azure 门户
> * 开发人员门户
> * 直接管理终结点
> * Git

### <a name="access-on-default-host-names"></a>基于默认主机名的访问权限
当你创建 API 管理服务（例如，`contosointernalvnet`）时，将默认配置以下服务终结点：

| 端点 | 终结点配置 |
| ----- | ----- |
| 网关或代理 | `contosointernalvnet.azure-api.net` |
| 开发人员门户 | `contosointernalvnet.portal.azure-api.net` |
| 新的开发人员门户 | `contosointernalvnet.developer.azure-api.net` |
| 直接管理终结点 | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

若要访问这些 API 管理服务终结点，你可以在连接到 VNET（其中部署了 API 管理）的子网中创建虚拟机。 假设服务的内部虚拟 IP 地址为 10.1.0.5，则可映射 hosts 文件 (`%SystemDrive%\drivers\etc\hosts`)，如下所示：

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

1. 为你的所有服务终结点设置自定义域名，如下图所示：

   ![为 API 管理设置自定义域][api-management-custom-domain-name]

2. 在你的 DNS 服务器中创建记录，以访问可从 VNET 中访问的终结点。

## <a name="routing"></a><a name="routing"> </a> 路由

* 子网范围 (DIP) 中的负载均衡专用虚拟 IP 地址将保留，用于从 VNET 中访问 API 管理服务终结点。 
    * 在服务的概述边栏选项卡上，找到此专用 IP 地址 Azure 门户。 
    * 用 VNET 使用的 DNS 服务器注册此地址。
* 负载均衡公共 IP 地址 (VIP) 也将保留，用于仅通过 `port 3443` 访问管理服务终结点。 
    * 在 Azure 门户中的服务的概述边栏选项卡上找到此公共 IP 地址。 
    * 仅对通过 `port 3443` 达终结点 `management` 的控制平面流量使用公共 IP 地址。 
    * 此 IP 地址可以被锁定到 [ApiManagement][ServiceTags] 服务标记。
* DIP 地址将被分配给服务中每个虚拟机，并用于访问 VNET 中的资源。 VIP 地址将用于访问 VNET 外部的资源。 如果使用 IP 限制列表保护 VNET 内的资源，那么你必须指定部署了 API 管理服务的子网的整个范围以授予或限制该服务的访问权限。
* 可以在 Azure 门户中的“概述”边栏选项卡上找到负载均衡公共 IP 地址和专用 IP 地址。
* 如果你在 VNET 中删除或添加该服务，那么为公共和专用访问分配的 IP 地址可能会更改。 你可能需要更新 VNET 中的 DNS 注册、传递规则和 IP 限制列表。

## <a name="related-content"></a><a name="related-content"> </a>相关内容
要了解更多信息，请参阅下列文章：
* [在 VNET 中设置 Azure API 管理时的常见网络配置问题][Common network configuration problems]
* [VNET 常见问题解答](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中创建记录](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
