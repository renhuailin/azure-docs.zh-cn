---
title: 使用专用链接将 Azure Front Door Premium 连接到存储帐户源
titleSuffix: Azure Private Link
description: 了解如何将 Azure Front Door Premium 专门连接到存储帐户。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201662"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>使用专用链接将 Azure Front Door Premium 连接到存储帐户源

本文将指导你完成对 Azure Front Door Premium SKU 的配置，以使用 Azure 专用链接服务专门连接到存储帐户源。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="enable-private-link-to-a-storage-account"></a>启用到存储帐户的专用链接
 
在本节中，你将把专用链接服务映射到在 Azure Front Door 专用网络中创建的专用终结点。 

1. 在 Azure Front Door Premium 配置文件中的“设置”下，选择“源组”。

1. 选择包含要为其启用专用链接的存储帐户源的源组。

1. 选择“+ 添加源”，以添加新的存储帐户源，或从该列表中选择以前创建的存储帐户源。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="启用存储帐户专用链接的屏幕截图。":::

1. 对于“选择 Azure 资源”，选择“在我的目录中”。 选择或输入以下设置，以配置希望 Azure Front Door Premium 进行专用连接的站点。

    | 设置 | 值 |
    | ------- | ----- |
    | 区域 | 选择与源相同或最近的区域。 |
    | 资源类型 | 选择“Microsoft.Storage/storageAccounts”。 |
    | 资源 | 选择存储帐户。 |
    | 目标子资源 | 可以选择“blob”或“Web”。 |
    | 请求消息 | 自定义消息或选择默认消息。 |

1. 然后选择“添加”以保存配置。

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>从存储帐户批准专用终结点连接

1. 转到你在上一节中为其配置专用链接的存储账户。 在“设置”下选择“网络” 。

1. 在“网络”中，选择“专用终结点连接” 。 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Web 应用中网络设置的屏幕截图。":::

1. 选择 Azure Front Door Premium 中的“挂起”专用终结点请求，然后选择“批准”。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="挂起存储专用终结点请求的屏幕截图。":::

1. 批准后应类似于下面的屏幕截图。 完全建立连接需要几分钟时间。 现在可以从 Azure Front Door Premium 访问存储帐户。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="已批准的存储终结点请求的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解有关[存储帐户的专用链接服务](../../storage/common/storage-private-endpoints.md)。
