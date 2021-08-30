---
title: 产品/服务展示最佳做法 - Microsoft 商业市场
description: 了解适用于 Microsoft AppSource 和 Azure 市场产品/服务的上市展示最佳做法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 06/03/2021
ms.openlocfilehash: 12c3641597168ee7e76acf4a16984f4419d40555
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298828"
---
# <a name="offer-listing-best-practices"></a>套餐列出最佳做法

本文提供了有关创建具有吸引力的 Microsoft 商业市场产品/服务的建议。 下表概述了有关在合作伙伴中心填写产品/服务信息的最佳做法。 若要分析你的产品/服务的业绩，请在合作伙伴中心转到[市场见解仪表板](https://go.microsoft.com/fwlink/?linkid=2165936)。

## <a name="online-store-offer-details"></a>在线商店产品/服务详细信息

| 设置 | 最佳做法 |
|:--- |:--- |  
| 产品名称 | 对于应用，提供包括搜索关键字的清晰标题将有助于客户发现你的产品/服务。 <br> <br> 对于咨询服务，请遵循以下格式：[产品/服务名称：[持续时间] [产品/服务类型]（例如，Contoso：2 周实施） |
| 套餐说明 | 在前几句话中清晰地描述你的产品/服务的价值主张。  请记住，这些句子可能会在搜索引擎结果中使用。 核心价值主张应包含： <ul> <li>产品或解决方案的说明。 </li> <li> 从产品或解决方案中受益的用户角色。 </li> <li> 该产品或解决方案可满足的客户需求或解决的难题。 </li> </ul> <br> 尽可能使用行业标准词汇或基于优势的措辞。  不要依赖于特性和功能来销售产品。  而应该强调提供的价值。 <br> <br> 对于咨询服务商品，明确说明你提供的专业服务。 |

> [!IMPORTANT]
> 参考 Microsoft 商标以及 Microsoft 软件、产品和服务的名称时，请确保产品/服务的名称和说明符合 [Microsoft 商标和品牌指南](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)及其他相关的产品特定指南。

## <a name="online-store-listing-details"></a>在线商店商品详细信息

下表显示了哪些产品/服务类型具有适用于不同在线商店的类别和行业：Azure 市场和 Microsoft AppSource。

| 产品/服务类型 | Azure 市场类别 | AppSource 类别 | AppSource 行业 |
| :------------------- |:----------------:|:------:|:-------------:|
| Azure 应用程序     | X |   |   |
| Azure 容器       | X |   |   |
| Azure 虚拟机 | X |   |   |
| 咨询服务    | X<sup>*</sup> |   | X<sup>*</sup> |
| Dynamics 365 Customer Engagement 和 Power Apps | | X | X |
| Dynamics 365 for Operations | | X | X |
| Dynamics 365 Business Central | | X | X |
| IoT Edge 模块 | X | |  |
| 托管服务 | X | |  |
| Power BI 应用 | | X | X |
| SaaS | X | X | X |

* 产品/服务根据主要产品发布到相关的在线商店。 如果主要产品是 Azure，则会发布到 Azure 市场。 否则，会发布到 AppSource。

### <a name="categories"></a>类别

Microsoft AppSource 和 Azure 市场是提供不同解决方案类型的在线商店。 Azure 市场提供在 Azure 上构建的或面向 Azure 的 IT 解决方案。  Microsoft AppSource 提供业务解决方案，例如行业 SaaS 应用程序、Dynamics 365 加载项、Microsoft 365 加载项和 Power Platform 应用。

类别和子类别基于解决方案类型映射到每个在线商店。 产品/服务将发布到 Microsoft AppSource 还是 Azure 市场，具体取决于产品/服务类型、产品/服务的交易功能以及类别/子类别的选择。 

选择最符合解决方案类型的类别和子类别。 可以选择：

* 最多两个类别，包括主类别和辅助类别（可选）。
* 每个主类别和/或辅助类别最多包含两个子类别。 如果未选择子类别，将仍然只会在所选类别上发现你的产品/服务。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>重要说明：SaaS 产品/服务和 Microsoft 365 加载项

若要了解交易功能如何影响市场客户查看和购买产品/服务的方式的特定详细信息，请参阅[在商业市场中进行交易](marketplace-commercial-transaction-capabilities-and-considerations.md)。 对于 SaaS 产品/服务，产品/服务的交易功能和类别选择将决定产品/服务将在哪个在线商店发布。

下表显示了适用于不同在线商店的选项组合：Azure 市场和 Microsoft AppSource。

| 按流量计费 | Microsoft 365 加载项 | 仅专用计划 | 仅公用计划 | 公用和专用计划 | 适用的在线商店 |
|:-------------:|:---:|:--------:|:---------:|:---------------------:|:-------------:|
|  | X |  |  |  | AppSource |
| X |  | X |  |  | Azure 市场 |
| X |  |  | X |  | Azure 市场 |
| X |  |  |  | X | Azure 市场<sup>2</sup> |
|  |  | X |  |  | Azure 市场 |
|  |  |  | X |  | AppSource<sup>1</sup><br>Azure 市场<sup>1</sup> |
|  |  |  |  | X | AppSource<sup>1</sup><br>Azure 市场<sup>1,2</sup> |
|  |  |  |  | X | AppSource<sup>1</sup><br>Azure 市场<sup>1</sup> |

1. 取决于类别/子类别和行业选择
2. 具有专用计划的产品/服务将发布到 Azure 门户

> [!NOTE]
> 无法在同一产品/服务中同时拥有上市计划和可交易计划。

### <a name="industries"></a>行业

行业选择仅适用于发布到 AppSource 的产品/服务和在 Azure 市场中发布的咨询服务。  如果你的产品/服务可满足行业特定的需求，请选择相应的行业和/或垂直市场，并在产品/服务说明中列出行业特定的功能。 最多可以选择两 (2) 个行业；对于选定的每个行业，最多可以选择两 (2) 个垂直市场。

>[!Note]
>Azure 市场中的咨询服务产品/服务没有行业垂直市场。

| **行业** |  **垂直市场** |
| :------------------- | :----------------|
| **农业** | |
| **建筑和建设** | |
| **汽车** | |
| **Distribution** | 批发 <br> 包裹寄送 |  
| **教育水平** | 高等教育 <br> 小学和中学教育 / K-12 <br> 图书馆和博物馆 |
| **金融服务** | 银行业和资本市场 <br> 保险 | 
| 政府 |  国防和情报 <br> 平民政府 <br> 公安与司法 |
| **医疗保健** | 医疗付款人 <br> 医疗提供方 <br> 医药业 | 
| **酒店餐饮和旅游** | 旅游和运输业 <br> 酒店和休闲 <br> 餐饮服务 | 
| **制造和资源** | 化工和农业化学 <br> 离散制造 <br> 能源 | 
| **媒体和通信** | 媒体和娱乐 <br> 电信 | 
| **其他公共部门行业** | 林业和渔业 <br> 非营利组织 | 
| **专业服务** | 合作伙伴专业服务 <br> Legal | 
| **房地产** | |

仅适用于 Microsoft AppSource 的行业：

| **行业** |  **垂直市场** |
| :------------------- | :----------------|
| **零售和消费品** | 零售商 <br> 消费品 |

### <a name="applicable-products"></a>适用的产品

选择可与你的应用配合使用的适用产品，以便该产品/服务在 AppSource 中显示在所选产品下。

### <a name="search-keywords"></a>搜索关键字

关键字可帮助客户找到你的产品/服务。 确定产品/服务的高频搜索关键字，并将其包含在产品/服务摘要和说明中，以及产品/服务展示详细信息部分的关键字部分中。

## <a name="online-store-marketing-details"></a>在线商店营销详细信息
| 设置 | 最佳做法 |
|:--- |:--- |  
| 产品/服务徽标（216 × 216 到 350 x 350 像素的 PNG 格式）：应用详细信息页 | 针对数字媒体设计和优化徽标。<br>将 PNG 格式的徽标上传到产品/服务的应用详细信息列表页。 合作伙伴中心会将其调整为所需的徽标大小。 |
| 产品/服务徽标（48 × 48 像素的 PNG 格式）：搜索页 | 合作伙伴中心将根据上传的大徽标生成此徽标。 稍后，可以选择将其替换为其他图像。 |
| “了解详细信息”文档 | 在“了解详细信息”下包含支持性的销售和市场营销资产，例如：<ul><li>白皮书</li><li> 小册子</li><li>清单；</li><li> PowerPoint 演示文稿</li></ul><br>以 PDF 格式保存所有文件。 此处的目标应该是教育客户，而不是推销。<br><br>将应用登陆页的链接添加到所有文档，并添加 URL 参数来帮助跟踪访问量和试用量。 |
| 视频：仅限 AppSource、咨询服务和 SaaS 产品/服务 | 最强大的视频能够以叙事形式传达产品/服务的价值：<ul> <li> 让客户而不是你的公司成为故事的主角。 </li> <li> 视频应该解决目标客户面临的首要难题及其目标。 </li> <li> 建议长度：60-90 秒。</li> <li> 包含使用视频名称的搜索关键字。 </li> <li> 考虑添加其他视频，例如操作说明、入门或客户荐言。 </li> </ul> |
| 屏幕截图 (1280&nbsp;&times;&nbsp;720) | 最多添加 5 张屏幕截图：<br>在文件名中包含搜索关键字。 |

## <a name="link-to-your-offer-page-from-your-website"></a>从网站链接到套餐页

从网站中的 AppSource 或 Azure 市场徽章链接到商业市场列表时，可在 URL 末尾包含以下查询参数，以便为强大的分析和报告功能提供支持：
* **src**：包括路由到 AppSource 的流量的来源（例如网站、LinkedIn 或 Facebook）。
* **mktcmpid**：市场营销活动 ID，可以包含字母、数字、下划线和连字符的任意组合，最多 16 个字符（例如 blogpost_12）。

以下示例 URL 包含前面所述的两个查询参数：`https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

通过在 AppSource URL 中添加参数，可以在合作伙伴中心的[分析仪表板](https://go.microsoft.com/fwlink/?linkid=2165765)中查看营销活动的效果。

## <a name="next-steps"></a>后续步骤

详细了解[商业市场权益](./gtm-your-marketplace-benefits.md)。

登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)以创建并配置产品/服务。 在合作伙伴中心[创建一个帐户](create-account.md)（如果尚未创建）。
