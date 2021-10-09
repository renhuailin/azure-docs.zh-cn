---
title: 在商业市场中创建 SaaS 产品/服务
description: 在 Microsoft AppSource、Azure 市场中或通过 Azure 市场中的云解决方案提供商 (CSP) 计划创建新的服务型软件 (SaaS) 产品/服务进行上市销售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 5da6232a9bedeeb8228caecc79c7a7160630a8cd
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080802"
---
# <a name="create-a-saas-offer"></a>创建 SaaS 产品/服务

作为商业市场发布者，你可以创建服务型软件 (SaaS) 产品/服务，以便潜在客户购买你的基于 SaaS 的技术解决方案。 本文介绍针对 Microsoft 商业市场创建 SaaS 产品/服务的过程。

## <a name="before-you-begin"></a>在开始之前

如果尚未执行此操作，请阅读[计划 SaaS 产品/服务](plan-saas-offer.md)。 其中说明了 SaaS 应用的技术要求，并列出了创建产品/服务时所需的信息和资产。 除非你计划在商业市场中发布简单的列表（**与我联系** 列表选项），否则 SaaS 应用程序必须满足有关身份验证的技术要求。

> [!IMPORTANT]
> 我们建议创建单独的开发/测试 (DEV) 产品/服务，以及单独的生产 (PROD) 产品/服务。 本文介绍如何创建 PROD 产品/服务。 有关创建 DEV 产品/服务的详细信息，请参阅[创建测试 SaaS 产品/服务](create-saas-dev-test-offer.md)。

