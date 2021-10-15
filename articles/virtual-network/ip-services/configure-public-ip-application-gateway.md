---
title: 管理 Azure 应用程序网关的公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解在 Azure 应用程序网关上使用公共 IP 地址的方式，以及如何更改和管理配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 8139d6c6309bf971ed9a955f5ef02f0c674b152b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367393"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>管理 Azure 应用程序网关的公共 IP 地址

Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理发往 Web 应用程序的流量。 应用程序网关根据 HTTP 请求的属性做出路由决策。 属性的示例包括 URI 路径或主机头。  应用程序网关的前端是其后端池中应用程序的连接点。 

应用程序网关前端可以是专用 IP 地址和/或公共 IP 地址。  应用程序网关 V1 SKU 支持静态或动态的基本公共 IP。  V2 SKU 仅支持静态的标准 SKU 公共 IP。 应用程序网关 V2 SKU 不支持内部 IP 地址，因为此类地址只用于前端。  有关详细信息，请参阅[应用程序网关前端 IP 地址配置](../../application-gateway/configuration-front-end-ip.md)。  

本文介绍如何使用订阅中的现有公共 IP 创建应用程序网关。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的两个标准 SKU 公共 IP 地址。 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)。
    - 对于本文中的示例，请将新的公共 IP 地址命名为 myStandardPublicIP-1 和 myStandardPublicIP-2 。

## <a name="create-application-gateway-existing-public-ip"></a>使用现有公共 IP 创建应用程序网关

在本部分，你将创建一个应用程序网关资源。 你将选择在先决条件部分创建的 IP 地址作为应用程序网关的公共 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“应用程序网关”。

3. 在搜索结果中，选择“应用程序网关”。

4. 选择“+ 新建”。 

5. 在“创建应用程序网关”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。<ul><li>输入“myResourceGroupAppGW”。</li></ul>选择“确定”。 |
    | **实例详细信息** |   |
    | 应用程序网关名称 | 输入“myAppGateway”。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 层 | 保留默认值“标准 V2”。 |
    | 启用自动缩放 | 保留默认值“是”。 |
    | 最小实例计数 | 保留默认值“0”。 |
    | 最大实例计数 | 保留默认值“10”。 |
    | 可用性区域 | 保留默认值“无”。 |
    | HTTP2 | 保留默认值“禁用”。 |
    | 虚拟网络 | 选择“新建”。 <ul><li>在“创建虚拟网络”中，输入“myVNet”作为名称 。</li><li>在“地址空间”中保留默认地址空间。</li><li>在“子网”中，将“默认值”更改为“myAGSubnet”  。</li><li>在第二个子网名称中，输入“myBackendSubnet”。</li><li>在“地址范围”中，输入默认地址空间内的范围。</li></ul>选择“确定”。|

6. 选择“下一步: 前端”。

7. 在“前端”选项卡中，为“公共 IP 地址”选择“myStandardPublicIP-1”，或选择你的公共 IP 地址  。

8. 在完成时选择“下一步:**后端**。 

9. 选择“添加后端池”。 

10. 在“添加后端池”中，输入“myBackendPool”作为名称 。

11. 在完成时选择“下一步:配置”。

12. 选择“+ 添加路由规则”。 输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 规则名称 | 输入“myRoutingRule”。 |
    | **侦听器** |    |
    | 侦听器名称 | 输入“myListener”。 |
    | 前端 IP | 选择“公共”。 |
    | 前端 IP 协议 | 保留默认值“HTTP”。 |
    | 端口 | 保留默认值“80”。 |
    | **其他设置** |   |
    | 侦听器类型 | 保留默认值“基本”。 |
    | 错误页 URL | 保留默认值“否”。 |
    | 后端目标 |    |
    | 目标类型 | 保留默认值“后端池”。 |
    | 后端目标 | 选择“myBackendPool”。 |
    | HTTP 设置 | 选择“添加新订阅”。<ul><li>在“名称”中输入“myHTTPsetting” 。</li><li>将其他设置保留默认值。</li></ul>选择 **添加** 。|

13. 选择 **添加** 。

14. 依次选择“下一步: 标记”、“下一步: 查看 + 创建” 。

15. 选择“创建”。

> [!NOTE]
> 这是应用程序网关的简单部署。 有关高级配置和设置，请参阅[快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](../../application-gateway/quick-create-portal.md)
>
> 有关 Azure 应用程序网关的详细信息，请参阅[什么是 Azure 应用程序网关？](../../application-gateway/overview.md)

## <a name="change-or-remove-public-ip-address"></a>更改或删除公共 IP 地址

创建公共 IP 地址后，应用程序网关不支持更改该 IP 地址。

## <a name="more-information"></a>详细信息

* 如果动态基本 SKU IP 已关联到应用程序网关前端，该 IP 只会在网关停止或启动时才会更改。 与应用程序网关前端关联的 DNS 名称不会更改。 

## <a name="caveats"></a>注意事项

* 应用程序网关目前不支持公共 IPv6 地址。  

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何创建应用程序网关和使用现有公共 IP。 

- 有关 Azure 虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT？](../nat-gateway/nat-overview.md)。
- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。