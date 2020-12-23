---
title: 如何在商业应用商店中创建 Azure 应用程序产品/服务
description: 了解如何在 Azure Marketplace 中创建新的 Azure 应用程序产品/服务，或通过云解决方案提供商 (CSP) 计划使用 Microsoft 合作伙伴中心中的商业应用商店门户。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369961"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>如何在商业应用商店中创建 Azure 应用程序产品/服务

作为一家商业市场发布者，你可以创建一个 Azure 应用程序产品/服务，使潜在客户可以购买你的解决方案。 本文介绍为 Microsoft 商业应用商店创建 Azure 应用程序产品/服务的过程。

如果尚未执行此操作，请阅读 [规划适用于商业市场的 Azure 应用程序产品/服务](plan-azure-application-offer.md)。 它将提供资源，帮助您收集创建产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左侧导航菜单中，选择 " **商业市场**  >  **概述** "。

1. 在“概述”页上，选择“+ 新建产品/服务” > “Azure 应用程序”。

    ![阐释左侧导航菜单。](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. 在 " **新建产品/服务** " 对话框中，输入 **产品 ID** 。 这是你帐户中的每个产品/服务的唯一标识符。 此 ID 在商业市场列表和 Azure 资源管理器模板的 URL 中可见（如果适用）。 例如，如果在此框中输入 "测试/服务-1"，则 "产品/服务" 网址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

     * 帐户中的每个提议都必须具有唯一的产品 ID。
     * 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
     * 在选择“创建”后，就无法更改产品/服务 ID 了。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

     * 此名称仅显示在合作伙伴中心，与产品/服务名称和向客户显示的其他值不同。
     * 选择“创建”后，无法更改产品/服务别名。

1. 若要生成产品/服务并继续，请选择 "  **创建** "。

## <a name="configure-your-azure-application-offer-setup-details"></a>配置 Azure 应用程序产品/服务设置详细信息

在 " **产品/服务设置** " 选项卡上的 " **设置详细信息** " 下，选择是否配置测试驱动器。 你还可以将客户关系管理 (CRM) 系统与商业 marketplace 产品/服务连接。

### <a name="enable-a-test-drive-optional"></a> (可选) 启用测试驱动器

测试驱动器是向潜在客户展示您的产品/服务的一种很好的方式，通过在固定的小时数内提供对预配置环境的访问权限。 提供一个测试驱动器将导致转换速率增加，并生成高度合格的潜在顾客。 若要了解有关测试驱动器的详细信息，请参阅 [测试驱动器](plan-azure-application-offer.md#test-drive)。

#### <a name="to-enable-a-test-drive"></a>启用测试驱动器

- 在 " **测试驱动器** " 下，选中 " **启用测试驱动器** " 复选框。

### <a name="customer-lead-management"></a>客户主管管理

将客户关系管理 (CRM) 系统与你的商业 marketplace 产品/服务相连接，以便在客户表达兴趣或部署你的产品时可以接收客户联系信息。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>在合作伙伴中心配置连接详细信息

1. 在 " **客户主管** " 下，选择 " **连接** " 链接。
1. 在 " **连接详细信息** " 对话框中，从列表中选择潜在顾客目标。
1. 填写显示的字段。 有关详细步骤，请参阅以下文章：

   - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [将你的产品/服务配置为将潜在顾客发送到 dynamics 365 客户参与](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (以前的 Dynamics CRM Online) 
   - [配置你的产品/服务以将潜在客户发送到 HTTPS 终结点](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [配置你的产品/服务以将潜在顾客发送到 Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [配置你的产品/服务以将潜在顾客发送到 Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要验证提供的配置，请选择 " **验证** " 链接（如果适用）。
1. 若要关闭对话框，请选择 " **连接** "。
1. 选择 " **保存草稿** "，然后继续下一个选项卡： "属性"。

> [!NOTE]
> 请确保与潜在客户目标的连接保持最新，以便不会丢失任何潜在顾客。 请确保在更改了某些内容时更新这些连接。

## <a name="next-steps"></a>后续步骤

- [如何配置 Azure 应用程序产品/服务属性](create-new-azure-apps-offer-properties.md)
