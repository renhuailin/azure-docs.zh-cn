---
title: Azure Front Door：Endpoint Manager
description: 本文概述了 Azure Front Door Endpoint Manager。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098757"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>什么是 Azure Front Door 标准版/高级版（预览版）Endpoint Manager？

> [!NOTE]
> * 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看 [Azure Front Door 文档](../front-door-overview.md)。

Endpoint Manager 概述了已为 Azure Front Door 配置的终结点。 终结点是域及其相关配置的逻辑分组。 Endpoint Manager 可帮助你管理用于 CRUD（创建、读取、更新和删除）操作的终结点集合。 可通过 Endpoint Manager 管理终结点的以下元素：

* 域
* 源组
* 路由
* 安全

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="没有配置的 Endpoint Manager 的屏幕截图。" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Endpoint Manager 列出了一个终结点内创建每个元素的实例数。 还将显示每个元素的关联状态。 例如，可创建多个域和源组，并使用不同的路由来分配它们之间的关联。

> [!IMPORTANT]
> * Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="linked-view"></a>链接视图

通过 Endpoint Manager 中的链接视图，可轻松地识别 Azure Front Door 元素之间的关联，例如：

* 哪些域与当前终结点相关联？
* 哪个源组与哪个域相关联？
* 哪个 WAF 策略与哪个域相关联？

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="具有配置的 Endpoint Manager 的屏幕截图。" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door 标准版/高级版](create-front-door-portal.md)。
