---
title: 教程：使用 Azure 流量管理器配置优先级流量路由
description: 本教程介绍如何在流量管理器中配置优先级流量路由方法
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208050"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>教程：在流量管理器中配置优先级流量路由方法

本教程介绍如何通过 Azure 流量管理器使用优先级路由方法将用户流量路由到特定终结点。 在此路由方法中，需要定义进入流量管理器配置文件配置的每个终结点的顺序。 来自用户的流量将按列出它们的顺序路由到终结点。 如果要配置服务故障转移，则此路由方法非常有用。 主终结点的优先级编号为“1”，并将为所有传入请求提供服务。 而优先级较低的终结点将充当备份。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 根据优先级路由创建流量管理器配置文件。
> - 添加终结点。
> - 配置终结点的优先级。
> - 使用流量管理器配置文件。
> - 删除流量管理器配置文件。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="to-configure-the-priority-traffic-routing-method"></a>配置优先级流量路由方法
1. 在浏览器中，登录 [Azure 门户](https://portal.azure.com)。

1. 选择左侧的“+ 创建资源”。 搜索“流量管理器配置文件”，然后选择“创建” 。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="创建流量管理器优先级配置文件":::

1. 在“创建流量管理器配置文件”页中，定义以下设置：

    | 设置         | 值                                              |
    | ---             | ---                                                |
    | 名称            | 提供配置文件的名称。 此名称在 trafficmanager.net zone 中必须是唯一的。 若要访问流量管理器配置文件，请使用 DNS 名称 `<profilename>.trafficmanager.net`。 |    
    | 路由方法  | 选择“优先级”。  |
    | 订阅    | 选择订阅。 |
    | 资源组   | 使用现有资源组，或创建新的资源组，以在其下放置此配置文件。 如果选择创建新的资源组，请使用“资源组位置”  下拉列表来指定资源组位置。 此设置指的是资源组的位置，对全局部署的流量管理器配置文件没有影响。 |

1. 选择“创建”以部署流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="创建流量管理器配置文件优先级":::

## <a name="add-endpoints"></a>添加终结点

1. 从列表中选择该流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="流量管理器配置文件列表":::

1. 选择“设置”下的“终结点”，然后选择“+ 添加”以添加新终结点。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="流量管理器的“添加终结点”":::

1. 选择或输入以下设置： 

    | 设置                | “值”                                              |
    | ---                    | ---                                                |
    | 类型                   | 选择终结点类型。 |    
    | 名称                   | 提供一个名称，以标识此终结点。 |
    | 目标资源类型   | 选择目标的资源类型。 |
    | 目标资源        | 从列表中选择资源。 |
    | 优先级               | 为此终结点提供一个优先级编号。 1 为最高优先级。 |


1. 选择“添加”以添加该终结点。 重复步骤 2 和步骤 3 以添加更多终结点。 请记得设置相应的优先级编号。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="添加优先级为 1 的终结点":::

1. 在“终结点”页中，查看终结点的优先级顺序。 如果选择了“优先级”  流量路由方法，则所选终结点的顺序很重要。 验证终结点的优先级顺序。  主终结点位于顶部。 请仔细检查显示的顺序。 所有请求均会路由到第一个终结点；如果流量管理器检测到其处于不正常状态，则流量会自动故障转移到下一终结点。 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="优先级终结点列表":::

1. 若要更改终结点优先级顺序，请选择终结点，更改优先级值，然后选择“保存”以保存终结点设置。

## <a name="use-the-traffic-manager-profile"></a>使用流量管理器配置文件

1.  在门户的搜索栏中，搜索在前面部分中创建的“流量管理器配置文件”名称，并在显示的结果中选择该流量管理器配置文件。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="搜索流量管理器配置文件":::

1.  “流量管理器配置文件”概述页面会显示新建的流量管理器配置文件的 DNS 名称。 任何客户端（例如，通过使用 Web 浏览器导航到客户端）均可使用此名称来路由到由路由类型确定的相应终结点。 在这种情况下，所有请求均会路由到第一个终结点，如果流量管理器检测到其处于不正常状态，则流量会自动故障转移到下一终结点。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="流量管理器 DNS 名称":::

1. 流量管理器配置文件正常工作后，请在权威 DNS 服务器上编辑 DNS 记录，将公司域名指向流量管理器域名。

## <a name="clean-up-resources"></a>清理资源

如果不再需要流量管理器配置文件，请找到该配置文件，然后选择“删除配置文件”。

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="删除流量管理器优先级配置文件":::

## <a name="next-steps"></a>后续步骤

若要了解有关优先级路由方法的详细信息，请参阅：

> [!div class="nextstepaction"]
> [优先级路由方法](traffic-manager-routing-methods.md#priority-traffic-routing-method)
