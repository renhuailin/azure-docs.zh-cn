---
title: '通过在 Azure 前门标准/高级 (预览版中压缩文件来提高性能) '
description: 了解如何通过在 Azure 前门中压缩文件来提高文件传输速度和增加页面加载性能。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098492"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>通过在 Azure 前门标准/高级 (预览版中压缩文件来提高性能) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

文件压缩是提高文件传输速度和增加页面加载性能的一种有效方法。 压缩将在服务器发送文件之前减少文件的大小。 文件压缩可降低带宽成本，并为用户提供更好的体验。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
有两种方法可启用文件压缩：

- 正在源服务器上启用压缩。 Azure 前门会传递压缩文件，并将它们传递给请求它们的客户端。
- 直接在 Azure 前门 POP 服务器上启用压缩 (对 *动态*) 进行压缩。 在这种情况下，Azure 前门会压缩文件并将其发送给最终用户。

> [!IMPORTANT]
> Azure 前门配置更改最多需要10分钟才能传播到整个网络。 如果你是首次为 CDN 终结点设置压缩，请考虑在进行故障排除前等待1-2 小时，以确保压缩设置已传播到所有 Pop。

## <a name="enabling-compression"></a>启用压缩

> [!Note]
> 在 Azure 前门中，压缩是在路由中 **启用缓存** 的一部分。 仅当 **启用缓存** 时，才能利用 Azure 前门中的压缩功能。

可以通过以下方式启用压缩：
* 在 "快速创建" 期间，启用缓存后，可以启用压缩。
* 在自定义创建-在添加路由时启用缓存和压缩。 
* 在终结点管理器路由中。
* 在 "优化" 页上。

### <a name="enable-compression-in-endpoint-manager"></a>在终结点管理器中启用压缩

1. 在 "Azure 前门标准/高级配置文件" 页上，中转到 **终结点管理器** ，并选择要启用压缩的终结点。

1. 选择 " **编辑终结点**"，然后选择要启用压缩的 **路由** 。 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="终结点管理器登录页的屏幕截图。" lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. 确保选中 " **启用缓存** "，然后选中 " **启用压缩**" 复选框。

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="在终结点管理器中启用压缩。":::   

1. 选择 " **更新** " 以保存配置。

### <a name="enable-compression-in-optimization"></a>启用优化压缩

1. 在 Azure 前门标准/高级配置文件页上，在 "设置" 下中转到 " **优化** "。 展开终结点以查看路由列表。 

1. 选择已 *禁用* 压缩的 **路由** 旁边的三个点。 然后选择 " **配置路由**"。

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="&quot;优化&quot; 页上的 &quot;启用压缩&quot; 屏幕。" lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. 确保选中 " **启用缓存** "，然后选中 " **启用压缩**" 复选框。

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="在终结点管理器中启用压缩的屏幕截图。"::: 

1. 单击“更新”。

## <a name="modify-compression-content-type"></a>修改压缩内容类型

您可以在 "优化" 页上修改 MIME 类型的默认列表。

1. 在 Azure 前门标准/高级配置文件页上，在 "设置" 下中转到 " **优化** "。 然后选择已 *启用* 压缩的 **路由**。

1. 选择已 *启用* 压缩的 **路由** 旁边的三个点。 然后选择 " **查看压缩文件类型**"。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="&quot;优化&quot; 页的屏幕截图。" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. 删除默认格式，或者选择 " **添加** " 以添加新内容类型。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="自定义文件压缩页的屏幕截图。"::: 

1. 选择 " **保存**" 以更新压缩配置。

## <a name="disabling-compression"></a>禁用压缩

可以通过以下方式禁用压缩：
* 禁用终结点管理器路由中的压缩。
* 禁用优化页中的压缩。

### <a name="disable-compression-in-endpoint-manager"></a>在终结点管理器中禁用压缩

1. 在 Azure 前门标准/高级配置文件页上，在 "设置" 下中转到 **终结点管理器** 。 选择要禁用压缩的终结点。

1. 选择 " **编辑终结点** "，然后选择要禁用压缩的 **路由** 。 取消选中 " **启用压缩** " 框。

1. 选择 " **更新** " 以保存配置。

### <a name="disable-compression-in-optimizations"></a>禁用优化中的压缩

1. 在 Azure 前门标准/高级配置文件页上，在 "设置" 下中转到 " **优化** "。 然后选择已 *启用* 压缩的 **路由**。

1. 选择已 *启用* 压缩的 **路由** 旁边的三个点，然后选择 "*配置路由*"。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="禁用优化页中的压缩的屏幕截图。"::: 

1. 取消选中 " **启用压缩** " 框。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="用于禁用压缩的更新路由页的屏幕截图。"::: 

1. 选择 " **更新** " 以保存配置。

## <a name="compression-rules"></a>压缩规则

在 Azure 前门中，只有符合条件的文件才会被压缩。 要符合压缩条件，文件必须：
* 是 MIME 类型 
* 大于 1 KB
* 小于 8 MB

这些配置文件支持以下压缩编码：
* gzip (GNU zip)
* brotli 

如果请求支持多个压缩类型，那么 brotli 压缩优先。

当资产的请求指定 gzip 压缩并且请求导致缓存未命中时，Azure 前门会直接在 POP 服务器上对资产进行 gzip 压缩。 此后，将从缓存提供压缩的文件。

如果源使用分块传输编码 (CTE) 将压缩的数据发送到 Azure 前门 POP，则不支持大于 8 MB 的响应大小。 

## <a name="next-steps"></a>后续步骤

- 了解如何配置你的第一个 [规则集](how-to-configure-rule-set.md)
- 了解有关[规则集匹配条件](concept-rule-set-match-conditions.md)的详细信息
- 详细了解 [Azure 前门规则集](concept-rule-set.md)
