---
title: 如何在 Microsoft 商业市场创建咨询服务产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心的商业市场计划为 Microsoft AppSource 或 Azure 市场创建新的咨询服务产品/服务。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754326"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>如何在商业市场中创建咨询服务产品/服务

本文介绍如何使用合作伙伴中心为 Microsoft 商业市场创建咨询服务产品/服务。 

## <a name="before-you-begin"></a>开始之前

若要发布咨询服务产品/服务，必须满足某些合格要求，才能证明你所在领域的专业知识。 如果尚未执行此操作，请参阅[为商业市场计划咨询服务产品/服务](./plan-consulting-service-offer.md)。 它介绍了咨询服务的先决条件，以及在使用合作伙伴中心创建产品/服务时所需的资产。

## <a name="create-a-new-consulting-service-offer"></a>创建新的咨询服务套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2.  在左侧导航菜单中，选择“商业市场” > “概述” 。
3.  在“概述”选项卡上，选择“+ 新建产品/服务” > “咨询服务” 。

    ![展示了左侧导航菜单。](./media/new-offer-consulting-service.png)

4. 在“新建产品/服务”对话框中，输入“产品/服务 ID” 。 此 ID 在商业市场列表的 URL 中可见。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

    * 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
    * 只使用小写字母和数字。 该产品/服务 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
    * 选择“创建”后，无法更改产品/服务 ID。

5. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。 它在在线商店中不可见，并且不同于向客户显示的产品/服务名称。
6. 要生成产品/服务并继续操作，请选择“创建”。

## <a name="configure-lead-management"></a>配置潜在顾客管理

将客户关系管理 (CRM) 系统与你的商业市场产品/服务相连接，以便在客户表示对你的咨询服务有兴趣时你可以接收到客户的联系信息。 你可以在创建产品/服务期间或之后随时修改此连接。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作伙伴中心配置潜在顾客管理，请执行以下操作：

1.  在合作伙伴中心，转到“产品/服务设置”选项卡。
2.  在“潜在顾客”下，选择“连接”链接 。
3.  在“连接详细信息”对话框中，从列表选择潜在顾客目标。
4.  填写显示的字段。 有关详细步骤，请参阅以下文章：

    * [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [配置产品/服务以将潜在顾客发送到 Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)（以前称为 Dynamics CRM Online）
    * [配置产品/服务以将潜在顾客发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  若要验证你提供的配置，请选择“验证链接”。
6.  配置连接详细信息后，选择“连接”。
7.  选择“保存草稿”。

当你提交产品/服务以便在合作伙伴中心内发布后，我们会验证连接，并向你发送测试潜在顾客。 若要在上线前预览产品/服务，请通过亲自尝试在预览环境中购买产品/服务来测试潜在顾客连接。

> [!TIP]
> 请务必不断更新与潜在客户目标的连接，以免失去任何潜在客户。

## <a name="next-steps"></a>后续步骤

* [如何配置咨询服务产品/服务属性](./create-consulting-service-offer-properties.md)