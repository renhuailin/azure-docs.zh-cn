---
title: 在 Microsoft 合作伙伴中心配置托管服务产品/服务列表详细信息
description: 在 Azure 市场配置托管服务产品/服务列表详细信息。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/12/2021
ms.openlocfilehash: 1b2171c300fd52403da29d23f9f34d3f94961b3d
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204492"
---
# <a name="configure-managed-service-offer-listing-details"></a>配置托管服务产品/服务列表详细信息

你在合作伙伴中心的“产品/服务列表”页面上提供的信息将显示在 Azure 市场中。 这包括你的产品/服务名称、说明、媒体和其他营销资产。

> [!NOTE]
> 如果产品/服务的语言不是英语，则产品/服务列表可以采用英语，但说明必须以英语短语“This service is available in &lt;产品/服务内容的语言>”开头或结尾。 还可以使用与产品/服务列表详细信息中所用语言不同的语言提供支持文档。

在合作伙伴中心的“产品/服务列表”页上，提供下述信息。 若要详细了解托管服务产品/服务的列表详细信息，请参阅[计划托管服务产品/服务](./plan-managed-service-offer.md)。

## <a name="marketplace-details"></a>市场详细信息

1. “名称”框预先填充了之前在“新建产品/服务”对话框中输入的名称，但你随时可以更改它。 此名称将作为产品/服务列表的标题显示在在线商店中。
2. 在“搜索结果摘要”框中，用不多于 100 个字符的文字说明产品/服务的用途或目标。
3. 在“简短说明”字段中，提供对产品/服务的简短说明（最多 256 个字符）。 它将显示在 Azure 门户的产品/服务列表中。
4. 在“说明”字段中，描述你的托管服务产品/服务。 此框中最多可以输入 2,000 个字符的文本（包括 HTML 标记和空格）。 有关 HTML 格式设置的信息，请参阅[产品/服务说明中支持的 HTML 标记](./supported-html-tags.md)。
5. 在“隐私策略链接”框中，输入组织的隐私策略的链接（以 https 开头）。 你负责确保你的产品/服务符合隐私法律和法规，并负责提供有效的隐私策略。

## <a name="product-information-links"></a>产品信息链接

你可以选择提供有关你的解决方案的补充联机文档：

1. 选择“添加链接”。
2. 提供每个文档的名称和 Web 地址（以 https 开头）。

## <a name="contact-information"></a>联系信息

输入公司中的两个人的姓名、电子邮件地址和电话号码（你可以是其中一个人）：支持联系人和工程联系人。 我们将使用此信息与你沟通有关产品/服务的事项。 此信息不会向客户显示，但可能会提供给云解决方案提供商 (CSP) 合作伙伴。

## <a name="support-link"></a>支持链接

如果你有面向 Azure 全球客户和/或 Azure 政府客户的支持网站，请输入其 URL（以 https 开头）。

## <a name="marketplace-media"></a>市场媒体

> [!NOTE]
> 如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

### <a name="add-logos"></a>添加徽标

在“徽标”下，上传像素在 216 x 216 到 350 x 350 之间且格式为 .PNG 的大徽标。 合作伙伴中心会自动创建中徽标和小徽标，你以后可以替换它们。

- 大徽标（216 x 216 至 350 x 350 像素）显示在 Azure 市场上的产品/服务列表中。
- 中徽标（90 x 90 像素）在创建新资源时显示。
- 小徽标（48 x 48 像素）用在 Azure 市场搜索结果中。

### <a name="add-screenshots-optional"></a>添加屏幕截图（可选）

添加最多五个图像来展示你的产品/服务。 所有图像都必须为 1280 x 720 像素大小并采用 .PNG 格式。

1. 在“屏幕截图”下，将 PNG 文件拖放至“屏幕截图”框。
2. 选择“添加图像标题”。
3. 在显示的对话框中，输入一个标题。
4. 重复步骤 1 至步骤 3 以添加其他屏幕截图。

### <a name="add-videos-optional"></a>添加视频（可选）

你可以添加用于演示产品/服务的 YouTube 或 Vimeo 视频的链接。 这些视频与产品/服务一起呈现给客户。 必须以 .PNG 格式输入视频的缩略图，其大小为 1280 x 720 像素。 可以为每个产品/服务添加最多 5 个视频。

1. 在“视频”下，选择“添加视频链接”。
2. 在显示的框中，输入视频的名称和链接。
3. 将 .PNG 文件（1280 x 720 像素）拖放至灰色的“缩略图”框。
4. 若要添加其他视频，请重复步骤 1 至 3。

选择“保存草稿”，然后转到下一选项卡：“预览版受众”。

## <a name="next-steps"></a>后续步骤

- [添加预览受众](create-managed-service-offer-preview.md)
