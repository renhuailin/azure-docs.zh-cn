---
title: 如何在 Microsoft 商业市场中创建 SaaS 产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心的商业市场门户创建新的服务型软件 (SaaS) 产品/服务，以在 Microsoft AppSource、Azure 市场或通过云解决方案提供商 (CSP) 计划列出或销售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130062"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>如何在商业市场中创建 SaaS 产品/服务

作为商业市场发布者，你可以创建服务型软件 (SaaS) 产品/服务，以便潜在客户购买你的基于 SaaS 的技术解决方案。 本文介绍针对 Microsoft 商业市场创建 SaaS 产品/服务的过程。

## <a name="before-you-begin"></a>开始之前

如果尚未执行此操作，请参阅[为商业市场计划 SaaS 产品/服务](plan-saas-offer.md)。 其中说明了 SaaS 应用的技术要求，并列出了创建产品/服务时所需的信息和资产。 除非你计划在商业市场中发布简单的列表（**与我联系** 列表选项），否则 SaaS 应用程序必须满足有关身份验证的技术要求。

## <a name="create-a-new-saas-offer"></a>创建新的 SaaS 产品/服务

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，选择“商业市场” > “概述” 。
1. 在“概述”选项卡中，选择“+ 新建产品/服务” > “服务型软件”  。

   :::image type="content" source="media/new-offer-saas.png" alt-text="显示左侧导航菜单和“新建产品/服务”列表。":::

1. 在“新建产品/服务”对话框中，输入“产品/服务 ID”。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
   + 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
   + 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
   + 选择“创建”后，无法更改产品/服务 ID。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

   + 此名称在商业市场中不可见，它与向客户显示的产品/服务名称和其他值不同。
   + 选择“创建”后，无法更改产品/服务别名。
1. 要生成产品/服务并继续操作，请选择“创建”。

## <a name="configure-your-saas-offer-setup-details"></a>配置 SaaS 产品/服务设置详细信息

在“产品/服务设置”选项卡的“设置详细信息”下，选择是通过 Microsoft 销售你的产品/服务还是独立管理你的交易。 通过 Microsoft 销售的产品/服务称为“可交易的产品/服务”，意味着 Microsoft 代表发布者销售软件许可证。 有关这些选项的详细信息，请参阅[列出选项](plan-saas-offer.md#listing-options)和[确定发布选项](determine-your-listing-type.md)。

1. 若要通过 Microsoft 销售并由我们辅助交易，请选择“是”。 继续[启用体验版](#enable-a-test-drive-optional)。

1. 若要通过商业市场列出你的产品/服务但独立处理交易，请选择“否”，然后执行以下操作之一：
   + 若要为你的产品/服务提供免费订阅，请选择“立即获取(免费)”。 然后在显示的“产品/服务 URL”框中输入 URL（以 http 或 https开头），通过该 URL，客户可以[使用 Azure Active Directory (Azure AD) 进行一键式身份验证](azure-ad-saas.md)以获取试用版。 例如 `https://contoso.com/saas-app`。
   + 若要提供 30 天的免费试用版，请选择“免费试用版”，然后在显示的“试用版 URL”框中输入 URL（以 http 或 https 开头），通过该 URL，客户可以[使用 Azure Active Directory (Azure AD) 进行一键式身份验证](azure-ad-saas.md)来访问免费试用版。 例如 `https://contoso.com/trial/saas-app`。
   + 若要让潜在客户联系你购买产品/服务，请选择“与我联系”。

### <a name="enable-a-test-drive-optional"></a>启用体验版（可选）

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在顾客。 若要详细了解体验版，请参阅[什么是体验版？](./what-is-test-drive.md)。

> [!TIP]
> 体验版不同于免费试用版。 你可以提供体验版、免费试用版，或提供此两者。 这两种版本都可以让客户在固定时间段内使用你的解决方案。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

**若要启用体验版，请执行以下操作**
1.  在“体验版”下，选中“启用体验版”复选框。
1.  从显示的列表中选择体验版类型。

### <a name="configure-lead-management"></a>配置潜在顾客管理

将客户关系管理 (CRM) 系统与你的商业市场产品/服务相连接，以便在客户表示有兴趣或部署你的产品时你可以接收到客户的联系信息。 你可以在创建产品/服务期间或之后随时修改此连接。

> [!NOTE]
> 如果通过 Microsoft 销售产品/服务，或者选择了“与我联系”列表选项，则必须配置潜在顾客管理。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>在合作伙伴中心配置连接详细信息

1.  在“潜在顾客”下，选择“连接”链接。 
1. 在“连接详细信息”对话框中，从列表选择潜在顾客目标。
1. 填写显示的字段。 有关详细步骤，请参阅以下文章：

   - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [配置产品/服务以将潜在顾客发送到 Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)（以前称为 Dynamics CRM Online）
   - [配置产品/服务以将潜在顾客发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要验证你提供的配置，请选择“验证”链接。
1. 要关闭该对话框，请选择“确定”。

## <a name="next-steps"></a>后续步骤

- [如何配置 SaaS 产品/服务属性](create-new-saas-offer-properties.md)