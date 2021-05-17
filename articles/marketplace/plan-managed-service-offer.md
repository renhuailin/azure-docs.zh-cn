---
title: 为 Microsoft 商业市场计划托管服务产品/服务
description: 如何使用 Microsoft 合作伙伴中心的商业市场计划为 Azure 市场计划新的托管服务产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: ffa7e35bed1713a5ef9e04b59a8dbdb692b9630f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790948"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>如何为 Microsoft 商业市场计划托管服务产品/服务

本文介绍了通过合作伙伴中心向 Microsoft 商业市场发布托管服务产品/服务的要求。

托管服务是 Azure 市场产品/服务，可通过 Azure Lighthouse 实现跨租户和多租户管理。 要了解详细信息，请参阅[什么是 Azure Lighthouse？](../lighthouse/overview.md)。 客户在购买托管服务产品/服务时，可以委托一个或多个订阅或资源组。 然后，可以使用 Azure Lighthouse 的 [Azure 委托资源管理](../lighthouse/concepts/architecture.md)功能来处理这些资源。

## <a name="eligibility-requirements"></a>资格要求

要发布托管服务产品/服务，必须在云平台中获得黄金或白银 Microsoft 资质。 此资质向客户展示你的专业知识。 有关详细信息，请参阅 [Microsoft 合作伙伴网络资质](https://partner.microsoft.com/membership/competencies)。

产品/服务必须满足要在 Azure 市场上发布的所有适用的[商业市场认证策略](/legal/marketplace/certification-policies)。

## <a name="customer-leads"></a>潜在顾客

必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的在线商店将发送到你配置的 CRM 系统。 商业市场支持不同类型的 CRM 系统，以及使用 Azure 表或使用 Power Automate 配置 HTTPS 终结点的选项。

你可以在创建产品/服务期间或之后随时添加或修改 CRM 连接。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="legal-contracts"></a>法律协定

在“合作伙伴中心”的“属性”页中，系统将要求你提供产品/服务的使用 **条款和条件**。 你可以直接在“合作伙伴中心”中输入你的条款，或提供可找到这些条款的 URL。 客户必须接受这些条款和条件，才能购买产品/服务。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

在“合作伙伴中心”创建托管服务产品/服务时，将输入文本、图像、文档和其他产品/服务详细信息。 当客户在 Azure 市场中发现你的产品/服务时，可以看到这些内容。 请参阅以下示例：

:::image type="content" source="media/example-managed-service.png" alt-text="说明托管服务产品/服务在 Azure 市场上的显示方式。":::

**调用说明**

1. 徽标
1. 名称
1. 简短说明
1. 类别
1. 法律合同和隐私策略
1. 说明
1. 屏幕截图/视频
1. 有用链接

以下示例演示了产品/服务列表在 Azure 门户中的显示方式：

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="说明此产品/服务在 Azure 门户中如何显示。":::

**调用说明**

1. 名称
2. 说明
3. 有用链接
4. 屏幕截图/视频

> [!NOTE]
> 如果产品/服务的语言不是英语，则产品/服务列表可以采用英语，但说明必须以英语短语“This service is available in &lt;产品/服务内容的语言>”开头或结尾。 还可以使用与产品/服务列表详细信息中所用语言不同的语言提供支持文档。

为了帮助更轻松地创建产品/服务，请提前准备其中一些项。 除非另有说明，否则以下都是必填项。

**名称**：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且长度必须限制为 50 个字符。

**搜索结果摘要**：用 100 个字符或更少字符说明产品/服务的用途或目标。 此摘要用于商业市场列表搜索结果。 它不能与标题完全相同。 请考虑包含热门 SEO 关键字。

**简短说明**：提供对产品/服务的简短说明（最多 256 个字符）。 它将显示在 Azure 门户中的产品/服务列表中。

**说明**：以 3000 个字符或更少字符来描述你的产品/服务。 此说明将显示在商业市场列表中。 考虑包括价值主张、关键优势、类别或行业关联以及任何必要的披露。

下面是有关如何撰写描述的一些提示：

* 在前几句话中清晰描述你的产品/服务的价值，包括：
    * 从该产品/服务中受益的用户类型。
    * 客户需求或该产品/服务解决的问题。
* 请记住，前几句话可能会显示在搜索结果中。
* 使用行业特定的词汇。

可以使用 HTML 标记来设置说明的格式。 有关 HTML 格式设置的信息，请参阅[商业市场产品/服务说明中支持的 HTML 标记](./supported-html-tags.md)。

**隐私策略链接**：提供在网站上托管的隐私策略的 URL。 你负责确保你的产品/服务符合隐私法律和法规，并负责提供有效的隐私策略。

**有用的链接**（可选）：上传有关产品/服务的补充联机文档。

**联系信息**：提供公司两个人的姓名、电子邮件地址和电话号码（你可以是其中一个人）：支持联系人和工程联系人。 我们将使用此信息与你沟通有关产品/服务的信息。 此信息不会向客户显示，但可能会提供给云解决方案提供商 (CSP) 合作伙伴

**支持 URL**（可选）：如果你有面向 Azure 全球客户和/或 Azure 政府客户的支持网站，请提供这些 URL。

**市场媒体 – 徽标**：为你的产品/服务的大徽标提供 PNG 文件。 合作伙伴中心将使用它来创建中徽标和小徽标。 稍后，可以选择将这些徽标替换为其他图像。

* 大徽标（216 x 216 至 350 x 350 像素）出现在 Azure 市场上的产品/服务列表中。
* 创建新资源时，会显示中徽标（90 x 90 像素）。
* 小徽标（48 x 48 像素）在 Azure 市场搜索结果中使用。

请遵守徽标适用的下述准则：

* 确保图像未被拉伸。
* Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
* Azure 门户颜色为白色和黑色。 不要将这两个颜色用作徽标的背景。 建议使用简单的原色来突出你的徽标。
* 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
* 徽标的外观应平整。 避免渐变。 不要在徽标上放置文本，即使是公司或品牌名称也不可以。

**市场媒体 – 屏幕截图**（可选）：最多添加五个图像，用于演示产品/服务的工作方式。 所有图像都必须为 1280 x 720 像素大小并采用 .PNG 格式。

**市场媒体 – 视频**（可选）：最多上传 5 个视频来演示产品/服务。 视频必须托管在 YouTube 或 Vimeo 上，并且有一个缩略图（1280 x 720 PNG 文件）。

## <a name="preview-audience"></a>预览版受众

预览版受众可以在 Azure 市场发布产品/服务之前访问它，以便对其进行测试。 在“合作伙伴中心”的“预览版受众”页上，可以定义有限的预览版受众。

> [!NOTE]
> 预览版受众不同于专用计划。 专用计划仅适用于所选的特定受众。 通过它可以与特定客户协商自定义计划。

你可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动最多添加 10 个电子邮件地址，或使用 .csv 文件最多导入 20 个电子邮件地址。 如果产品/服务已上线，仍然可以定义预览版受众，以测试对产品/服务进行的任何更改或更新。

## <a name="plans-and-pricing"></a>规划和定价

托管服务产品/服务至少需要一个计划。 计划定义解决方案范围、限制和关联定价（如果适用）。 可以为你的产品/服务创建多个计划，从而为你的客户提供不同的技术和定价选项。 有关计划（包括专用计划）的一般指导，请参阅[适用于商业市场产品/服务的计划和定价](plans-pricing.md)。

托管服务仅支持一个定价模型：自带许可 (BYOL)。 这意味着你将直接向客户收费，并且 Microsoft 不会向你收取任何费用。

## <a name="next-steps"></a>后续步骤

* [创建托管服务产品](./create-managed-service-offer.md)
* [套餐列出最佳做法](./gtm-offer-listing-best-practices.md)