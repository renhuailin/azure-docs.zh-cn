---
title: 配置 Azure 前门路由
description: 本文介绍如何在域和源组之间配置路由。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098469"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>配置 Azure 前门标准/高级路由

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本文介绍为现有终结点创建 Azure 前门 (AFD) 路由时使用的每个设置。 将自定义域和源添加到现有 Azure 前端终结点后，需要配置路由以定义域和源之间的关联，以便在它们之间路由流量。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

在配置 Azure 前门路由之前，必须在当前终结点中至少创建一个源组和一个自定义域。 

若要设置源组，请参阅 [创建新的 Azure 前门标准/高级源组](how-to-create-origin.md)。 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>创建新的 Azure 前门标准/高级路由

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 前门标准/高级配置文件。

1. 选择 "**设置**" 下的 **终结点管理器**。
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="前门终结点管理器设置的屏幕截图。" lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. 然后为要配置路由的终结点选择 " **编辑终结点** "。
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="选择 &quot;编辑终结点&quot; 的屏幕截图。":::

1. 此时将显示 " **编辑终结点** " 页。 选择 " **+ 添加** 路由"。
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="在 &quot;编辑终结点&quot; 页上添加路由的屏幕截图。":::    
    
1. 在 " **添加路由** " 页上，输入或选择以下信息。

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="&quot;添加路由&quot; 页的屏幕截图。" lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | 设置 | 值 |
    | --- | --- |
    | 名称 | 输入新路由的唯一名称。 |   
    | Domain| 选择一个或多个已验证且未关联到其他路由的域 |
    | 要匹配的模式  | 配置此路由将接受的所有 URL 路径模式。 例如，你可以将此设置为 `/images/*` 以接受 URL 上的所有请求 `www.contoso.com/images/*` 。 如果没有完全匹配的路径，AFD 将尝试根据精确匹配来确定流量，并查找匹配的通配符路径。 如果找不到具有匹配路径的传递规则，则拒绝该请求并返回“400：错误请求”错误 HTTP 应答。 |
    | 接受的协议 | 指定希望在客户端发出请求时 Azure 前门接受的协议。 |
    | 重定向 | 指定是否为使用 HTTP 请求的传入请求强制执行 HTTPS |
    | 源组 | 选择 "返回源" 请求发生时应将哪个源组转发到该源组。 |
    | 源路径 | 输入要缓存的资源的路径。 若要允许缓存域中的任何资源，请将此设置留空。 |
    | 转发协议 | 选择用于转发请求的协议。 |
    | Caching | 选择此选项可以使用 Azure 前门启用静态内容缓存。 |
    | 规则 | 选择将应用于此路由的规则集。 有关如何配置规则的详细信息，请参阅 [为 Azure 前门配置规则集](how-to-configure-rule-set.md) | 

1. 选择 " **添加** " 以创建新的路由。 路由将显示在终结点的路由列表中。
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="路由列表的屏幕截图。":::  
    
## <a name="clean-up-resources"></a>清理资源

若要在不再需要某个路由时将其删除，请选择该路由，然后选择 " **删除**"。 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="如何删除路由的屏幕截图。":::  

## <a name="next-steps"></a>后续步骤
若要了解自定义域，请继续阅读教程，了解如何将自定义域添加到 Azure 前门终结点。

> [!div class="nextstepaction"]
> [添加自定义域]()
