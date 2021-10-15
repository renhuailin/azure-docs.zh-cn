---
title: 在 Microsoft AppSource（Azure 市场）上创建 Dynamics 365 for Operations 产品/服务
description: 在 Microsoft AppSource（Azure 市场）上创建 Dynamics 365 for Operations 产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: eadbeee57960577f12f880b4582e820eddb63e3c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234369"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 产品/服务

本文介绍如何创建 [Dynamics 365 for Operations](https://dynamics.microsoft.com/finance-and-operations) 产品/服务。 此产品/服务类型是一项企业资源计划 (ERP) 服务，支持高级运营、财务、制造和供应链管理。 Dynamics 365 的所有产品/服务都必须通过我们的认证流程。

开始之前，先在[合作伙伴中心](./create-account.md)创建一个商业市场帐户，并确保该帐户已在商业市场计划中注册。

## <a name="before-you-begin"></a>开始之前

查看[规划 Dynamics 365 产品/服务](marketplace-dynamics-365.md)。 其中说明了此产品/服务的技术需求，并列出了创建该产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2166002)。

1. 在“主页”上，选择“市场产品/服务”磁贴。

    [ ![说明了合作伙伴中心主页上的“市场产品/服务”磁贴。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. 在“市场产品/服务”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Operations”。

    [ ![“市场产品/服务”页上“新建产品/服务”按钮的屏幕截图，其中突出显示了“Dynamics 365 for Operations”产品/服务类型。](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png#lightbox)

> [!IMPORTANT]
> 发布某个产品/服务后，在合作伙伴中心对其进行的任何编辑都不会出现在 Microsoft AppSource 中，除非重新发布该产品和服务。 对产品/服务进行更改后，请确保一律重新发布该产品/服务。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
1. 在“概述”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Operations”。

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-operations.png" alt-text="左窗格菜单选项和“新产品/服务”按钮。":::

> [!IMPORTANT]
> 发布某个产品/服务后，在合作伙伴中心对其进行的任何编辑都不会出现在 Microsoft AppSource 中，除非重新发布该产品和服务。 对产品/服务进行更改后，请确保一律重新发布该产品/服务。

---

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在产品/服务及 Azure 资源管理器模板的 web 地址中看到此 ID（如有）。
- 只使用小写字母和数字。 该 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称不用于 AppSource。 它不同于向客户显示的产品/服务名称和其他值。
- 选择“创建”后，便无法更改此名称。

选择“创建”，以生成产品/服务。 将打开合作伙伴中心的“产品/服务设置”页。

## <a name="alias"></a>Alias

输入描述性名称，此名称将仅用于在合作伙伴中心内指示此产品/服务。 此产品/服务的别名（预先填入你在创建该产品/服务时输入的内容）不会在市场中使用，并且与向客户显示的产品/服务名称不同。 如果以后要更新产品/服务名称，请参阅[产品/服务列表](dynamics-365-operations-offer-listing.md)页面。

## <a name="setup-details"></a>设置详细信息

如要了解“潜在客户如何与此列表产品/服务进行交互”，请选择要用于此产品/服务的选项。

- **立即获取（免费）** - 列出向客户免费提供的产品/服务。
- **免费试用（列表）** - 向客户列出包含免费试用版链接的产品/服务。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

    > [!NOTE]
    > 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

- **联系我** - 通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

## <a name="test-drive"></a>体验版

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在顾客。 若要了解详细信息，请从[什么是体验版？](what-is-test-drive.md)开始阅读。

> [!TIP]
> 体验版不同于免费试用版。 你可以提供体验版、免费试用版，或提供此两者。 这两个版本都让客户使用你的解决方案固定的一段时间。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

若要启用体验版，请选中“启用体验版”复选框，然后选择“体验版类型” 。 稍后你将配置体验版。 使用体验版，还必须为潜在客户将产品/服务配置为 CRM 系统（请参阅下一部分）。 若要从产品/服务中删除体验版，请清除此复选框。

## <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

有关详细信息，请参阅[来自商业市场产品/服务的客户销售线索](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="business-applications-isv-program"></a>Business Applications ISV 计划

你的产品/服务最初在标准层进行注册。

在发布产品/服务之前，请完成[联合销售模块](https://aka.ms/BizAppsISVProgram)。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“属性”。 

## <a name="next-steps"></a>后续步骤

- 配置产品/服务[属性](dynamics-365-operations-properties.md)
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)
