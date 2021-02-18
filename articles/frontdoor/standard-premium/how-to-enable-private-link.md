---
title: 通过专用链接将 Azure 前门高级版连接到源
description: 了解如何使用 Azure 门户将 Azure 前门高级版连接到使用专用链接服务的源。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098456"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>通过专用链接将 Azure 前门高级版连接到源

本文将指导你完成如何配置 Azure 前门 Premium SKU，以使用 Azure 专用链接服务连接到虚拟网络中托管的应用程序。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 为源 web 服务器创建 [专用链接](../../private-link/create-private-link-service-portal.md) 服务。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>启用 Azure 前门服务中的专用终结点

在本部分中，会将 Azure 专用链接服务映射到在 Azure 前门高级 SKU 的专用网络中创建的专用终结点。 

1. 在 Azure 前门 Premium 配置文件中的 " *设置*" 下，选择 " **源组**"。

1. 选择包含要为其启用专用链接的源的源组。

1. 选择 " **+ 添加原点** " 添加新源，或从列表中选择一个以前创建的源。 然后选中相应的复选框以 **启用专用链接服务**。

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="在 &quot;添加源&quot; 页中启用专用链接的屏幕截图。":::

1. 对于 " **选择 Azure 资源**"，请 **在 "我的目录" 中** 选择。 选择或输入以下设置，以配置希望 Azure 前端高级版能够私下连接的资源。
    
    | 设置 | 值 |
    | ------- | ----- |
    | 区域 | 选择与源相同或最近的区域。 |
    | 资源类型 | 选择“Microsoft.Network/privateLinkServices”。 |
    | 资源 | 选择“myPrivateLinkService”。 |
    | 目标子资源 | 请将此字段留空。 |
    | 请求消息 | 自定义消息或选择默认消息。 |

## <a name="next-steps"></a>后续步骤

了解 [Azure 前门高级版专用链接](concept-private-link.md)。
