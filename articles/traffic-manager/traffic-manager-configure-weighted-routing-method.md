---
title: 教程：使用 Azure 流量管理器配置加权轮询机制流量路由
description: 本教程介绍如何在流量管理器中使用轮询机制方法对流量进行负载均衡
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207800"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>教程：在流量管理器中配置加权流量路由方法

一种常见的流量路由方法模式是提供一组相同的终结点（包括云服务和网站），并均匀地向每个终结点发送流量。 以下步骤概述如何配置这种类型的流量路由方法。

> [!NOTE]
> Azure Web 应用已经为 Azure 区域内的网站（可能包含多个数据中心）提供了循环负载均衡功能。 流量管理器允许你在不同数据中心的网站之间分配流量。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 根据加权路由创建流量管理器配置文件。
> - 使用流量管理器配置文件。
> - 删除流量管理器配置文件。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

## <a name="configure-the-weighted-traffic-routing-method"></a>配置加权流量路由方法

1. 在浏览器中，登录 [Azure 门户](https://portal.azure.com)。

1. 在门户的搜索栏中，搜索在前面部分中创建的“流量管理器配置文件”名称，并在显示的结果中选择该流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="搜索流量管理器配置文件":::

1. 选择“配置”。然后选择或输入以下设置：

    | 设置         | “值”                                              |
    | ---             | ---                                                |
    | 路由方法            | 选择“加权”。 |    
    | DNS 生存时间 (TTL) | 该值控制客户端本地缓存名称服务器在流量管理器系统中查询已更新 DNS 条目的频率。 流量管理器中发生的任何更改（例如，流量路由方法更改或所添加的终结点的可用性更改）都需要经过这么长的时间才能在整个 DNS 服务器全局系统中刷新。 |
    | 协议    | 选择用于监视终结点的协议。 选项：HTTP、HTTPS 和 TCP |
    | 端口 | 指定端口号。 |
    | 路径 | 若要监视终结点，必须指定路径和文件名。 正斜杠“/”是有效的相对路径条目，表示文件位于根目录（默认位置）中。 |
    | 自定义标头设置 | 在 format host:contoso.com,newheader:newvalue 中配置自定义标头。 支持的对数上限为 8。 适用于 Http 和 Https 协议。 适用于配置文件中的所有终结点 |
    | 预期状态代码范围（默认值：200） | 配置状态代码范围（采用 200-299,301-301 格式）。 支持的最大范围为 8。 适用于 Http 和 Https 协议。 适用于配置文件中的所有终结点 |
    | 探测间隔 | 配置终结点运行状况探测之间的时间间隔。 你可以选择 10 秒或 30 秒。 |
    | 容许的失败次数 | 配置触发终结点失败之前容许的运行状况探测失败次数。 请输入介于 0 和 9 之间的数字。 | 
    | 探测超时 | 配置终结点运行状况探测超时之前所需的时间。此值必须至少为 5 并且小于探测时间间隔值。 |

1. 选择“保存”，以完成配置。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="流量管理器加权配置"::: 

1. 选择“终结点”并配置每个终结点的权重。 权重可以介于 1-1000 之间。 权重越高，优先级就越高。  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="流量管理器加权终结点配置"::: 

## <a name="use-the-traffic-manager-profile"></a>使用流量管理器配置文件

“流量管理器配置文件”  会显示新建的流量管理器配置文件的 DNS 名称。 任何客户端（例如，通过使用 Web 浏览器导航到客户端）均可使用此名称来路由到由路由类型确定的相应终结点。 在这种情况下，所有请求都以轮循机制的方式路由每个终结点。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="流量管理器加权概述"::: 

## <a name="clean-up-resources"></a>清理资源

如果不再需要流量管理器配置文件，请找到该配置文件，然后选择“删除配置文件”。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="删除流量管理器加权配置文件":::

## <a name="next-steps"></a>后续步骤

若要详细了解加权路由方法，请参阅：

> [!div class="nextstepaction"]
> [加权流量路由方法](traffic-manager-routing-methods.md#weighted)