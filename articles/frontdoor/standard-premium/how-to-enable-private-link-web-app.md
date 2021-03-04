---
title: 使用专用链接将 Azure 前门 Premium 连接到 web 应用源
titleSuffix: Azure Private Link
description: 了解如何私下将 Azure 前门高级版连接到 webapp。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030573"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>使用专用链接将 Azure 前门 Premium 连接到 Web 应用源

本文将指导你如何配置 Azure 前门 Premium SKU，以使用 Azure 专用链接服务私下连接到你的 Web 应用。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 为源 web 服务器创建 [专用链接](../../private-link/create-private-link-service-portal.md) 服务。

> [!Note]
> 专用终结点可在公共区域中用于高级 V2 层级、高级 V3 层级 Windows Web 应用、Linux Web 应用和 Azure Functions 高级计划（有时被称为弹性高级计划）。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>在 Azure 前门 Premium 中启用到 Web 应用的私有链接
 
在本部分中，将专用链接服务映射到在 Azure 前门专用网络中创建的专用终结点。 

1. 在 Azure 前门 Premium 配置文件中的 " *设置*" 下，选择 " **源组**"。

1. 选择包含要为其启用专用链接的 Web 应用源的源组。

1. 选择 " **+ 添加原点** " 添加新的 web 应用源，或从列表中选择以前创建的 web 应用源。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="启用到 Web 应用的私有链接的屏幕截图。":::

1. 对于 " **选择 Azure 资源**"，请 **在 "我的目录" 中** 选择。 选择或输入以下设置，以配置希望 Azure 前端高级版能够私下连接的站点。

    | 设置 | 值 |
    | ------- | ----- |
    | 区域 | 选择与源相同或最近的区域。 |
    | 资源类型 | 选择“Microsoft.Web/sites”。 |
    | 资源 | 选择“myPrivateLinkService”。 |
    | 目标子资源 | sites |
    | 请求消息 | 自定义消息或选择默认值。 |

1. 然后选择 " **添加** " 以保存配置。

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>从 Web 应用批准 Azure 前门高级专用终结点连接

1. 在上一节中转到 "为你配置专用链接" 的 Web 应用。 选择 "**设置**" 下的 "**网络**"。

1. 在“网络”中，选择“配置专用终结点连接” 。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Web 应用中的网络设置的屏幕截图。":::

1. 选择 " *挂起* 的专用终结点请求"，然后选择 " **批准**"。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="挂起的专用终结点请求的屏幕截图。":::

1. 批准后，它应类似于下面的屏幕截图。 完全建立连接需要几分钟时间。 你现在可以从 Azure 前门 Premium 访问你的 web 应用。 启用专用终结点后，将禁用从公共 internet 直接访问 Web 应用。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="已批准的终结点请求的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解 [Azure Web 应用的私有链接服务](../../app-service/networking/private-endpoint.md)。
