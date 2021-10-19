---
title: 面向 Microsoft 商业市场 - Azure 市场规划 SaaS 产品/服务
description: 使用 Microsoft 合作伙伴中心的商业市场计划规划新的服务型软件 (SaaS) 产品/服务，以在 Microsoft AppSource、Azure 市场中或通过云解决方案提供商 (CSP) 计划列出或销售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 6f429bd55c3fbf93f88c91168021eed9f1d6505a
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730178"
---
# <a name="plan-a-saas-offer-for-the-commercial-marketplace"></a>面向商业市场规划 SaaS 产品/服务

本文介绍面向 Microsoft 商业市场发布服务型软件 (SaaS) 产品/服务的不同选项和要求。 使用 SaaS 产品/服务，可以通过在线订阅向客户交付和许可软件解决方案。 作为 SaaS 发布者，你负责管理所需的基础结构并支付相关费用，以便支持客户使用该产品/服务。 本文将通过合作伙伴中心帮助你准备产品/服务，以便面向商业市场发布。

## <a name="listing-options"></a>列表选项

在准备发布新 SaaS 产品/服务时，需要决定选择哪个“列表”选项。 所选的列表选项决定在合作伙伴中心创建产品/服务时还需要提供哪些信息。 列表选项在“产品/服务设置”页上定义，如“[如何在商业市场创建 SaaS 产品/服务](create-new-saas-offer.md)”中所述。

下表显示了适用于商业市场中 SaaS 产品/服务的列表选项。

