---
title: 在 Microsoft AppSource 上配置 Azure 容器产品/服务列表的详细信息
description: 在 Microsoft AppSource 上配置 Azure 容器产品/服务列表的详细信息。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0b9b96c4cbc2484597e51463a6b7a4ecfaf9e950
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892311"
---
# <a name="configure-azure-container-offer-listing-details"></a>配置 Azure 容器产品/服务列表的详细信息

通过此页面可以定义产品/服务的详细信息，例如产品/服务的名称、描述、链接、联系人、徽标和屏幕截图。

> [!NOTE]
> 仅可用一种语言提供产品/服务列表的详细信息。 只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”，那么就不要求用英语。 提供“实用链接 URL”来提供产品/服务列表内容中使用的语言以外的语言内容，也是可以接受的。

## <a name="marketplace-details"></a>市场详细信息

在此处输入的“名称”作为产品/服务的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的名称，但可更改此值。 名称：

- 可以包含商标和版权符号。
- 不得超过 50 个字符。
- 不得包含表情符号。

提供要在“搜索结果摘要”中使用的产品/服务简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

提供对产品/服务的简短说明（最多 256 个字符）。 此内容将显示在搜索结果和产品/服务的详细信息页上。

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

使用 HTML 标记设置说明的格式，以使其更具吸引力。 有关允许的标记的列表，请参阅[受支持的 HTML 标记](supported-html-tags.md)。

输入组织的隐私策略的 Web 地址 (URL)。 确保你的产品/服务符合隐私法律法规。 还必须在网站上发布有效的隐私策略。

## <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 最多可以添加 25 个链接。 若要添加链接，请选择“+ 添加链接”，然后填写以下字段：

- **名称** – 客户将在产品/服务详细信息页上看到此名称。
- **链接** (URL) - 输入链接，以供客户查看联机文档。 链接必须以 http:// 或 https:// 开头。

### <a name="contact-information"></a>联系信息

提供支持联系人、工程联系人和云解决方案提供商计划联系人的姓名、电子邮件和电话号码  。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

在“支持联系人”部分中，提供支持网站，Azure Global 和 Azure 政府（如果适用）的客户可以通过该网站联系你的支持团队 。

## <a name="marketplace-media"></a>市场媒体

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊图像会导致提交遭到拒绝。

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络未阻止合作伙伴中心所使用的 https://upload.xboxlive.com 服务。

### <a name="logos"></a>徽标

提供大徽标的 PNG 文件。 合作伙伴中心将使用它来创建其他所需的大小。 稍后，可以选择将其替换为其他图像。

这些徽标会在列表的不同位置上使用：

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>屏幕截图

添加至少一张（最多五张）显示产品/服务的工作方式的屏幕截图。 所有屏幕截图都必须为 1280 x 720 像素，并且为 PNG 格式。 为每张屏幕截图添加标题。

### <a name="videos"></a>视频

最多添加五个演示产品/服务的可选视频。 这些视频应在外部视频服务上进行托管。 输入每个视频的名称、Web 地址和视频的缩略图 PNG 图像（为 1280 x 720 像素）。

有关其他市场列表资源，请参阅[市场产品/服务列表最佳做法](gtm-offer-listing-best-practices.md)。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“预览版受众” 。
<!-- #### Offer examples

The following examples show how the offer listing fields appear in different places of the offer.

This shows search results in Azure Marketplace:

[![Illustrates the search results in Azure Marketplace](media/azure-container/azure-create-7-search-results-mkt-plc-small.png)](media/azure-container/azure-create-7-search-results-mkt-plc.png#lightbox)

This shows the **Offer listing** page in Azure portal:

:::image type="content" source="media/azure-container/azure-create-8-offer-listing-portal.png" alt-text="Illustrates the Offer listing page in Azure portal.":::

This shows search results in Azure portal:

[![Illustrates the search results in Azure portal.](media/azure-container/azure-create-9-search-results-portal-small.png)](media/azure-container/azure-create-9-search-results-portal.png#lightbox) -->

## <a name="next-steps"></a>后续步骤

- [设置产品/服务预览版受众](azure-container-preview-audience.md)
