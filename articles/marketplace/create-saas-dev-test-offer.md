---
title: 创建测试产品/服务
description: 如何在 Microsoft 合作伙伴中心的商业市场计划中创建单独的开发产品/服务，以供测试生产产品/服务。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 318eebcca3053be2164a1fc52905f36f26b11a6d
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331117"
---
# <a name="create-a-test-offer"></a>创建测试产品/服务

要在与生产产品/服务不同的环境中进行开发，你将创建一个单独的测试和开发 (DEV) 产品/服务以及一个单独的生产 (PROD) 产品/服务。 如需了解使用单独的 DEV 产品/服务的好处，请参阅[规划 SaaS 产品/服务](plan-saas-offer.md#test-offer)。

在 DEV 和 PROD 产品/服务中，应将大多数设置都配置为相同的。 例如，官方营销语言和资产（如屏幕截图和徽标）应相同。 在配置相同的情况下，你可以将 DEV 产品/服务的计划中的字段复制并粘贴到 PROD 产品/服务的计划中。

以下各部分介绍 DEV 和 PROD 这两种产品/服务之间的配置差异。

## <a name="offer-setup-page"></a>产品/服务设置页

建议在这两个产品/服务的“别名”框中使用相同的别名，但在 DEV 产品/服务的别名后面附加“_test”。 例如，如果你的 PROD 产品/服务的别名为“contososolution”，则 DEV 产品/服务的别名应为“contososolution_test”。 这样一来，就可以轻松地辨别 PROD 产品/服务和 DEV 产品/服务。

在“潜在客户”部分中，为 DEV 产品/服务使用 Azure 表或 CRM 测试环境。 为 PROD 产品/服务使用打算使用的潜在客户管理系统。

## <a name="properties-page"></a>“属性”页

在 DEV 和 PROD 产品/服务中，对该页面进行相同的配置。

## <a name="offer-listing-page"></a>产品/服务列表页

在 DEV 和 PROD 产品/服务中，对该页面进行相同的配置。

## <a name="preview-audience"></a>预览版受众

在 DEV 产品/服务中，包括开发人员和测试人员（包括你自己）的 Azure Active Directory (AAD) 用户主体名称或 Microsoft 帐户 (MSA) 电子邮件地址。 AAD 上用户的用户主体名称可以不同于该用户的电子邮件。 例如，jane.doe@contoso.com 无效，但 janedoe@contoso.com 有效。 当你在开发和测试阶段共享“预览版”链接时，你指定的用户将有权访问 DEV 产品/服务。

在 PROD 产品/服务中，于选择“上线”按钮以实时发布产品/服务之前，先包括将验证产品/服务的用户的 Azure AD 用户主体名称或 Microsoft 帐户电子邮件。

## <a name="technical-configuration-page"></a>技术配置页

下表描述了 DEV 产品/服务和 PROD 产品/服务在设置方面的差异。

***表 1：技术配置差异***

| 设置 | DEV 产品/服务 | PROD 产品/服务 |
| ------------ | ------------- | ------------- |
| 登陆页 URL | 输入开发/测试终结点。 | 输入生产终结点。 |
| 连接 Webhook | 输入开发/测试终结点。 | 输入生产终结点。 |
| Azure Active Directory 租户 ID | 输入测试应用注册租户 ID（AAD 目录 ID）。 | 输入生产应用注册租户 ID。 |
| Azure Active Directory 应用程序 ID | 输入测试应用注册应用程序 ID（客户端 ID）。 | 输入生产应用注册应用程序 ID。 |
||||

## <a name="plan-overview-page"></a>计划概述页面

创建计划时，建议在 DEV 和 PROD 产品/服务中使用相同的“计划 ID”和“计划名称”，但需要在 DEV 产品/服务的计划 ID 后面附加“_test” 。 例如，如果 PROD 产品/服务的计划 ID 为“enterprise”，则 DEV 产品/服务的计划 ID 应为“enterprise_test”。 这样一来，就可以轻松地辨别 PROD 产品/服务和 DEV 产品/服务。 你将在 PROD 产品/服务中创建计划，其中包含你认为最适合你的产品/服务的定价模型和价格。

### <a name="plan-listing"></a>计划一览

在 DEV 和 PROD 计划的“计划概述” > “计划列表”选项卡上，输入相同的计划描述 。

### <a name="pricing-and-availability-page"></a>定价和可用性页面

本部分提供有关完成“计划概述” > “定价和可用性”页的指导 。

#### <a name="markets"></a>市场

为 DEV 和 PROD 产品/服务选择相同的市场。

#### <a name="pricing"></a>定价

使用 DEV 产品/服务来试验定价模型。 验证了哪种定价模型效果最好后，你将在 PROD 产品/服务中创建包含所需定价模型和价格的计划。

DEV 产品/服务应具有价格为零或低价的计划。 PROD 产品/服务将包含你想要向客户收取的价格。

> [!IMPORTANT]
> 系统将为 DEV 和 PROD 这两种产品/服务处理预览版中的购买。 如果产品/服务的价格为 $100/月，则将向你的公司收费 $100。 如果发生这种情况，你可以创建一个[支持工单](support.md)，我们将全额退款（不收取存储服务费）。

#### <a name="pricing-model"></a>定价模型

在 DEV 和 PROD 产品/服务的计划中使用相同的定价模型。 例如，如果 PROD 产品/服务中的计划为统一费率，并按月计费，请使用相同的模型在 DEV 产品/服务中配置计划。

为了降低测试定价模型（包括市场自定义计量维度）的成本，建议你在 DEV 产品/服务中以比 PROD 产品/服务更低的价格配置“定价和可用性”选项卡的“定价”部分 。 在 DEV 产品/服务中为计划设置价格时，可以遵循以下准则。

***表 2：定价准则***

| 价格 | 评论 |
| ------------ | ------------- |
| $0.00 | 将总交易费用设置为零，这样不会产生财务影响。 在调用计量 API 时使用此价格，或在开发解决方案时将其用于测试产品/服务中的购买计划。 |
| $0.01 - $49.99 | 使用此价格范围来测试分析、报告和购买过程。 |
| 50.00 - 100.00 美元 | 使用此价格范围测试付款。 有关我们的付款计划的信息，请参阅[付款计划和流程](/partner-center/payout-policy-details)。 |
|||

> [!IMPORTANT]
>  为了避免在测试中支付存储服务费，请在购买测试后 7 天内创建[支持票证](support.md)。

#### <a name="free-trial"></a>免费试用

不要为 DEV 产品/服务启用免费试用版。

## <a name="co-sell-with-microsoft-page"></a>“与 Microsoft 联合销售”页

不要配置 DEV 产品/服务的“与 Microsoft 联合销售”选项卡。

## <a name="resell-through-csps"></a>通过 CSP 转售

不要配置 DEV 产品/服务的“通过 CSP 转售”选项卡。

## <a name="next-steps"></a>后续步骤

- [测试和发布 SaaS 产品/服务](test-publish-saas-offer.md)
