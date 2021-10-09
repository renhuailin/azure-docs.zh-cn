---
title: 通过在 Azure Front Door 标准版/高级版（预览版）中压缩文件来提高性能
description: 了解如何通过在 Azure Front Door 中压缩文件来提高文件传输速度和增加页面加载性能。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: ca9c2e3b4e9873d4880385479b701d36c92238b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750177"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>通过在 Azure Front Door 标准版/高级版（预览版）中压缩文件来提高性能

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

文件压缩是提高文件传输速度和增加页面加载性能的有效方法。 压缩会缩减文件的大小，然后再由服务器发送文件。 文件压缩可以减少带宽成本，并为用户提供更好的体验。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
有两种方法可启用文件压缩：

- 在源服务器上启用压缩。 Azure Front Door 传递压缩文件，将其交付给发出请求的客户端。
- 直接在 Azure Front Door POP 服务器上启用压缩（“快速压缩”）。 在这种情况下，Azure Front Door 会压缩文件并将其发送到最终用户。

> [!NOTE]
> 范围请求可以压缩成不同的大小。 Azure Front Door 要求任何 GET HTTP 请求的 content-length 值都相同。 如果客户端发送带有 `accept-encoding` 标头的字节范围请求导致源以不同的内容长度响应，则 Azure Front Door 将返回 503 错误。 可以在源/Azure Front Door 上禁用压缩，也可以创建规则集规则以从字节范围请求中删除 `accept-encoding`。

> [!IMPORTANT]
> Azure Front Door 配置变更最多需要 10 分钟才能传播到整个网络。 如果首次为 CDN 终结点设置压缩，请考虑等待 1-2 个小时，确保将压缩设置传播到所有 POP 之后再排查问题。

## <a name="enabling-compression"></a>启用压缩

> [!Note]
> 在 Azure Front Door 中，压缩是路由中“启用缓存”的一部分。 只有启用缓存后，才能利用 Azure Front Door 中的压缩功能。

可通过以下方式启用压缩：
* 快速创建期间 - 启用缓存时，可以启用压缩。
* 自定义创建期间 - 添加路由时启用缓存和压缩。 
* 在 Endpoint Manager 路由中。
* 在“优化”页面上。

### <a name="enable-compression-in-endpoint-manager"></a>在 Endpoint Manager 中启用压缩

1. 在“Azure Front Door 标准版/高级版”配置文件页面上，转到 Endpoint Manager，然后选择要启用压缩的终结点。

1. 选择“编辑终结点”，然后选择要启用压缩的路由 。 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Endpoint Manager 登录页面的屏幕截图。" lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. 确保选中“启用缓存”，然后选中“启用压缩”复选框 。

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="在 Endpoint Manager 中启用压缩。":::   

1. 选择“更新”以保存配置。

### <a name="enable-compression-in-optimization"></a>在“优化”中启用压缩

1. 在“Azure Front Door 标准版/高级版”配置文件页面上，转到“设置”下的“优化”。 展开终结点，查看路由列表。 

1. 选择禁用了压缩的路由旁的三个点。 然后选择“配置路由”。

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="在“优化”页面上启用压缩的屏幕截图。" lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. 确保选中“启用缓存”，然后选中“启用压缩”复选框 。

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="在 Endpoint Manager 中启用压缩的屏幕截图。"::: 

1. 单击 **Update**。

## <a name="modify-compression-content-type"></a>修改压缩内容类型

可以在“优化”页面上修改 MIME 类型的默认列表。

1. 在“Azure Front Door 标准版/高级版”配置文件页面上，转到“设置”下的“优化”。 然后选择启用了压缩的路由。

1. 选择启用了压缩的路由旁的三个点。 然后选择“查看压缩的文件类型”。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="“优化”页面的屏幕截图。" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. 删除默认格式，或选择“添加”以添加新的内容类型。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="“自定义文件压缩”页面的屏幕截图。"::: 

1. 选择“保存”以更新压缩配置。

## <a name="disabling-compression"></a>禁用压缩

可通过以下方式禁用压缩：
* 在 Endpoint Manager 路由中禁用压缩。
* 在“优化”页面中禁用压缩。

### <a name="disable-compression-in-endpoint-manager"></a>在 Endpoint Manager 中禁用压缩

1. 在“Azure Front Door 标准版/高级版”配置文件页面上，转到“设置”下的 Endpoint Manager。 选择要禁用压缩的终结点。

1. 选择“编辑终结点”，然后选择要禁用压缩的路由 。 取消选中“启用压缩”框。

1. 选择“更新”以保存配置。

### <a name="disable-compression-in-optimizations"></a>在“优化”中禁用压缩

1. 在“Azure Front Door 标准版/高级版”配置文件页面上，转到“设置”下的“优化”。 然后选择启用了压缩的路由。

1. 选择启用了压缩的路由旁的三个点，然后选择“配置路由” 。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="在“优化”页面中禁用压缩的屏幕截图。"::: 

1. 取消选中“启用压缩”框。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="用于禁用压缩的“更新路由”页面的屏幕截图。"::: 

1. 选择“更新”以保存配置。

## <a name="compression-rules"></a>压缩规则

在 Azure Front Door 中，仅压缩符合条件的文件。 要符合压缩条件，文件必须：
* 属于 MIME 类型 
* 大于 1 KB
* 小于 8 MB

这些配置文件支持以下压缩编码：
* gzip (GNU zip)
* brotli 

如果请求支持多个压缩类型，那么 brotli 压缩优先。

如果对资产的请求指定了 gzip 压缩并且请求导致缓存失误，则 Azure Front Door 将直接在 POP 服务器上对资产执行 gzip 压缩。 此后，将从缓存提供压缩的文件。

如果源使用分块传输编码 (CTE) 将压缩的数据发送到 Azure Front Door POP，则不支持大于 8 MB 的响应大小。 

## <a name="next-steps"></a>后续步骤

- 了解如何配置你的第一个[规则集](how-to-configure-rule-set.md)
- 详细了解[规则集匹配条件](concept-rule-set-match-conditions.md)
- 详细了解 [Azure Front Door 规则集](concept-rule-set.md)