## <a name="create-a-saas-offer"></a>创建 SaaS 产品/服务

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在“主页”上，选择“市场产品/服务”磁贴。

    [ ![说明了合作伙伴中心主页上的“市场产品/服务”磁贴。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. 在“市场产品/服务”页上，依次选择“+ 新建产品/服务” > “服务型软件”。

    [ ![说明“新建产品/服务”列表中的“SaaS 产品/服务”选项。](./media/new-offer-saas-workspaces.png) ](./media/new-offer-saas-workspaces.png#lightbox)

1. 在“新建服务型软件”对话框中，输入“产品/服务 ID”。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
   + 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
   + 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
   + 选择“创建”后，无法更改产品/服务 ID。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

   + 此名称在商业市场中不可见，它与向客户显示的产品/服务名称和其他值不同。
   + 选择“创建”后，无法更改产品/服务别名。
1. 要生成产品/服务并继续操作，请选择“创建”。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，选择“商业市场” > “概述” 。
1. 在“概述”选项卡中，选择“+ 新建产品/服务” > “服务型软件”  。

   :::image type="content" source="./media/new-offer-saas.png" alt-text="显示左侧导航菜单和“新建产品/服务”列表。":::

1. 在“新建产品/服务”对话框中，输入“产品/服务 ID”。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
   + 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
   + 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
   + 选择“创建”后，无法更改产品/服务 ID。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

   + 此名称在商业市场中不可见，它与向客户显示的产品/服务名称和其他值不同。
   + 选择“创建”后，无法更改产品/服务别名。
1. 要生成产品/服务并继续操作，请选择“创建”。

---

## <a name="configure-your-saas-offer-setup-details"></a>配置 SaaS 产品/服务设置详细信息

在“产品/服务设置”选项卡的“设置详细信息”下，选择是通过 Microsoft 销售你的产品/服务还是独立管理你的交易。 通过 Microsoft 销售的产品/服务称为“可交易的产品/服务”，意味着 Microsoft 代表发布者销售软件许可证。 有关这些选项的详细信息，请参阅[列出选项](plan-saas-offer.md#listing-options)和[确定发布选项](determine-your-listing-type.md)。

1. 若要通过 Microsoft 销售并由我们辅助交易，请选择“是”。 继续[启用体验版](#enable-a-test-drive-optional)。
1. 若要通过商业市场列出你的产品/服务但独立处理交易，请选择“否”，然后执行以下操作之一：
   + 若要为你的产品/服务提供免费订阅，请选择“立即获取(免费)”。 然后在显示的“产品/服务 URL”框中输入 URL（以 http 或 https开头），通过该 URL，客户可以[使用 Azure Active Directory (Azure AD) 进行一键式身份验证](azure-ad-saas.md)以获取试用版。 例如 `https://contoso.com/saas-app`。
   + 若要提供 30 天的免费试用版，请选择“免费试用版”，然后在显示的“试用版 URL”框中输入 URL（以 http 或 https 开头），通过该 URL，客户可以[使用 Azure Active Directory (Azure AD) 进行一键式身份验证](azure-ad-saas.md)来访问免费试用版。 例如 `https://contoso.com/trial/saas-app`。
   + 若要让潜在客户联系你购买产品/服务，请选择“与我联系”。

    > [!NOTE]
    > 如果你的情况发生变化，你可以将已发布的仅列入清单产品/服务转换为通过商业市场出售的产品/服务，但你不能将已发布的可交易产品/服务转换为仅列入清单产品/服务。 相反，必须创建新的仅仅列入清单产品/服务并停止分发已发布的可交易产品/服务。

## <a name="enable-a-test-drive-optional"></a>启用体验版（可选）

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在顾客。 若要详细了解体验版，请参阅[什么是体验版？](./what-is-test-drive.md)。

> [!TIP]
> 体验版不同于免费试用版。 你可以提供体验版、免费试用版，或提供此两者。 这两个版本都让客户使用你的解决方案固定的一段时间。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

### <a name="to-enable-a-test-drive"></a>启用体验版

1. 在“体验版”下，选中“启用体验版”复选框。
1. 从显示的列表中选择体验版类型。

## <a name="configure-lead-management"></a>配置潜在顾客管理

将客户关系管理 (CRM) 系统与你的商业市场产品/服务相连接，以便在客户表示有兴趣或部署你的产品时你可以接收到客户的联系信息。 你可以在创建产品/服务期间或之后随时修改此连接。

> [!NOTE]
> 如果通过 Microsoft 销售产品/服务，或者选择了“与我联系”列表选项，则必须配置潜在顾客管理。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

### <a name="configure-the-connection-details-in-partner-center"></a>在合作伙伴中心配置连接详细信息

[!INCLUDE [Customer leads](includes/customer-leads.md)]

## <a name="configure-microsoft-365-app-integration"></a>配置 Microsoft 365 应用集成

你可通过链接来了解 SaaS 产品/服务的[统一发现和交付](plan-SaaS-offer.md)，以及任何相关的 Microsoft 365 应用使用情况。

### <a name="integrate-with-microsoft-api"></a>与 Microsoft API 集成

1. 如果你的 SaaS 产品/服务未与 Microsoft Graph API 集成，请选择“否”。 请转到“链接已发布的 Microsoft 365 应用使用情况客户端”。  
1. 如果你的 SaaS 产品/服务与 Microsoft Graph API 集成，请选择“是”，然后提供你创建并注册的 Azure Active Directory 应用 ID，以便与 Microsoft Graph API 集成。

### <a name="link-published-microsoft-365-app-consumption-clients"></a>链接已发布的 Microsoft 365 应用使用情况客户端

1. 如果没有适用于你的 SaaS 产品/服务的已发布 Office 外接程序、“团队”应用或 SharePoint Framework 解决方案，请选择“否”。
1. 如果你已发布适用于 SaaS 产品/服务的 Office 外接程序、“团队”应用或 SharePoint Framework 解决方案，请选择“是”，然后选择“+ 添加另一个 AppSource 链接”以添加新链接。   
1. 提供有效的 AppSource 链接。
1. 继续通过选择“+ 添加另一个 AppSource 链接”，并提供有效的 AppSource 链接，添加所有的链接。  
1. SaaS 产品/服务列表页上显示链接产品的顺序由“排名”值指出，你可通过选择、按住并在列表中上下移动 = 图标更改顺序。 
1. 可以通过在“产品”行中选择“删除”来删除链接的产品。  

> [!IMPORTANT]
> 你停止销售链接的产品时，它不会自动取消 SaaS 产品/服务上的链接，你必须从链接产品列表中删除该产品，然后重新提交 SaaS 产品/服务。  

## <a name="next-steps"></a>后续步骤

- [配置 SaaS 产品/服务属性](create-new-saas-offer-properties.md)
