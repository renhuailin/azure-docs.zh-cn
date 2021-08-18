---
title: 在 Azure 市场上配置虚拟机产品/服务一览详细信息
description: 在 Azure 市场上配置虚拟机产品/服务列表详细信息。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/20/2020
ms.openlocfilehash: 32214b9552e7879d5e84012b0e65886ea92187b3
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601153"
---
# <a name="configure-virtual-machine-offer-listing-details"></a>配置虚拟机产品/服务列表详细信息

在“产品/服务一览”页（从合作伙伴中心内的左侧导航菜单中选择）上，定义产品/服务详细信息，如产品/服务名称、说明、链接和联系人。

> [!NOTE]
> 只要产品/服务说明以“此应用程序只提供 \<non-English language> 版”短语开头，产品/服务一览内容（如说明、文档、屏幕截图和使用条款）不必是英语的。 也可以提供一个 Web 地址，以链接到不使用产品/服务一览内容中所用语言提供内容的站点。

## <a name="marketplace-details"></a>市场详细信息

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段自动填充你在创建产品/服务时在“产品/服务别名”框中输入的名称。 名称：

- 可以包含商标和版权符号。
- 不得超过 50 个字符。
- 不得包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供要在 Azure 市场搜索结果中显示的产品/服务的简短说明。 最多可包含 100 个字符。

### <a name="short-description"></a>简短说明

提供要在 Azure 市场搜索结果中显示的产品/服务的详细说明。 最多可包含 256 个字符。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

使用 HTML 标记设置说明的格式，以使其更具吸引力。 有关允许使用的标记列表，请参阅[受支持的 HTML 标记](supported-html-tags.md)。

### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 Web 地址 (URL)。 确保你的产品/服务符合隐私法律法规。 还必须在网站上发布有效的隐私策略。

## <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 若要添加链接，请选择“添加链接”，然后填写以下字段：

- **Name**：客户将在详细信息页上看到该名称。
- 链接：输入可供客户查看联机文档的 Web 地址。

## <a name="customer-support-links"></a>客户支持链接

提供支持网站，客户可在其中联系支持团队。

- Azure 全球支持网站
- Azure 政府支持网站

## <a name="partner-support-contact"></a>合作伙伴支持联系人

提供当客户在开立支持票证时供 Microsoft 合作伙伴使用的“用户名”、“电子邮件地址”和“电话号码”。 Azure 市场中未列出此信息。

## <a name="engineering-contact"></a>工程联系人

提供当你的产品/服务出现问题（包括认证问题）时供 Microsoft 使用的“用户名”、“电子邮件地址”和“电话号码”。 Azure 市场中未列出此信息。

## <a name="cloud-solution-provider-program-contact"></a>云解决方案提供商计划联系人

提供负责处理 CSP 计划的人员（若有）的“用户名”、“电子邮件地址”和“电话号码”。 Azure 市场中未列出此信息。 对于“CSP 计划营销资料”，请输入可供此计划中的合作伙伴查看营销资料的 Web 地址。

## <a name="marketplace-media"></a>市场媒体

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊图像会导致提交遭到拒绝。

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络未阻止合作伙伴中心所使用的 https://upload.xboxlive.com 服务。

### <a name="logos"></a>徽标

提供大徽标的 PNG 文件。 合作伙伴中心将使用此文件来创建小徽标和中等徽标。 日后可以视需要选择将这些徽标替换为其他图像。

- 大（216 x 216 到 350 x 350 像素，必需）
- 中等（90 x 90 像素，可选）
- 小（48 x 48 像素，可选）

这些徽标会在列表的不同位置上使用：

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>屏幕截图

最多添加五个显示了产品/服务的工作方式的屏幕截图。 每个屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。 每个屏幕截图都必须包含一个标题。

### <a name="videos"></a>视频

最多添加五个视频来演示产品/服务。 这些视频应在外部视频服务上进行托管。 输入每个视频的名称、Web 地址和视频的缩略图 PNG 图像（为 1280 x 720 像素）。

有关其他市场列表资源，请参阅[市场产品/服务列表最佳做法](gtm-offer-listing-best-practices.md)。

选择“保存草稿”，然后继续转到左侧导航菜单中的下一个选项卡“预览受众”。

## <a name="next-steps"></a>后续步骤

- [创建预览受众](azure-vm-create-preview.md)
