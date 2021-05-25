---
title: Azure Front Door 标准版/高级版中的缓存清除（预览版）
description: 本文介绍如何清除 Azure Front Door 标准版/高级版中的缓存。
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098648"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Azure Front Door 标准版/高级版中的缓存清除（预览版）

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 标准版/高级版会缓存资产，直到资产的生存时间 (TTL) 到期为止。 每当客户端请求的资产的 TTL 已过期时，Azure Front Door 环境会检索新的更新后资产副本以提供请求，然后存储已刷新的缓存。

确保用户始终获取资产的最新副本是最佳做法。 方法如下：针对每次更新将资产版本化，并将其发布为新 URL。 Azure Front Door 标准版/高级版将立即检索用于下一个客户端请求的新资产。 有时候可能希望从所有边缘节点清除缓存的内容，并强制其全部检索新的已更新资产。 清除缓存内容的原因如下：更新了应用程序，或者想要更新包含错误信息的资产。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

查看 [Azure Front Door 缓存](concept-caching.md)了解缓存的工作原理。

## <a name="configure-cache-purge"></a>配置缓存清除

1. 转到包含要清除的资产的 Azure Front Door 配置文件的概述页，然后选择“清除缓存”。

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="屏幕截图显示了概述页面上的缓存清除。":::

1. 选择要从边缘节点清除的终结点和域。 *（可以选择多个域）*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="屏幕截图显示了缓存清除页面。":::

1. 要清除所有资产，请选择“清除选定域的所有资产”。 否则，在“路径”中，输入要清除的每个资产的路径。

要清除的路径列表中支持以下格式：

* **单一路径清除**：通过指定资产的完整路径（不带协议和域）并包含文件扩展名（例如 /pictures/strasbourg.png）来清除单个资产。
* **根域清除**：清除路径中具有“/*”的终结点的根。

Azure Front Door 标准版/高级版的缓存清除不区分大小写。 此外，它们不区分查询字符串，这表示清除 URL 时将一并清除其查询字符串的所有变体。 

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door 标准版/高级版](create-front-door-portal.md)。