| 列表选项 | 事务处理 |
| ------------ | ------------- |
| 与我联系 | 客户根据列表中的信息直接与你联系。``*``<br>发布产品/服务后，可以更改为其他列表选项。 |
| 免费试用 | 客户将通过 Azure Active Directory (Azure AD) 重定向到目标 URL。``*``<br>发布产品/服务后，可以更改为其他列表选项。 |
| 立即获取（免费） | 客户将通过 Azure AD 重定向到目标 URL。``*``<br>发布产品/服务后，可以更改为其他列表选项。 |
| 通过 Microsoft 进行销售  | 通过 Microsoft 销售的产品/服务称为可交易产品/服务。 可交易的产品/服务是指 Microsoft 代表发布者协助以资金换取软件许可证。 我们使用你所选的定价模型对 SaaS 产品/服务计费，并代表你管理客户事务。 我们直接向你（合作伙伴）收取 Azure 基础结构使用费。 你应将基础结构成本纳入定价模型之中。 有关更多详细信息，请参阅以下“[SaaS 账单](#saas-billing)”。<br><br>注意：发布产品/服务后，便无法再更改此选项。  |
|||

``*`` 发布者负责为软件许可证交易的各个方面提供支持，包括但不限于订单、履单、计量、账单、开票、付款和收款。

有关这些列表选项的详细信息，请参阅[商业市场交易功能](marketplace-commercial-transaction-capabilities-and-considerations.md)。

发布产品/服务后，为产品/服务选择的列表选项将以按钮形式显示在产品/服务列表页的左上角。 例如，以下屏幕截图使用“立即获取”按钮显示了 Azure 市场中的产品/服务列表页。

![说明在线商店中的产品/服务列表。](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>技术要求

技术要求因对产品/服务选择的列表选项而异。

“与我联系”列表选项没有技术要求。 可以选择连接客户关系管理 (CRM) 系统来管理潜在顾客。 有关信息，请参阅本文后面的[潜在顾客](#customer-leads)部分。

“立即获取（免费）”、“免费试用”和“通过 Microsoft 进行销售”  具有以下技术要求：

- 必须同时启用 Microsoft 帐户 (MSA) 和 [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 来对站点上的买家进行身份验证。 必须让具有 Azure AD 帐户的买家通过单一登录 (SSO) 使用 Azure AD 登录到你的应用程序。
- 必须创建一个登陆页，为购买产品/服务的客户提供无缝登录和加入体验。 该登陆页可帮助他们完成任何其他所需的预配或设置。 有关创建登陆页的指导，请参阅以下文章：
  - [为商业市场中的可交易 SaaS 产品/服务构建登陆页](azure-ad-transactable-saas-landing-page.md)
  - [针对商业市场中的免费或试用版 SaaS 套餐构建登录页](azure-ad-free-or-trial-landing-page.md)

这些附加技术要求仅适用于“通过 Microsoft 进行销售”（可交易）列表选项：

- 必须使用 [SaaS 履单 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 与 Azure 市场和 Microsoft AppSource 相集成。 必须公布一项可与 SaaS 订阅交互的服务，以便创建、更新和删除用户帐户与服务计划。 必须在 24 小时内支持关键 API 更改。 非关键 API 更改将定期发布。 有关所收集字段使用情况的图表和详细说明，请参阅相关 [API](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 的文档。
- 必须至少为你的产品/服务创建一个计划。 该计划将根据你在发布之前选择的定价模型（“统一费率”或“用户费率”）进行定价。  本文后面部分将提供有关[计划](#plans)的更多详细信息。
- 客户可随时取消你的产品/服务。

### <a name="technical-information"></a>技术信息

如果要创建一款可交易产品/服务，则需要为“技术配置”页收集以下信息。 如果选择独立处理事务而不创建可交易产品/服务，请跳过此部分并转到[体验版](#test-drives)。

- 登陆页 URL：用户从商业市场获得产品/服务以及从新创建的 SaaS 订阅触发配置进程后，将被定向到的 SaaS 站点 URL（例如 `https://contoso.com/signup`）。 该 URL 将收到一个令牌，该令牌可用于调用履单 API 以获取交互式注册页面的预配详细信息。

  通过唯一标识特定客户 SaaS 购买的市场购买标识令牌参数，可调用此 URL。 必须使用[解析 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 将此令牌换为相应的 SaaS 订阅详细信息。 这些详细信息以及任何其他希望作为客户交互网页一部分收集的内容可用于启动客户加入体验，该体验最终需要在 API 上通过激活调用来启动订阅期。 在此页上，用户应进行注册，并使用 Azure Active Directory (Azure AD) 完成一键式身份验证。

  当客户从 Azure 门户或 Microsoft 365 管理中心启动托管 SaaS 体验时，还会调用包含市场购买标识令牌参数的 URL。 在新客户购买后首次提供令牌时，以及现有客户再次提供令牌来管理其 SaaS 解决方案时，应处理这两个流。

    配置的登陆页面应全天候保持正常运行。 只有这样，在商业市场中你的 SaaS 产品/服务有新交易或触发有效产品/服务订阅的配置请求后，你才会收到通知。

- 连接 Webhook：对于 Microsoft 需向你发送的所有异步事件（例如，SaaS 订阅已取消），我们要求[提供连接 Webhook URL](./partner-center-portal/pc-saas-fulfillment-api-v2.md#implementing-a-webhook-on-the-saas-service)。 我们将调用此 URL 来向你通知事件。

  提供的 Webhook 应全天候保持正常运行。 只有这样，你才能收到客户通过商业市场购买的 SaaS 订阅的更新通知。

  > [!NOTE]
  > 在 Azure 门户中，要求你创建单租户 [Azure Active Directory (Azure AD) 应用注册](../active-directory/develop/howto-create-service-principal-portal.md)。 调用市场 API 时，请使用应用详细信息对解决方案进行身份验证。 要查找[租户 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，请转到 Azure Active Directory，选择“属性”，然后查找列出的“目录 ID”编号。 例如 `50c464d3-4930-494c-963c-1e951d15360e`。

- Azure Active Directory 租户 ID：（也称为目录 ID）。 在 Azure 门户中，我们需要你[注册 Azure Active Directory (AD) 应用](../active-directory/develop/howto-create-service-principal-portal.md)，以便可以将其添加到 API 的访问控制列表 (ACL)，从而确保你有权调用该应用。 要查找 Azure Active Directory (Azure AD) 应用的租户 ID，请转到 Azure Active Directory 中的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用。 然后查找列出的目录（租户）ID号（例如，`50c464d3-4930-494c-963c-1e951d15360e`）。

- Azure Active Directory 应用程序 ID：还需要使用[应用程序 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 要获取其值，请转到 Azure Active Directory 中的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用。 然后查找列出的应用程序（客户端）ID 号（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。

  Azure AD 应用程序 ID 与合作伙伴中心帐户中的发布者 ID 相关联。 该于该帐户下的所有产品/服务，必须使用同一个应用程序 ID。

  > [!NOTE]
  > 如果发布者在合作伙伴中心有两个或更多不同的帐户，则 Azure AD 应用程序注册详细信息只能在一个帐户中使用。 不支持在不同的发布者帐户下使用同一租户 ID、产品/服务的应用程序 ID 对。

## <a name="test-drives"></a>体验版
可以选择为 SaaS 应用启用体验版。 体验版允许客户在固定的小时数内访问预配置环境。 可以为任何发布选项启用体验版，但此功能还有其他要求。 若要详细了解体验版，请参阅[什么是体验版？](what-is-test-drive.md)。 有关配置不同类型的体验版的信息，请参阅[体验版技术配置](test-drive-technical-configuration.md)。

> [!TIP]
> 体验版不同于[免费试用版](plans-pricing.md#free-trials)。 你可以提供体验版、免费试用版，或提供这两者。 这两种版本都可以让客户在固定时间内使用你的解决方案。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

## <a name="customer-leads"></a>潜在顾客

必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的在线商店将发送到你配置的 CRM 系统。 商业市场支持各种 CRM 系统，以及使用 Azure 表或使用 Power Automate 配置 HTTPS 终结点的选项。

你可以在创建产品/服务期间或之后随时添加或修改 CRM 连接。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="selecting-an-online-store"></a>选择在线商店

发布 SaaS 产品/服务后，它将列在 Microsoft AppSource 和/或 Azure 市场中。 每个在线商店可满足独特的客户需求。 AppSource 面向业务解决方案，而 Azure 市场面向 IT 解决方案。 产品/服务类型、交易功能和类别决定该产品/服务将发布到何处。 类别和子类别基于解决方案类型映射到每个在线商店。 

如果 SaaS 产品既是 IT 解决方案（Azure 市场），又是业务解决方案 (AppSource)，则可以选择适用于每个在线商店的类别和子类别。 发布到这两个在线商店的产品/服务应设定价值主张：IT 解决方案和业务解决方案。

> [!IMPORTANT]
> 通过 Azure 市场和 Azure 门户可获取[按流量计费](partner-center-portal/saas-metered-billing.md)的 SaaS 产品/服务。 通过 Azure 门户和 AppSource 可获取只有专用计划的 SaaS 产品/服务。

| 按流量计费 | 公共计划 | 专用计划 | 获取方式： |
|---|---|---|---|
| 是             | 是         | 否           | Azure 市场和 Azure 门户 |
| 是             | 是         | 是          | Azure 市场和 Azure 门户* |
| 是             | 否          | 是          | 仅使用 Azure 门户 |
| 否              | 否          | 是          | Azure 门户和 AppSource |
|||||

&#42; 只能通过 Azure 门户和 AppSource 获取产品/服务的专用计划。

例如，一款按流量计费的产品/服务只有专用计划（无公共计划），则客户可在 Azure 门户中进行购买。 详细了解 [Microsoft 商业市场中的专用产品/服务](private-offers.md)。

有关在线商店支持的列表选项的详细信息，请参阅[在线商店支持的列表和定价选项](determine-your-listing-type.md#listing-and-pricing-options-by-online-store)。 有关类别和子类别的详细信息，请参阅[商业市场中的类别和子类别](categories.md)。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

如果选择使用标准协定，则可以选择向标准协定中添加通用修订条款和最多 10 条自定义修订。 还可以使用自己的条款和条件，而不是标准协定。 将在“属性”页中管理这些详细信息。 有关详细信息，请参阅 [Microsoft 商业市场的标准协定](standard-contract.md)。

> [!NOTE]
> 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件。 它是“或”方案。 要么根据标准协定提供解决方案，要么根据自己的条款和条件提供解决方案。 如果想修改标准协定的条款，可以通过标准协定修订进行。

## <a name="microsoft-365-integration"></a>Microsoft 365 集成

通过与 Microsoft 365 集成，SaaS 产品/服务可通过相关的免费加载项（如 Teams 应用、Office 加载项和 SharePoint Framework 解决方案）跨多个 Microsoft 365 应用表面提供连接体验。 通过提供以下信息，可帮助你的客户轻松发现 E2E 解决方案的所有方面（Web 服务 + 相关加载项）并将它们部署在一个进程中。 
  - 如果 SaaS 产品/服务与 Microsoft Graph 集成，则提供 SaaS 产品/服务用于集成的 Azure Active Directory (AAD) 应用 ID。 管理员可以审查 AAD 应用 ID 上设置的 SaaS 产品/服务正常运行所需的访问权限，并在部署时需要高级管理权限时授予访问权限。 
    
     如果选择通过 Microsoft 销售产品/服务，则这是已注册在登陆页上使用的同一 AAD 应用 ID，用于获取完成客户订阅激活所需的基本用户信息。 有关详细指导，请参阅[为商业市场中的可交易 SaaS 产品/服务构建登陆页](azure-ad-transactable-saas-landing-page.md)。 
    
   -    提供与要链接的 SaaS 产品/服务配合使用的相关加载项列表。 客户将能够在 Microsoft AppSource 上发现 E2E 解决方案，而管理员可以通过 Microsoft 365 管理中心在同一进程中同时部署 SaaS 和已链接的所有相关加载项。
    
        若要链接相关加载项，需要提供加载项的 AppSource 链接，这意味着必须先将加载项发布到 AppSource。 可以链接的受支持加载项类型包括：Teams 应用、Office 加载项和 SharePoint Framework (SPFx) 解决方案。 每个链接的加载项对于 SaaS 产品/服务必须唯一。 

对于链接的产品，在 AppSource 上搜索将返回一个结果，其中包含 SaaS 和所有链接的加载项。客户可以在 SaaS 产品/服务的产品详细信息页和链接的加载项之间导航。IT 管理员可以通过 Microsoft 365 管理中心的集成和连接体验，在同一进程中查看和部署 SaaS 和链接的加载项。 若要了解详细信息，请参阅[测试和部署 Microsoft 365应用 - Microsoft 365 管理](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps)。

### <a name="microsoft-365-integration-support-limitations"></a>Microsoft 365 集成支持限制

所有情况下，AppSource 都支持单个 E2E 解决方案发现，但是，以下情况不支持上述通过 Microsoft 365 管理中心简化 E2E 解决方案的部署：

   - “与我联系”仅列出产品/服务。 
   - 相同加载项链接到多个 SaaS 产品/服务。
   - SaaS 产品/服务链接到加载项，但它不与 Microsoft Graph 集成，并且未提供 AAD 应用 ID。
  - SaaS 产品/服务链接到加载项，但为 Microsoft Graph 提供的 AAD 应用 ID 在多个 SaaS 产品/服务之间共享。
 
## <a name="offer-listing-details"></a>产品/服务列表详细信息

在合作伙伴中心[创建新 SaaS 产品/服务](create-new-saas-offer.md)时，需在“产品/服务列表”页上输入文本、图像、可选视频和其他详细信息。 这些就是客户在商业市场中发现你的产品/服务列表时会看到的信息，如以下示例中所示。

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

调用说明

1. 徽标
2. 类别
3. 行业
4. 支持地址（链接）
5. 使用条款
6. 隐私策略
7. 产品名称
8. 总结
9. 说明
10. 屏幕截图/视频
11. 文档

以下示例演示了 Azure 门户中的产品/服务列表。

![说明 Azure 门户中的产品/服务列表。](./media/example-managed-service-azure-portal.png)

**调用说明**

1. 标题
1. 说明
1. 有用的链接
1. 屏幕截图

> [!NOTE]
> 如果产品/服务说明的开头短语是“此应用程序只提供 [非英语语言] 版本”，那么产品/服务列表内容就不要求是英语的。

为了帮助更轻松地创建产品/服务，请提前准备其中一些项。 除非另有说明，以下项均为必填项。

- 名称：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且长度必须限制为 50 个字符。
- 搜索结果摘要：使用无换行符且长度不超过 100 个字符的单句来描述产品/服务的用途或功能。 此摘要用于商业市场列表搜索结果。
- 说明：此说明将显示在商业市场列表概述中。 考虑的内容包括价值主张、关键优势、目标用户群、任何类别或行业关联、应用内购买机会、任何所需披露，以及用于了解详细信息的链接。

    此文本框包含富文本编辑器控件，可以使用这些控件来使说明更具吸引力。 还可以使用 HTML 标记来设置说明的格式。 此框中最多可以输入 3,000 个字符（包括 HTML 标记）。 有关其他提示，请参阅[编写出色的应用说明](/windows/uwp/publish/write-a-great-app-description)。

- 入门说明：如果选择通过 Microsoft 销售产品/服务（可交易产品/服务），则此字段是必填字段。 这些说明可帮助客户连接到 SaaS 产品/服务。 可以添加最多 3,000 个字符的文本，以及指向更详细的联机文档的链接。
- 搜索关键字（可选）：最多可输入三个搜索关键字，客户可以使用它们在在线商店中查找你的产品/服务。 无需包含产品/服务的“名称”和“说明”：该文本自动包含在搜索中。
- **隐私策略链接**：你公司的隐私策略的 URL。 你必须提供有效的隐私策略并负责确保你的应用符合隐私法律和法规。
- 联系信息：必须提供组织中的以下联系人：
  - **支持联系人**：提供客户打开票证时要使用的 Microsoft 合作伙伴的姓名、电话和电子邮件。 还必须包含支持网站的 URL。
  - **工程联系人**：提供姓名、电话和电子邮件，以便 Microsoft 在产品/服务出现问题时直接联系。 此联系信息未列在商业市场中。
  - CSP 计划联系人（可选）：如果选择加入 CSP 计划，请提供姓名、电话号码和电子邮件，以便这些合作伙伴可以在有任何疑问时与你联系。 你还可以在营销材料中包含 URL。
- **有用的链接**（可选）：你可以为产品/服务的用户提供各种资源的链接。 例如，论坛、常见问题解答和发行说明。
- 支持文档：最多可提供三份面向客户的文档，例如白皮书、手册、清单或 PowerPoint 演示文稿。
- 媒体 – 徽标：为“大”徽标提供 PNG 文件。 合作伙伴中心将使用此文件来创建小徽标和中等徽标。 稍后可以选择将这些徽标替换为其他图像。

   - 大（216 x 216 到 350 x 350 像素，必选）
   - 中（90 x 90 像素，可选）
   - 小（48 x 48 像素，可选）

  这些徽标用于在线商店的不同位置：

  - 小徽标显示在 Azure 市场搜索结果中以及 AppSource 主页和搜索结果页上。
  - 中徽标在 Microsoft Azure 中创建新资源时显示。
  - 大徽标显示在 Azure 市场和 AppSource 的产品/服务列表页上。

- 媒体-屏幕截图：必须至少添加一个符合以下要求的屏幕截图（最多 5 个），以显示产品/服务的工作原理：
  - 1280 x 720 像素
  - PNG 文件类型
  - 必须包含标题
- 媒体 - 视频（可选）：最多可添加四个符合以下要求的视频来演示产品/服务：
  - 名称
  - URL：必须仅托管在 YouTube 或 Vimeo 上。
  - 缩略图：1280 x 720 PNG 文件

> [!Note]
> 产品/服务必须满足一般[商业市场认证策略](/legal/marketplace/certification-policies#100-general)和[服务型软件策略](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)，才可发布到商业市场。

> [!NOTE]
> 预览版受众不同于专用计划。 专用计划仅适用于所选的特定受众。 通过它可以与特定客户协商自定义计划。 有关详细信息，请参阅下一部分“计划”。

你可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动最多添加 10 个电子邮件地址，或使用 .csv 文件最多导入 20 个电子邮件地址。 如果产品/服务已上线，仍然可以定义预览版受众，以测试对产品/服务进行的任何更改或更新。

## <a name="plans"></a>Plans

可交易产品/服务至少需要有一个计划。 计划定义解决方案范围和限制以及关联定价。 可以为你的产品/服务创建多个计划，从而为你的客户提供不同的技术和定价选项。 如果选择单独处理事务而不是创建可交易产品/服务，则不会显示“计划”页。 如果是这种情况，请跳过本部分并转到 [其他销售机会](#additional-sales-opportunities)。

有关计划（包括定价模型、免费试用版和专用计划）的一般指导，请参阅[适用于商业市场产品/服务的计划和定价](plans-pricing.md)。 以下部分讨论特定于 SaaS 产品/服务的其他信息。

### <a name="saas-pricing-models"></a>SaaS 定价模型

SaaS 产品/服务可对每个计划使用两种定价模型中的一种：统一费率或用户费率。  同一产品/服务的所有计划都必须与同一定价模型相关联。 例如，在某一产品/服务中，不允许一种计划采用“单一费率”，而同时另一种计划采用“每用户”。

统一费率 - 支持每月或每年按照一种统一费率价格访问产品/服务。 这有时称为基于站点的定价。 使用此定价模型可以选择性地定义计量计划，这些计划使用市场计量服务 API 向客户收取统一费率不涵盖的使用量的费用。 有关按流量计费的详细信息，请参阅[对使用商业市场流量服务的 SaaS 执行按流量计费](./partner-center-portal/saas-metered-billing.md)。 如果 SaaS 服务的使用行为呈突发状态，也应使用此选项。

用户费率 - 支持按照可访问产品/服务或占用席位的用户数来确定访问产品/服务的价格。 使用这种基于用户的模型，可以设置计划支持的最小和最大用户数。 可以根据用户数创建多个计划来配置不同的价格点。 这些字段是可选的。 如果保持未选中状态，则用户数将被解释为无限制（最小值为 1，最大值为服务可支持的数量）。 这些字段可以作为计划更新的一部分进行编辑。

> [!IMPORTANT]
> 发布产品/服务后便无法再更改定价模型。 此外，同一产品/服务的所有计划必须共享同一定价模型。

### <a name="saas-billing"></a>SaaS 计费

对于在（发布者）Azure 订阅中运行的 SaaS 应用，将直接对你收取基础结构使用费，客户看不到实际的基础结构使用费用。 你应当将 Azure 基础结构使用费捆绑到软件许可证定价中，以补偿为运行解决方案而部署的基础结构成本。

SaaS 应用产品/服务支持通过 Microsoft 进行销售，每月或每年按照固定费用、用户费率或使用[按流量计费服务](./partner-center-portal/saas-metered-billing.md)的消耗费用计费。 商业市场基于代理模型运行，即发布者设定价格、Microsoft 向客户收费以及 Microsoft 向发布者支付收入并预扣代理费。

下面是一个演示代理模型费用和付款明细的示例。 在此示例中，Microsoft 向客户收取 $100.00 美元的软件许可证费用，并向发布者支付 $80.00 美元。

| 你的许可证费用 | $100 美元/月 |
| ------------ | ------------- |
| Azure 使用费用（D1/1 核） | 直接向发布者而不是客户收费 |
| 由 Microsoft 向客户收费 | $100.00 美元/月（发布者必须将所有产生的或者转嫁的基础结构费用纳入许可证费用中） |
| **Microsoft 收费** | **$100 美元/月** |
| Microsoft 收取 3% 的市场服务费用，并向您支付 97% 的许可费用 | 97.00 美元/月 |
|

预览版受众可以在你的产品/服务尚未在在线商店实时发布之前提前访问。 他们可以在实时发布之前看到产品/服务在商业市场中的外观，并测试端到端功能。 

在“预览版受众”页上，可以定义有限的预览版受众。 如果选择单独处理交易，而不是通过 Microsoft 销售产品/服务，则此设置不可用。 如果是这种情况，则可以跳过本部分并转到[其他销售机会](#additional-sales-opportunities)。

## <a name="test-offer"></a>测试产品/服务

在实时发布产品/服务之前，应使用预览功能来开发技术实现、测试并试验不同的定价模型。

若要以最低的风险开发和测试 SaaS 产品/服务，建议创建一个测试和开发 (DEV) 产品/服务进行试验和测试。 DEV 产品/服务将与生产 (PROD) 产品/服务分开。

为了防止意外购买 DEV 产品/服务，决不能按“投入使用”按钮实时发布 DEV 产品/服务。

![说明合作伙伴中心中产品/服务的“产品/服务概述”页。 将显示“上线”按钮和预览版链接。 “查看验证报告”链接也显示在“自动验证”下。](./media/review-publish-offer/publish-status-saas.png)

下面是为开发团队创建单独的 DEV 产品/服务以用于开发和测试 PROD 产品/服务的一些原因：

- 避免意外收取客户费用
- 评估定价模型
- 不添加不针对实际客户的计划

### <a name="avoid-accidental-customer-charges"></a>避免意外收取客户费用

通过使用 DEV 产品/服务而不是 PROD 产品/服务，并将它们视为开发和生产环境，可以避免意外收取客户费用。

建议注册两个不同的 Azure AD 应用，用于调用市场 API。 开发人员将使用一个包含 DEV 产品/服务设置的 Azure AD 应用，运营团队将使用 PROD 应用注册。 这样一来，就可以避免开发团队犯疏忽的错误，如调用 API 来取消每月支付 10 万美元的客户的订阅。 还可以避免向客户收取未使用的计量使用费用。

### <a name="evaluate-pricing-models"></a>评估定价模型

当开发人员试验不同定价模型时，测试 DEV 产品/服务中的定价模型可降低风险。

发布者可以在 DEV 产品/服务中创建所需的计划，以确定最适合其产品/服务的定价模型。 开发人员可能希望在 DEV 产品/服务中创建多个计划来测试不同的定价组合。 例如，可以使用不同的自定义计量维度集来创建计划。 可以混合使用单一费率和自定义计量维度创建不同的计划。

若要测试多个定价选项，需要为每个唯一定价模型创建一个计划。 有关详细信息，请参阅[计划](#plans)。

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>不添加不针对实际客户的计划

通过使用 DEV 产品/服务进行开发和测试，可以减少 PROD 产品/服务中不必要的混乱。 例如，如果未提交支持票证，则不能删除创建用于测试不同定价模型或技术配置的计划。 因此，通过创建 DEV 产品/服务测试计划，可以减少 PROD 产品/服务中的混乱。

PROD 产品/服务中的混乱会让产品和营销团队感到沮丧，因为他们希望所有计划都面向真实客户。 特别是对于都希望使用不同沙盒的分散的大型团队，创建两个产品/服务将为 DEV 和 PROD 提供两个不同的环境。 在某些情况下，你可能想要创建多个 DEV 产品/服务，以支持不同人员运行不同测试方案的更大型的团队。 让不同的团队成员在独立于 PROD 产品/服务的 DEV 产品/服务中工作，有助于使生产计划尽可能接近生产就绪状态。

测试 DEV 产品/服务有助于避免达到每个产品/服务 30 个自定义计量维度的上限。 开发人员可以在 DEV 产品/服务中尝试不同的计量组合，不会影响 PROD 产品/服务中的自定义计量维度限制。

## <a name="additional-sales-opportunities"></a>其他销售机会

你可以选择加入 Microsoft 支持的营销和销售渠道。 在合作伙伴中心创建产品/服务时，你将在此过程结束时看到两个选项卡：

- **通过 CSP 转售**：使用此选项可允许 Microsoft 云解决方案提供商 (CSP) 合作伙伴将你的解决方案作为捆绑产品/服务的一部分转售。 有关此计划的详细信息，请参阅[云解决方案提供商计划](cloud-solution-providers.md)。

- **与 Microsoft 联合销售**：此选项可让 Microsoft 销售团队在评估其客户需求时考虑符合你的 IP 联合销售资格的解决方案。 有关 IP 联合销售资格的详细信息，请参阅[联合销售状态的要求](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)。 有关如何准备产品/服务以供评估的详细信息，请参阅[合作伙伴中心的联合销售选项](co-sell-configure.md)。

## <a name="next-steps"></a>后续步骤

- [如何在商业市场中创建 SaaS 产品/服务](create-new-saas-offer.md)
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)
