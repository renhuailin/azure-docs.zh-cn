---
title: 在 Microsoft AppSource 中创建 Dynamics 365 Business Central 产品/服务
description: 如何在 Microsoft AppSource 中创建 Dynamics 365 for Business Central 产品/服务。 在 AppSource 或通过云解决方案提供商 (CSP) 计划列出或出售你的产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 99c36354334701dcd4c7c30c5fd5039c13885525
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358701"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>创建 Dynamics 365 for Business Central 产品/服务

本文介绍如何创建新的 Dynamics 365 Business Central 产品/服务。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) 是一个企业资源计划 (ERP) 服务系统，用于处理财务、运营、供应链、客户关系管理 (CRM)、项目管理和电子商务等各种业务流程。 高级包还支持经典部署模型和制造。 Dynamics 365 Business Central 的所有产品/服务都必须完成我们的认证过程。

开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建）。 确保你的帐户已注册加入商业市场计划。

>[!NOTE]
> 发布了产品/服务后，只有在重新提交产品/服务进行发布的情况下，才会在合作伙伴中心和在线商店中更新对产品/服务的编辑信息。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述” 。
3. 在“概述”页面上，依次选择“+新建产品/服务” > “Dynamics 365 Business Central” 。

    ![左侧导航菜单图示。](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 产品 ID 和发布者 ID 的组合长度必须为在 40 个字符以内。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格。 例如，如果发布者 ID 为 `testpublisherid`，用户输入“test-offer-1”，则产品/服务 Web 地址为 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`。
- 选择“创建”后，便无法更改此 ID。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称没有用于市场，与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，便无法更改此名称。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-setup"></a>产品/服务设置

### <a name="alias"></a>Alias

输入一个描述性名称，此名称将仅用于在合作伙伴中心内指示此产品/服务。 此名称（预先填入在创建产品/服务时输入的内容）不会在市场中使用，并且与向客户显示的产品/服务名称不同。 如果以后要更新产品/服务名称，请参阅[产品/服务列表](#offer-listing)页。

### <a name="setup-details"></a>设置详细信息

选择适用于你的产品/服务的“包类型”：

- “附加产品”应用可扩展 Dynamics 365 Business Central 的体验和现有功能。 有关详细信息，请参阅[附加产品应用](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)。
- 如果必须在 Dynamics 365 Business Central 与第三方解决方案或服务之间建立点到点连接，则可在方案中使用“连接”应用。 有关详细信息，请参阅 [Connect 应用](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)。

如要了解 **潜在客户如何与此列表产品/服务进行交互**，请选择要用于此产品/服务的选项。

- **立即获取（免费）** - 列出向客户免费提供的产品/服务。
- **免费试用（列表）** - 向客户列出包含免费试用版链接的产品/服务。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

    > [!NOTE]
    > 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

- “联系我” - 通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 若要了解详细信息，请从[什么是体验版](../what-is-test-drive.md)开始阅读。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续操作。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="categories"></a>类别

选择类别和子类别将产品/服务放入相应的市场搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个，最多两个类别，包括主类别和辅助类别（可选）。
- 每个主类别和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择“不适用”。

请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。

### <a name="industries"></a>行业

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页上看到此版本列出。

### <a name="terms-and-conditions"></a>条款和条件

在此处提供你自己的法律条款和条件。 还可以提供可在其中找到条款和条件的地址。 客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务列表

在此页面上定义产品/服务详细信息，例如产品/服务名称、说明、链接和联系人。

> [!NOTE]
> 仅可用一种语言提供产品/服务列表的详细信息。 如果不要求用英语，则只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”就可以。 提供“实用链接 URL” 来提供产品/服务列表内容中使用的语言以外的语言内容，也是可以接受的。

以下示例说明了产品/服务信息在 Microsoft AppSource 中的显示方式（所有的价格仅用于举例目的，而不代表实际成本）：
<!-- update screen? -->
:::image type="content" source="media/example-d365-business-central.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>调用说明

1. 徽标
2. 产品
3. 类别
4. 支持地址（链接）
5. 使用条款
6. 隐私策略
7. 产品名称
8. 总结
9. 说明
10. 屏幕截图/视频

### <a name="marketplace-details"></a>市场详细信息

在此处输入的“名称”作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

提供可能会在“搜索结果摘要”中使用的产品/服务简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

可以视需要选择输入最多三个“搜索关键字”，以帮助客户在市场中查找产品/服务。 为了获得最佳结果，请在说明中也使用这些关键字。

若要让客户知道“哪些产品可配合应用使用”，请在此处输入最多三个产品名称。

### <a name="helpprivacy-urls"></a>帮助/隐私 URL

输入“应用的帮助链接” (URL)，客户可通过该链接了解有关产品/服务的详细信息。 支持 URL不能与帮助链接相同。

在组织的隐私策略中输入“隐私策略链接”(URL)。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="contact-information"></a>联系信息

提供“支持联系人”和“工程联系人”的姓名、电子邮箱和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

在“支持联系人”部分中的“支持 URL”内，提供 CSP 合作伙伴可以在其中找到你产品/服务的支持的 URL。 支持 URL 不能与帮助 URL 相同。

### <a name="supporting-documents"></a>支持性文档

在此处提供至少一个（最多三个）相关的 PDF 格式的营销文档，如白皮书、小册子、清单或演示文稿。

### <a name="marketplace-media"></a>市场媒体

提供在向客户展示你的产品/服务时使用的徽标和图像。 所有图像都必须为 PNG 格式。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="logos"></a>徽标

提供大徽标的 PNG 文件。 合作伙伴中心将使用它来创建其他所需的大小。 稍后，可以选择将其替换为其他图像。

这些徽标会在列表的不同位置上使用：

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要 3 个屏幕截图，最多可添加 5 个。 所有屏幕截图都必须为 1280 x 720 像素，并且为 PNG 格式。

#### <a name="videos"></a>视频

可以选择添加最多四个演示产品/服务的视频。 视频必须托管在外部站点上。 对于每个视频，请输入视频名称、地址和视频缩略图（1280 x 720 像素）。

有关其他市场列表资源，请参阅[市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)。

选择“保存草稿”，然后继续操作。

## <a name="availability"></a>可用性

可使用此页定义在何处以及如何提供产品/服务。

### <a name="markets"></a>市场

若要指定提供产品/服务的市场，请选择“编辑市场”以显示“市场选择”弹出窗口。

请至少选择一个市场。 选择“全选”可使你的产品/服务在每个可能的市场中提供，也可仅选择要添加的特定市场。 完成后，选择“保存”。

你此处所做的选择仅适用于新的获取；如果某人已在特定市场中获取了你的应用，而你后来又删除了此市场，那么已经在此市场中获取了你的产品/服务的人可以继续使用它，但此市场中的任何新客户都无法获取你的产品/服务。

> [!IMPORTANT]
> 你需要负责满足任何本地法律要求，即使这些要求未在此处或合作伙伴中心列出。 即使选择所有市场，本地法律和限制或其他因素也可能会阻止在某些国家和地区列出某些产品/服务。

### <a name="preview-audience"></a>预览版受众

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览版受众的成员可以将此隐藏密钥用作令牌，以查看市场中产品/服务的预览版。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除该隐藏密钥并再次发布。

选择“保存草稿”，然后继续操作。

## <a name="technical-configuration"></a>技术配置

此页定义用于连接到产品/服务的技术详细信息。 如果最终客户选择获取你的产品/服务，我们可以通过此连接为其预配你的产品/服务。

### <a name="file-upload"></a>文件上传

如果在上面选择了“附加产品”，则将上传产品/服务的包文件，以及有其依赖项的任何扩展包文件。

#### <a name="extension-package-file"></a>扩展包文件

上传产品/服务的扩展包文件 (.app)。

#### <a name="library-extension-package-file"></a>库扩展包文件

如果你的产品/服务必须与将不在市场中发布的其他扩展一起安装，则需要此文件。 如果是，请在此处上传它的 .app 文件。

>[!NOTE]
>不再使用该依赖项包文件。 改为上载库扩展包文件。

选择“保存草稿”，然后继续操作。

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>补充内容

通过此页，可以提供产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

### <a name="target-release"></a>目标版本

指出你的解决方案面向哪个版本的 Microsoft Dynamics Business Central：“当前版本”、“下一主要版本”或“下一次要版本。 我们可通过此信息适当地测试你的解决方案。

### <a name="supported-editions"></a>支持的版本

如果你的产品/服务需要 Microsoft Dynamics 365 Business Central 高级版，请仅选择“Premium”。 否则，请同时选择“Essentials”和“Premium” 。

### <a name="key-usage-scenario"></a>主要使用方案

上传一个 PDF 文件，其中列出产品/服务的主要使用方案。 在我们批准你的产品/服务加入市场之前，此处列出的所有方案可能都已经过我们的验证团队的验证。

### <a name="test-accounts"></a>用户帐户

如果我们的认证团队需要测试帐户才能恰当审阅你的产品/服务，请随附测试帐户信息上传一个 .pdf、.doc 或 .docx 文件。

### <a name="app-tests-automation"></a>应用测试自动化

如果你的产品/服务是附加产品应用，则必须上传一个应用测试自动化文件 (.app)。 此文件不适用于 Connect 应用。

选择“保存草稿”，然后继续操作。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交产品/服务进行预览

完成产品/服务的所有必需部分后，请选择门户右上角的“查看和发布”。

如果这是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
    - “未开始” - 尚未涉及该部分，需要完成。
    - “未完成” - 此部分包含需要修复的错误或需要提供详细信息。 返回相应部分并进行更新。
    - “完成” - 此部分已完成，已提供所有必需的数据，并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 在“认证说明”部分，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 当产品/服务的预览版本可供查看和审批时，我们将向你发送一封电子邮件通知。 返回合作伙伴中心并选择“上线”，公开发布你的产品/服务。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)