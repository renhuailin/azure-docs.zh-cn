---
title: 教程：使用 Azure 流量管理器配置地理流量路由
description: 本教程介绍如何使用 Azure 流量管理器配置地理流量路由方法
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188660"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>教程：使用流量管理器配置地理流量路由方法

使用地理流量路由方法，可基于请求源自的地理位置将流量定向到特定终结点。 本教程说明如何使用此路由方法创建流量管理器配置文件，并配置终结点以从特定地理区域接收流量。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 根据地理路由创建流量管理器配置文件。
> - 配置嵌套终结点。
> - 使用流量管理器配置文件。
> - 删除流量管理器配置文件。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1. 在浏览器中，登录 [Azure 门户](https://portal.azure.com)。

1. 选择左侧的“+ 创建资源”。 搜索“流量管理器配置文件”，然后选择“创建” 。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="创建流量管理器配置文件":::

1. 在“创建流量管理器配置文件”页中，定义以下设置：

    | 设置         | 值                                              |
    | ---             | ---                                                |
    | 名称            | 提供配置文件的名称。 此名称在 trafficmanager.net zone 中必须是唯一的。 若要访问流量管理器配置文件，请使用 DNS 名称 `<profilename>.trafficmanager.net`。 |    
    | 路由方法  | 选择“地理”。 |
    | 订阅    | 选择订阅。 |
    | 资源组   | 使用现有资源组，或创建新的资源组，以在其下放置此配置文件。 如果选择创建新的资源组，请使用“资源组位置”  下拉列表来指定资源组位置。 此设置指的是资源组的位置，对全局部署的流量管理器配置文件没有影响。 |

1. 选择“创建”以部署流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="创建“流量管理器配置文件”页":::

## <a name="add-endpoints"></a>添加终结点

1. 从列表中选择该流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="流量管理器地理列表":::

1. 选择“设置”下的“终结点”，然后选择“+ 添加”以添加新终结点。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="添加终结点":::

1. 选择或输入以下设置： 

    | 设置                | 值                                              |
    | ---                    | ---                                                |
    | 类型                   | 选择终结点类型。 对于生产中使用的地理路由配置文件，我们强烈建议使用包含有多个终结点的子配置文件的嵌套终结点类型。 有关详细信息，请参阅[有关地理流量路由方法的常见问题解答](traffic-manager-FAQs.md)。 |    
    | 名称                   | 为该终结点命名，用于标识它。 |
    | 目标资源类型   | 选择目标的资源类型。 |
    | 目标资源        | 从列表中选择资源。 |

    > [!Note]
    > 此页面中的某些字段取决于要添加的终结点的类型：
    > 1. 如果要添加 Azure 终结点，请根据要将流量定向到的资源选择 **目标资源类型** 和 **目标**
    > 1. 如果要添加 **外部** 终结点，请为终结点提供 **完全限定域名(FQDN)**。
    > 1. 如果要添加 **嵌套终结点**，请选择与要使用的子配置文件对应的 **目标资源**，并指定 **最小子终结点计数**。

1. 在“异地映射”部分中，使用下拉列表添加要从中将流量发送到此终结点的区域。 必须至少添加一个区域。 可以映射多个区域。

1. 对于要在此配置文件下添加的所有终结点重复最后一个步骤，然后选择“保存”。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="添加流量管理器终结点":::

## <a name="use-the-traffic-manager-profile"></a>使用流量管理器配置文件

1.  在门户的搜索栏中，搜索在前面部分中创建的“流量管理器配置文件”名称，并在显示的结果中选择该流量管理器配置文件。
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="搜索流量管理器配置文件":::

1. “流量管理器配置文件”  会显示新建的流量管理器配置文件的 DNS 名称。 任何客户端（例如，通过使用 Web 浏览器导航到客户端）均可使用此名称来路由到由路由类型确定的相应终结点。 通过地理路由，流量管理器可查看传入请求的源 IP，并确定该请求所源自的区域。 如果该区域映射到某个终结点，则流量将路由到该终结点。 如果该区域未映射到终结点，则流量管理器将返回“无数据”查询响应。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="地理流量管理器概述":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要流量管理器配置文件，请找到该配置文件，然后选择“删除配置文件”。

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="删除流量管理器配置文件":::

## <a name="next-steps"></a>后续步骤

若要了解有关地理路由方法的详细信息，请参阅：

> [!div class="nextstepaction"]
> [地理流量路由方法](traffic-manager-routing-methods.md#geographic)
