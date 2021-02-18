---
title: 'Azure 前门标准/高级 (预览版中的缓存清除) '
description: 本文将帮助你了解如何清除 Azure 前门标准/高级版上的缓存。
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098648"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Azure 前门标准/高级 (预览版中的缓存清除) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门标准/高级版将在资产的生存时间 (TTL) 过期之前缓存资产。 只要客户端请求的资产的 TTL 已过期，Azure 前门环境就会检索新的资产更新副本来处理请求，然后存储已刷新的缓存。

最佳做法是确保用户始终获得最新的资产副本。 执行此操作的方法是为每个更新版本资产，并将其发布为新的 Url。 Azure 前门标准/高级版会立即为下一个客户端请求检索新资产。 有时，你可能想要从所有边缘节点中清除缓存的内容，并强制他们全部检索新的更新的资产。 要清除缓存内容的原因是，您已经对应用程序进行了新的更新，或者您想要更新包含错误信息的资产。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

查看 [Azure 前门缓存](concept-caching.md) ，了解缓存的工作方式。

## <a name="configure-cache-purge"></a>配置缓存清除

1. 在 Azure 前门配置文件的 "概述" 页上，选择要清除的资产，然后选择 " **清除缓存**"。

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="概述页上的缓存清除屏幕截图。":::

1. 选择要从边缘节点中清除的终结点和域。 *(可以选择多个域)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="缓存清除页的屏幕截图。":::

1. 若要清除所有资产，请选择 **"清除所选域的所有资产"**。 否则，在 " **路径**" 中，输入要清除的每个资产的路径。

要清除的路径列表支持这些格式：

* **单路径清除**：通过指定资产 (的完整路径（不含协议和域) ，使用文件扩展名）清除单个资产，例如，/pictures/strasbourg.png。
* **根域清除**：在路径中清除 "/*" 终结点的根。

Azure 前门标准/Preium 上的缓存清除不区分大小写。 此外，它们是不可知的查询字符串，这意味着清除 URL 将清除其所有查询字符串变体。 

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
