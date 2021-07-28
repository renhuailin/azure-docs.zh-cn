---
title: 在 Microsoft AppSource（Azure 市场）上配置 Power BI 应用产品/服务列表详细信息
description: 在 Microsoft AppSource（Azure 市场）上配置 Power BI 应用产品/服务列表详细信息。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: bc827cb42246129a507ee0894251b65d5c53a89c
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631540"
---
# <a name="configure-power-bi-app-offer-listing-details"></a>配置 Power BI 应用产品/服务列表详细信息

此页可让你定义将出现在 Microsoft AppSource 上的产品/服务详细信息，例如产品/服务的名称、说明、链接、联系人、徽标和屏幕截图。

> [!NOTE]
> 仅可用一种语言提供产品/服务列表的详细信息。 只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”，那么就不要求用英语。 提供“实用链接 URL”来提供产品/服务列表内容中使用的语言以外的语言内容，也是可以接受的。

以下示例说明了产品/服务信息在 Microsoft AppSource 中的显示方式（列出的所有价格仅用于举例目的，而不代表实际成本）：

:::image type="content" source="media/power-bi/example-power-bi-app.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

##### <a name="call-out-descriptions"></a>调用说明

1. 徽标
1. 产品
1. 类别
1. 行业
1. 支持地址（链接）
1. 使用条款
1. 隐私策略
1. 产品名称
1. 搜索结果摘要
1. 说明
1. 屏幕截图/视频

## <a name="marketplace-details"></a>市场详细信息

在此处输入的“名称”作为产品/服务的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的名称，但可更改此值。 名称：

- 可以包含商标和版权符号。
- 不得超过 50 个字符。
- 不得包含表情符号。

提供要在“搜索结果摘要”中使用的产品/服务简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

使用 HTML 标记设置说明的格式，以使其更具吸引力。 有关允许使用的标记列表，请参阅[受支持的 HTML 标记](supported-html-tags.md)。

#### <a name="helpprivacy-urls"></a>帮助/隐私 URL

输入“应用的帮助链接”(URL)，客户可通过该链接详细了解该产品/服务。 帮助 URL 不能与支持 URL 相同。

在组织的隐私策略中输入“隐私策略链接”(URL)。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="contact-information"></a>联系信息

提供“支持联系人”和“工程联系人”的姓名、电子邮箱和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

在“支持联系人”部分中的“支持 URL”内，提供 CSP 合作伙伴可以在其中找到你产品/服务的支持的 URL。 支持 URL 不能与帮助 URL 相同。

## <a name="supporting-documents"></a>支持性文档

在此处提供最多三个相关的 PDF 市场营销文档，如白皮书、小册子、核对清单或演示文稿（可选）。

## <a name="marketplace-media"></a>市场媒体

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊图像会导致提交遭到拒绝。

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

最多添加四个演示产品/服务的可选视频。 这些视频应在外部视频服务上进行托管。 输入每个视频的名称、Web 地址和视频的缩略图 PNG 图像（为 1280 x 720 像素）。

有关其他市场列表资源，请参阅[市场产品/服务列表最佳做法](gtm-offer-listing-best-practices.md)。

选择“保存草稿”，然后继续转到左侧导航菜单中的下一个选项卡——“属性” 。

## <a name="next-steps"></a>后续步骤

- [设置产品/服务技术配置](power-bi-app-technical-configuration.md)
