---
title: 配置 Azure Front Door 路由
description: 本文介绍如何在域和源组之间配置路由。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 05/17/2021
ms.author: qixwang
ms.openlocfilehash: 8b25f56b238c7f0c42a1ba589f7da2d68344c915
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464384"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>配置 Azure Front Door 标准版/高级版路由

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

本文介绍为现有终结点创建 Azure Front Door (AFD) 路由时使用的每项设置。 将自定义域和源添加到现有 Azure Front Door 终结点后，需要配置路由以定义域和源之间的关联，才能在它们之间路由流量。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

在配置 Azure Front Door 路由之前，必须在当前终结点中至少创建了一个源组和一个自定义域。 

若要设置源组，请参阅[创建新的 Azure Front Door 标准版/高级版源组](how-to-create-origin.md)。 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>创建新的 Azure Front Door 标准版/高级版路由

1. 登录 [Azure 门户](https://portal.azure.com)，导航到你的 Azure Front Door 标准版/高级版配置文件。

1. 在“设置”下，选择“Endpoint Manager” 。
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Front Door“Endpoint Manager”设置的屏幕截图。" lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. 然后为要配置路由的终结点选择“编辑终结点”。
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="选择“编辑终结点”的屏幕截图。":::

1. 随即将显示“编辑终结点”页。 选择“路由”部分中的“+ 添加”。
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="在“编辑终结点”页添加路由的屏幕截图。":::    
    
1. 在“添加路由”页上，输入或选择以下信息。

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="“添加路由”页的屏幕截图。" lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | 设置 | 说明 |
    | --- | --- |
    | 名称 | 为新路由输入唯一名称。 |   
    | Domain| 选择一个或多个已验证且未关联到其他路由的域 |
    | 匹配模式  | 配置此路由将接受的所有 URL 路径模式。 例如，可将其设置为 `/images/*`，以接受 URL `www.contoso.com/images/*` 上的所有请求。 如果没有完全匹配的路径，AFD 首先将尝试根据精确匹配来确定流量，然后再查找匹配的通配符路径。 如果找不到具有匹配路径的传递规则，则拒绝该请求并返回“400：错误请求”错误 HTTP 应答。 |
    | 接受的协议 | 指定你希望 Azure Front Door 在客户端发出请求时接受的协议。 |
    | 重定向 | 指定是否为使用 HTTP 请求的传入请求强制使用 HTTPS |
    | 源组 | 选择返回源请求发生时应转发哪个源组。 |
    | 源路径 | 此路径用于重写 Azure Front Door 在构造转发到源的请求时将使用的 URL。 默认情况下，不提供此路径。 因此，Azure Front Door 将在对源的请求中使用传入 URL 路径。 还可以指定通配符路径，以便将传入路径的任何匹配部分复制到源的请求路径。 </br></br>要匹配的模式：`/foo/*`</br>源路径：`/fwd/`</br></br>传入 URL 路径：`/foo/a/b/c/`</br>从 Azure Front Door 到源的 URL：`fwd/a/b/c`。 |
    | 转发协议 | 选择用于转发请求的协议。 |
    | Caching | 选择此选项后，可缓存 Azure Front Door 中的静态内容。 |
    | 规则 | 选择将应用于此路由的规则集。 要详细了解如何配置规则，请参阅[为 Azure Front Door 配置规则集](how-to-configure-rule-set.md) | 

1. 选择“添加”，创建新路由。 该路由将显示在终结点的路由列表中。
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="路由表的屏幕截图。":::  
    
## <a name="clean-up-resources"></a>清理资源

若要删除不再需要的路由，请选择该路由，然后选择“删除”。 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="如何删除路由的屏幕截图。":::  

## <a name="next-steps"></a>后续步骤
若要了解自定义域，请继续学习有关如何向 Azure Front Door 终结点添加自定义域的教程。

> [!div class="nextstepaction"]
> [添加自定义域]()
