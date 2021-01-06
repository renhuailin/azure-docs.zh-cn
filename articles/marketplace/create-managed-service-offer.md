---
title: 如何在 Microsoft 商业应用商店中创建托管服务产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心的 "商用 Marketplace" 计划为 Azure Marketplace 创建新的托管服务产品/服务。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918105"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>如何为商业应用商店创建托管服务产品/服务

本文介绍如何使用合作伙伴中心为 Microsoft 商业应用商店创建托管服务产品/服务。

若要发布托管服务产品/服务，你必须在云平台中获得金牌或银 Microsoft 资格。 如果尚未执行此操作，请阅读 [为商业市场规划托管服务产品/服务](./plan-managed-service-offer.md)。 在合作伙伴中心创建产品/服务时，它将帮助你准备所需的资产。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场**  >  **概述**"。
3. 在 "概述" 选项卡上，选择 " **+ 新建产品**/  >  **服务**"。

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="展示了左侧导航菜单。":::

4. 在 " **新建产品/服务** " 对话框中，输入 **产品 ID**。 这是你帐户中的每个产品/服务的唯一标识符。 此 ID 在商业市场列表和 Azure 资源管理器模板的 URL 中可见（如果适用）。 例如，如果在此框中输入 "测试/服务-1"，则 "产品/服务" 网址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

    * 帐户中的每个提议都必须具有唯一的产品 ID。
    * 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
    * 在选择“创建”后，就无法更改产品/服务 ID 了。

5. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。 它在联机商店中不可见，并且不同于向客户显示的产品/服务名称。
6. 若要生成产品/服务并继续，请选择 " **创建**"。

## <a name="configure-lead-management"></a>配置潜在顾客管理

将客户关系管理 (CRM) 系统与你的商业 marketplace 产品/服务相连接，以便在客户对你的咨询服务感兴趣时接收客户联系信息。 你可以在创建产品/服务的过程中或之后随时修改此连接。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作伙伴中心配置潜在客户管理，请执行以下操作：

1. 在合作伙伴中心，请参阅 **产品/服务设置** 选项卡。
2. 在 " **客户主管**" 下，选择 " **连接** " 链接。
3. 在 " **连接详细信息** " 对话框中，从列表中选择潜在顾客目标。
4. 填写显示的字段。 有关详细步骤，请参阅以下文章：

    * [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [将你的产品/服务配置为将潜在顾客发送到 dynamics 365 客户参与](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (以前的 Dynamics CRM Online) 
    * [配置你的产品/服务以将潜在客户发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 若要验证提供的配置，请选择 " **验证" 链接**。
6. 配置连接详细信息后，选择 " **连接**"。
7. 选择“保存草稿”。

在合作伙伴中心提交产品/服务进行发布后，我们将验证连接并向你发送测试线索。 当你在推出产品/服务之前对其进行预览时，请在预览环境中尝试自行购买产品/服务，以测试你的潜在客户连接。

> [!TIP]
> 请务必不断更新与潜在客户目标的连接，以免失去任何潜在客户。

## <a name="configure-offer-properties"></a>配置产品/服务属性

在合作伙伴中心的产品/服务的 "属性" 页上，你将定义适用于你的产品/服务和法律合同的类别。 此信息确保你的托管服务在在线商店中正确显示，并向正确的客户提供。

### <a name="select-a-category"></a>选择类别

在 " **类别**" 下，选择至少一个和五个类别，将产品/服务分组到适当的商业市场搜索区域。

### <a name="provide-terms-and-conditions"></a>提供条款和条件

在 " **法律**" 下，请提供此优惠的条款和条件。 在使用产品/服务之前，客户将需要接受这些条款。 还可以提供能够在其中找到条款和条件的 URL。

选择“保存草稿”，然后继续操作。

## <a name="next-step"></a>后续步骤

* [配置托管服务产品/服务列表](./create-managed-service-offer-listing.md)