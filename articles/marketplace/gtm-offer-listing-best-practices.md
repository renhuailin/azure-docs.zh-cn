---
title: 产品/服务展示最佳做法 - Microsoft 商业市场
description: 了解适用于 Microsoft AppSource 和 Azure 市场产品/服务的上市展示最佳做法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/20/2021
ms.openlocfilehash: a788340b5ad3d35f86e61a1433815a0d7b28f28e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965699"
---
# <a name="offer-listing-best-practices"></a>套餐列出最佳做法

本文提供了有关创建具有吸引力的 Microsoft 商业市场产品/服务的建议。 下表概述了有关在合作伙伴中心填写产品/服务信息的最佳做法。

有关市场营销最佳做法的完整列表，包括通过产品列表吸引流量并提高客户参与度的最佳做法，请参阅商业市场的[市场营销最佳做法指南](https://aka.ms/marketplacebestpracticesguide)。

若要分析你的产品/服务的业绩，请在合作伙伴中心转到[市场见解仪表板](https://go.microsoft.com/fwlink/?linkid=2165936)。

## <a name="online-store-offer-details"></a>在线商店产品/服务详细信息

| 设置 | 最佳做法 |
|:--- |:--- |  
| 产品名称 | 对于应用，提供包括搜索关键字的清晰标题将有助于客户发现你的产品/服务。<br><br>对于咨询服务，请遵循以下格式：[产品/服务名称：[持续时间] [产品/服务类型]（例如，Contoso：2 周实施） |
| 套餐说明 | 在前几句话中清晰地描述你的产品/服务的价值主张。 这些句子可能会在搜索引擎结果中使用。 核心价值主张应包含：<ul><li>产品或解决方案的说明。</li><li>从产品或解决方案中受益的用户角色。</li><li>该产品或解决方案可满足的客户需求或解决的难题。</li></ul><br>尽可能使用行业标准词汇或基于优势的措辞。 不要依赖于特性和功能来销售产品。 而是重点介绍产品/服务提供的价值。<br><br>对于咨询服务商品，明确说明你提供的专业服务。 |
| 产品/服务徽标（PNG 格式，216×216 到 350x350 像素）：应用详细信息页 | 针对数字媒体设计和优化徽标。<br><br>将 PNG 格式的徽标上传到产品/服务的应用详细信息列表页。 合作伙伴中心会将其调整为所需的徽标大小。 |
| 产品/服务徽标（PNG 格式，48×48 像素）：搜索页 | 合作伙伴中心将根据上传的大徽标生成此徽标。 稍后，可以选择将其替换为其他图像。 |
“了解详细信息”文档 | 在“了解详细信息”下包含支持性的销售和市场营销资产，例如：<ul><li>白皮书</li><li>小册子</li><li>checklists</li><li>PowerPoint 演示文稿</li></ul><br>以 PDF 格式保存所有文件。 此处的目标应该是教育客户，而不是推销。<br><br>将应用登陆页的链接添加到所有文档，并添加 URL 参数来帮助跟踪访问量和试用量。 |
| 视频（仅限 AppSource、咨询服务和 SaaS 产品/服务） | 最强大的视频能够以叙事形式传达产品/服务的价值：<ul><li>让客户而不是你的公司成为故事的主角。</li><li>视频应该解决目标客户面临的首要难题及其目标。</li><li>建议长度：60-90 秒。</li><li>包含使用视频名称的搜索关键字。</li></ul><br>考虑添加其他视频，例如操作说明、入门或客户荐言。 |
| 屏幕截图（1280×720 像素） | 最多添加 5 张屏幕截图。 在文件名中包含搜索关键字。 |
|

> [!IMPORTANT]
> 参考 Microsoft 商标以及 Microsoft 软件、产品和服务的名称时，请确保产品/服务的名称和说明符合 [Microsoft 商标和品牌指南](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)及其他相关的产品特定指南。

## <a name="link-to-your-offer-page-from-your-website"></a>从网站链接到套餐页

若要轻松地将用户引导到商业市场中的产品/服务，请利用网站或数字营销宣传材料中的“立即获取”徽章。 在我们的[市场营销工具包](https://partner.microsoft.com/asset/collection/azure-marketplace-and-appsource-publisher-toolkit#/)查找这些徽章。

从网站中的 AppSource 或 Azure 市场徽章链接到商业市场列表时，可在 URL 末尾包含以下查询参数，以便为强大的分析和报告功能提供支持：
* **src**：包括路由到 AppSource 的流量的来源（例如网站、LinkedIn 或 Facebook）。
* **mktcmpid**：市场营销活动 ID，可以包含字母、数字、下划线和连字符的任意组合，最多 16 个字符（例如 blogpost_12）。

以下示例 URL 包含前面所述的两个查询参数：`https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

在 AppSource URL 中添加这些参数后，可在合作伙伴中心的[分析仪表板](https://go.microsoft.com/fwlink/?linkid=2165765)中查看营销活动的效果。

## <a name="listing-creation-technical-best-practices"></a>列出创建技术最佳做法

导航 Markdown 可能比较麻烦。 我们编译了一些最佳做法，用于修订和查看合作伙伴中心内的商业市场的产品/服务列表，从而为你提供帮助。 [商业市场列表技术最佳做法指南](https://partner.microsoft.com/asset/collection/azure-marketplace-and-appsource-publisher-toolkit#/)介绍如何编辑列表和预览 Markdown 代码。

## <a name="next-steps"></a>后续步骤

- 详细了解[商业市场权益](./gtm-your-marketplace-benefits.md)。
- 登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)以创建并配置产品/服务。 在合作伙伴中心[创建一个帐户](create-account.md)（如果尚未创建）。
