---
title: 如何在 Microsoft 商业应用商店中创建咨询服务产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心的 "商用 Marketplace" 计划为 Microsoft AppSource 或 Azure Marketplace 创建新的咨询服务优惠。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754326"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>如何在商业应用商店中创建咨询服务产品/服务

本文介绍如何使用合作伙伴中心为 Microsoft 商用 marketplace 创建咨询服务产品/服务。 

## <a name="before-you-begin"></a>开始之前

若要发布咨询服务产品/服务，必须满足某些符合性要求，才能在现场提供专业知识。 如果尚未执行此操作，请阅读 [为商业市场规划咨询服务产品/服务](./plan-consulting-service-offer.md)。 它介绍了咨询服务的先决条件，以及在使用合作伙伴中心创建产品/服务时所需的资产。

## <a name="create-a-new-consulting-service-offer"></a>创建新的咨询服务套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2.  在左侧导航菜单中，选择 " **商业市场**  >  **概述** "。
3.  在 "概述" 选项卡上，选择 " **+ 新建产品** /  >  **服务** "。

    ![展示了左侧导航菜单。](./media/new-offer-consulting-service.png)

4. 在 " **新建产品/服务** " 对话框中，输入 **产品 ID** 。 此 ID 在 "商业应用商店" 列表的 URL 中可见。 例如，如果在此框中输入 "测试/服务-1"，则 "产品/服务" 网址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

    * 帐户中的每个提议都必须具有唯一的产品 ID。
    * 只使用小写字母和数字。 产品/服务 ID 可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。
    * 选择 " **创建** " 后，无法更改产品/服务 ID。

5. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。 它在联机商店中不可见，并且不同于向客户显示的产品/服务名称。
6. 若要生成产品/服务并继续，请选择 " **创建** "。

## <a name="configure-lead-management"></a>配置潜在顾客管理

将客户关系管理 (CRM) 系统与你的商业 marketplace 产品/服务相连接，以便在客户对你的咨询服务感兴趣时接收客户联系信息。 你可以在创建产品/服务的过程中或之后随时修改此连接。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作伙伴中心配置潜在客户管理，请执行以下操作：

1.  在合作伙伴中心，请参阅 **产品/服务设置** 选项卡。
2.  在 " **客户主管** " 下，选择 " **连接** " 链接。
3.  在 " **连接详细信息** " 对话框中，从列表中选择潜在顾客目标。
4.  填写显示的字段。 有关详细步骤，请参阅以下文章：

    * [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [将你的产品/服务配置为将潜在顾客发送到 dynamics 365 客户参与](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (以前的 Dynamics CRM Online) 
    * [配置你的产品/服务以将潜在客户发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  若要验证提供的配置，请选择 " **验证" 链接** 。
6.  配置连接详细信息后，选择 " **连接** "。
7.  选择“保存草稿”。

在合作伙伴中心提交产品/服务进行发布后，我们将验证连接并向你发送测试线索。 当你在推出产品/服务之前对其进行预览时，请在预览环境中尝试自行购买产品/服务，以测试你的潜在客户连接。

> [!TIP]
> 请务必不断更新与潜在客户目标的连接，以免失去任何潜在客户。

## <a name="next-steps"></a>后续步骤

* [如何配置咨询服务产品/服务属性](./create-consulting-service-offer-properties.md)