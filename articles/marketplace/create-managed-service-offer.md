---
title: 在 Azure 市场中创建托管服务产品/服务
description: 创建面向 Azure 市场的新托管服务产品/服务。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/12/2021
ms.openlocfilehash: 0d5c178010012546bfcf786be7106f980289d6eb
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204475"
---
# <a name="create-a-managed-service-offer-for-the-commercial-marketplace"></a>创建面向 Microsoft 商业市场的托管服务产品/服务

本文介绍如何使用合作伙伴中心创建面向 Microsoft 商业市场的托管服务产品/服务。

若要发布托管服务产品/服务，必须在云平台中获得黄金或白银 Microsoft 资质。 参阅[规划面向商业市场的托管服务产品/服务](./plan-managed-service-offer.md)（如果尚未这样做）。 此文可帮助你准备好在合作伙伴中心创建产品/服务时所需的资产。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述” 。
3. 在“概述”选项卡上，选择“+ 新建产品/服务” > “托管服务”。

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="展示了左侧导航菜单。":::

4. 在“新建产品/服务”对话框中，输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

    - 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
    - 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
    - 在选择“创建”后，就无法更改产品/服务 ID 了。

5. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。 它在在线商店中不可见，并且不同于向客户显示的产品/服务名称。
6. 要生成产品/服务并继续操作，请选择“创建”。

## <a name="setup-details"></a>设置详细信息

本部分不适用于此产品/服务类型。

## <a name="customer-leads"></a>潜在顾客

将客户关系管理 (CRM) 系统与你的商业市场产品/服务相连接，以便在客户表示对你的咨询服务有兴趣时你可以接收到客户的联系信息。 你可以在创建产品/服务期间或之后随时修改此连接。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作伙伴中心配置潜在顾客管理，请执行以下操作：

1. 在合作伙伴中心，转到“产品/服务设置”选项卡。
2. 在“潜在顾客”下，选择“连接”链接 。
3. 在“连接详细信息”对话框中，从列表选择潜在顾客目标。
4. 填写显示的字段。 有关详细步骤，请参阅以下文章：

    - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    - [配置产品/服务以将潜在客户发送到 Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)（以前称为 Dynamics CRM Online）
    - [配置产品/服务以将潜在客户发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    - [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    - [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 若要验证你提供的配置，请选择“验证链接”。
6. 配置连接详细信息后，选择“连接”。
7. 选择“保存草稿”。

当你提交产品/服务以便在合作伙伴中心内发布后，我们会验证连接，并向你发送测试潜在顾客。 若要在上线前预览产品/服务，请通过亲自尝试在预览环境中购买产品/服务来测试潜在顾客连接。

> [!TIP]
> 请务必不断更新与潜在客户目标的连接，以免失去任何潜在客户。

选择“保存草稿”，然后转到下一选项卡“属性” 。

## <a name="next-step"></a>下一步

- 配置产品/服务[属性](create-managed-service-offer-properties.md)