---
title: 管理 Azure 负载均衡器的规则 - Azure 门户
description: 本文介绍如何使用 Azure 门户管理 Azure 负载均衡器的规则
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868468"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 负载均衡器的规则

Azure 负载均衡器支持用于配置后端池流量的规则。  在本文中，你将了解如何管理 Azure 负载均衡器的规则。

有四种类型的规则：

* 负载均衡规则 - 负载均衡器规则用于定义将传入的流量分配至后端池中所有实例的方式 。 负载均衡规则将给定的前端 IP 配置和端口映射到多个后端 IP 地址和端口。 例如，在端口 80 上创建用于对 Web 流量进行负载均衡的规则。

* 高可用性端口 - 使用“协议 - 所有”和“端口 - 0”配置的负载均衡器规则  。 通过这些规则，可以使用单条规则对到达内部标准负载均衡器所有端口的全部 TCP 和 UDP 流量进行负载均衡。 HA 端口负载均衡规则可帮助满足各种场景的需求，如虚拟网络内部网络虚拟设备 (NVA) 的高可用性和缩放。 当大量端口必须进行负载均衡时，此功能可以帮助完成。

* 入站 NAT 规则 - 入站 NAT 规则转发发送到前端 IP 地址和端口组合的传入流量。 该流量将被转发到后端池中的特定虚拟机或实例。 可以通过与负载均衡相同的基于哈希的分配来实现此端口转发。

* 出站规则 - 出站规则为后端池所标识的所有虚拟机或实例配置出站网络地址转换 (NAT) 。 此规则使后端中的实例能够与 Internet 或其他终结点进行通信（出站）。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 订阅中的标准公共负载均衡器。 有关如何创建 Azure 负载均衡器的详细信息，请参阅[快速入门：使用 Azure 门户创建公共负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-public-portal.md)。 本文中示例的负载均衡器名称是 myLoadBalancer。

- 订阅中的标准内部负载均衡器。 有关如何创建 Azure 负载均衡器的详细信息，请参阅[快速入门：使用 Azure 门户创建内部负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-internal-portal.md)。 本文中示例的负载均衡器名称是 myLoadBalancer。

## <a name="load-balancing-rules"></a>负载均衡规则

在本部分中，你将了解如何添加和删除负载均衡规则。 示例中使用的是公共负载均衡器。

### <a name="add-a-load-balancing-rule"></a>添加负载均衡规则

在此示例中，你将创建用于对端口 80 进行负载均衡的规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“负载均衡规则” 。

5. 在“负载均衡规则”中选择“+ 添加”以添加规则 。

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="标准负载均衡器中的“负载均衡规则”页面的屏幕截图。" border="true":::

6. 在“添加负载均衡规则”中输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | myHTTPRule |
    | IP 版本 | 选择 IPv4 或 IPv6 。 |
    | 前端 IP 地址 | 选择负载均衡器的前端 IP 地址。 <br> 在此示例中，它是 myFrontendIP。 |
    | 协议 | 保留默认值 TCP。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择负载均衡器的后端池。 </br> 在此示例中，它是 myBackendPool。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入 myHealthProbe 。 </br> 在“协议”中选择 HTTP 。 </br> 保留其余设置为默认值或根据需求进行调整。 </br> 选择“确定”。 |
    | 会话暂留 | 选择“无”或所需的持久性。 </br> 有关分发模式的详细信息，请参阅 [Azure 负载均衡器分发模式](load-balancer-distribution-mode.md)。 | 
    | 空闲超时(分钟) | 保留默认值 4 或将滑块移动到所需的空闲超时。 |
    | TCP 重置 | 选择“启用”。  </br> 有关 TCP 重置的详细信息，请参阅[负载均衡器 TCP 重置和空闲超时](load-balancer-tcp-reset.md)。 |
    | 浮动 IP | 如果部署需要浮动 IP，请保留默认值“禁用”，否则保留默认值“启用”。 </br> 有关浮动 IP 的信息，请参阅 [Azure 负载均衡器浮动 IP 配置](load-balancer-floating-ip.md)。 |
    | 出站源网络地址转换 (SNAT) | 保留默认值“(建议)使用出站规则为后端池成员提供对 Internet 的访问权限。” </br> 有关出站规则和 (SNAT) 的详细信息，请参阅[出站规则 Azure 负载均衡器](outbound-rules.md)和[为出站连接使用源网络地址转换 (SNAT)](load-balancer-outbound-connections.md)。|

7. 选择 **添加** 。

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="“添加负载均衡器规则”页面的屏幕截图。" border="true":::

### <a name="remove-a-load-balancing-rule"></a>删除负载均衡规则

在此示例中，你将删除负载均衡规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“负载均衡规则” 。

5. 选择要删除的规则旁边的三个点。

6. 选择“删除”。

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="删除负载均衡规则的屏幕截图。" border="true":::

## <a name="high-availability-ports"></a>高可用性端口

