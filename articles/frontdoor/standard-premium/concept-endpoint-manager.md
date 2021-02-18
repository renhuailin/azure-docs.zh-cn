---
title: Azure 前门：终结点管理器
description: 本文概述了 Azure 前门终结点管理器。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098757"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>什么是 Azure 前门标准/高级 (预览版) 终结点管理器？

> [!NOTE]
> * 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [Azure 前门文档](../front-door-overview.md)。

终结点管理器概述了已为 Azure 前门配置的终结点。 终结点是域及其相关配置的逻辑分组。 终结点管理器可帮助你管理用于 CRUD (创建、读取、更新和删除) 操作的终结点集合。 可以通过终结点管理器管理终结点的以下元素：

* 域
* 源组
* 路由
* 安全性

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="不带配置的端点管理器的屏幕截图。" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

终结点管理器列出了每个元素在终结点中创建的实例数。 还将显示每个元素的关联状态。 例如，可以创建多个域和源组，并将它们之间的关联分配给不同的路由。

> [!IMPORTANT]
> * Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="linked-view"></a>链接视图

通过终结点管理器中的链接视图，你可以轻松地识别 Azure 前门元素之间的关联，如：

* 哪些域与当前终结点相关联？
* 哪个源组与哪个域相关联？
* 哪个 WAF 策略与哪个域相关联？

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="具有配置的终结点管理器的屏幕截图。" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
