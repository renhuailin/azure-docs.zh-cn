---
title: 使用 Azure 门户创建支持 HTTP 到 HTTPS 重定向的 Front Door
description: 了解如何使用 Azure 门户创建支持从 HTTP 到 HTTPS 重定向流量的 Front Door。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: e3e2de040c80692824c648ee15f6ae0c97e2786c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110453311"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 门户创建支持 HTTP 到 HTTPS 重定向的 Front Door

可以通过 Azure 门户使用 TLS 终端的证书[创建 Front Door](quickstart-create-front-door.md)。 使用一个路由规则将 HTTP 流量重定向到 HTTPS。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用现有的 Web 应用资源创建 Front Door

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

1. 选择 Azure 门户左上角的“创建资源”。

1. 使用搜索栏搜索 Front Door，找到该资源类型后，选择“创建” 。

1. 选择一个订阅，然后使用现有资源组或创建新资源组。 选择“下一步”进入配置选项卡。

    > [!NOTE]
    > UI 中请求提供的位置只是资源组的位置。 你的 Front Door 配置将部署到 [Azure Front Door 的所有 POP 位置](front-door-faq.yml#where-are-the-edge-locations-for-azure-front-door-)。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="配置新 Front Door 的基本信息":::

1. Front Door 的配置在三个步骤中进行 - 添加默认前端主机，在后端池中添加后端，然后创建路由规则以映射前端主机的路由行为。 选择“前端主机”中的“+”图标创建前端主机。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Front Door 配置设计器":::

1. 为 Front Door 的默认前端主机输入全局唯一的名称。 选择“添加”以继续执行下一步。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="添加前端主机":::

### <a name="create-backend-pool"></a>创建后端池

1. 选择“后端池”中的“+”图标创建后端池。 提供后端池的名称，然后选择“添加后端”。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Front Door 配置设计器 - 后端池":::

1. 为“后端主机类型”选择“应用服务”。 选择托管你的 Web 应用的订阅，然后从“后端主机名”下拉列表中选择特定的 Web 应用。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="在后端池中添加后端":::

1. 选择“添加”以保存后端，然后再次选择“添加”以保存后端池配置 。 

## <a name="create-http-to-https-redirect-rule"></a>创建 HTTP 到 HTTPS 重定向规则

1. 在“路由规则”中选择“+”图标以创建路由。 提供路由的名称（例如“HttpToHttpsRedirect”），然后将“接受的协议”字段设置为“仅 HTTP”。 确保选择适当的前端/域。  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Front Door 配置设计器 - 路由规则":::

1. 在“路由详细信息”部分下，将“路由类型”设置为“重定向” 。 确保“重定向类型”设置为“已找到(302)”，“重定向协议”设置为“仅 HTTPS”。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="添加 HTTP 到 HTTPS 重定向路由":::

1. 选择“添加”以保存 HTTP 到 HTTPS 重定向的路由规则。

## <a name="create-forwarding-rule"></a>创建转发规则

1. 添加另一个路由规则用于处理 HTTPS 流量。 在“路由规则”中选择“+”符号，并提供路由的名称，例如“DefaultForwardingRoute”。 然后，将“接受的协议”字段设置为“仅 HTTPS”。 确保选择适当的前端/域。

1. 在“路由详细信息”部分，将“路由类型”设置为“转发”。 确保选择了适当的后端池，并且“转发协议”已设置为“仅 HTTPS”。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="添加用于处理 HTTPS 流量的转发路由" border="false":::

1. 选择“添加”以保存请求转发路由规则。

1. 选择“查看 + 创建”，然后单击“创建”以创建 Front Door 配置文件 。 创建后转到该资源。

## <a name="next-steps"></a>后续步骤

- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 详细了解 [Front Door 中的 URL 重定向](front-door-url-redirect.md)。