在本部分中，你将了解如何添加和删除高可用性端口规则。 在此示例中，你将使用内部负载均衡器。 

标准内部负载均衡器支持 HA 端口规则。

### <a name="add-high-availability-ports-rule"></a>添加高可用性端口规则

在此示例中，你将创建高可用性端口规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“负载均衡规则” 。

5. 在“负载均衡规则”中选择“+ 添加”以添加规则 。

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="标准负载均衡器中的“负载均衡规则”页面的屏幕截图。" border="true":::

6. 在“添加负载均衡规则”中输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myHARule。 |
    | IP 版本 | 选择 IPv4 或 IPv6 。 |
    | 前端 IP 地址 | 选择负载均衡器的前端 IP 地址。 <br> 在此示例中，它是 myFrontendIP。 </br> 选中“HA 端口”旁边的框。 |
    | 后端池 | 选择负载均衡器的后端池。 </br> 在此示例中，它是 myBackendPool。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入 myHealthProbe 。 </br> 在“协议”中选择 TCP 。 </br> 在“端口”中输入 TCP 端口。 在此示例中，它是端口 80。 输入满足你需求的端口。 </br> 保留其余设置为默认值或根据需求进行调整。 </br> 选择“确定”。 |
    | 会话暂留 | 选择“无”或所需的持久性。 </br> 有关分发模式的详细信息，请参阅 [Azure 负载均衡器分发模式](load-balancer-distribution-mode.md)。 | 
    | 空闲超时(分钟) | 保留默认值 4 或将滑块移动到所需的空闲超时。 |
    | TCP 重置 | 选择“启用”。  </br> 有关 TCP 重置的详细信息，请参阅[负载均衡器 TCP 重置和空闲超时](load-balancer-tcp-reset.md)。 |
    | 浮动 IP | 如果部署需要浮动 IP，请保留默认值“禁用”，否则保留默认值“启用”。 </br> 有关浮动 IP 的信息，请参阅 [Azure 负载均衡器浮动 IP 配置](load-balancer-floating-ip.md)。 |

    有关 HA 端口规则配置的详细信息，请参阅[高可用性端口概述](load-balancer-ha-ports-overview.md)。

7. 选择 **添加** 。

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="“添加负载均衡器 HA 端口规则”页面的屏幕截图。" border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>删除高可用性端口规则

在此示例中，你将删除负载均衡规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“负载均衡规则” 。

5. 选择要删除的规则旁边的三个点。

6. 选择“删除”。

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="删除 HA 端口负载均衡规则的屏幕截图。" border="true":::

## <a name="inbound-nat-rule"></a>入站 NAT 规则

入站 NAT 规则用于将连接路由到后端池中的特定 VM。 有关配置和测试入站 NAT 规则的详细信息和详细教程，请参阅[教程：使用门户在 Azure 负载均衡器中配置端口转发](tutorial-load-balancer-port-forwarding-portal.md)。

## <a name="outbound-rule"></a>出站规则

你将在本部分中了解如何添加和删除出站规则。 在此示例中，你将使用公共负载均衡器。 

标准公共负载均衡器支持出站规则。

### <a name="add-outbound-rule"></a>添加出站规则

在此示例中，你将创建出站规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“出站规则” 。

5. 在“出站规则”中选择“+ 添加”，以添加规则 。

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="标准负载均衡器中的“出站规则”页面的屏幕截图。" border="true":::

6. 在“添加出站规则”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myOutboundRule。 |
    | 前端 IP 地址 | 选择负载均衡器的前端 IP 地址。 <br> 在此示例中，它是 myFrontendIP。 | 
    | 协议 | 保留默认值“全部”。 |
    | 空闲超时(分钟) | 保留默认值 4 或移动滑块，以满足你的需求。 |
    | TCP 重置 | 保留默认值“启用”。 |
    | 后端池 | 选择负载均衡器的后端池。 </br> 在此示例中，它是 myBackendPool。 |
    | **端口分配** |   |
    | 端口分配 | 选择“手动选择出站端口数”。 |
    | **出站端口** |  |
    | 选择依据 | 选择“每个实例的端口数”。 |
    | 每个实例的端口数 | 输入“10000”。 |

7. 选择 **添加** 。

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="“添加出站规则”页面的屏幕截图。" border="true":::

### <a name="remove-an-outbound-rule"></a>删除出站规则

在此示例中，你将删除出站规则。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

3. 选择 myLoadBalancer 或你的负载均衡器。

4. 在负载均衡器页面中，选择“设置”中的“出站规则” 。

5. 选择要删除的规则旁边的三个点。

6. 选择“删除”。

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="删除出站规则的屏幕截图。" border="true":::

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何管理 Azure 负载均衡器的负载均衡规则。

有关 Azure 负载均衡器的详细信息，请参阅：
- [什么是 Azure 负载均衡器？](load-balancer-overview.md)
- [常见问题解答 - Azure 负载均衡器](load-balancer-faqs.yml)
