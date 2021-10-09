---
title: 在 Azure 市场中创建 Azure 应用程序产品/服务
description: 使用商业市场门户创建 Azure 应用程序产品/服务，以便通过 Azure 市场或云解决方案提供商 (CSP) 计划上市或销售。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c0226e8ffdd8445818313d6f21255752dc2e5616
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083253"
---
# <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

作为商业市场发布者，你可以创建 Azure 应用程序产品/服务，以便潜在客户购买你的解决方案。 本文介绍针对 Microsoft 商业市场创建 Azure 应用程序产品/服务的过程。

如果尚未执行此操作，请参阅[为商业市场创建 Azure 应用程序产品/服务计划](plan-azure-application-offer.md)。 它将提供资源并帮助你收集创建产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在“主页”上，选择“市场产品/服务”磁贴。

    [ ![说明了合作伙伴中心主页上的“市场产品/服务”磁贴。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. 在“市场产品/服务”页上，依次选择“+ 新建产品/服务” > “Azure 应用程序”。

    [ ![说明了“市场产品/服务”页上的“新建产品/服务”按钮。](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png) ](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png#lightbox)

1. 在“新建 Azure 应用程序”对话框中，输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

     * 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
     * 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
     * 在选择“创建”后，就无法更改产品/服务 ID 了。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

     * 此名称仅在合作伙伴中心中可见，它与向客户显示的产品/服务名称和其他值不同。
     * 选择“创建”后，无法更改产品/服务别名。

1. 要生成产品/服务并继续操作，请选择“创建”。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左侧导航菜单中，选择“商业市场” > “概述” 。

1. 在“概述”页上，选择“+ 新建产品/服务” > “Azure 应用程序”。

    ![阐释左侧导航菜单。](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. 在“新建产品/服务”对话框中，输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。 此 ID 在商业市场列表的 URL 以及 Azure 资源管理器模板中可见（如果适用）。 例如，如果你在此框中输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

     * 帐户中的每个产品/服务都必须具有唯一的产品/服务 ID。
     * 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
     * 在选择“创建”后，就无法更改产品/服务 ID 了。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

     * 此名称仅在合作伙伴中心中可见，它与向客户显示的产品/服务名称和其他值不同。
     * 选择“创建”后，无法更改产品/服务别名。

1. 要生成产品/服务并继续操作，请选择“创建”。

---

## <a name="configure-your-azure-application-offer-setup-details"></a>配置 Azure 应用程序产品/服务设置详细信息

在“产品/服务设置”选项卡的“设置详细信息”下，选择是否配置体验版。 你还可以将客户关系管理 (CRM) 系统与商业市场产品/服务连接。

### <a name="enable-a-test-drive-optional"></a>启用体验版（可选）

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在客户。 若要详细了解体验版，请参阅[体验版](plan-azure-application-offer.md#test-drive)。

#### <a name="to-enable-a-test-drive"></a>启用体验版

- 在“体验版”下，选中“启用体验版”复选框。

### <a name="customer-lead-management"></a>潜在客户管理

将客户关系管理 (CRM) 系统与你的商业市场产品/服务相连接，以便在客户表示有兴趣或部署你的产品时你可以接收到客户的联系信息。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>在合作伙伴中心配置连接详细信息

1. 在“潜在客户”下，选择“连接”链接。 
1. 在“连接详细信息”对话框中，从列表选择潜在顾客目标。
1. 填写显示的字段。 有关详细步骤，请参阅以下文章：

   - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [配置产品/服务以将潜在客户发送到 Dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)（以前称为 Dynamics CRM Online）
   - [配置产品/服务以将潜在客户发送到 HTTPS 终结点](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [配置你的产品/服务以将潜在顾客发送到 Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [配置你的产品/服务以将潜在顾客发送到 Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要验证你提供的配置，请选择“验证”链接（如果适用）。
1. 要关闭该对话框，请选择“连接”。
1. 选择“保存草稿”，然后转到下一选项卡：“属性”。

> [!NOTE]
> 确保不断更新与潜在客户目标的连接，以免失去任何潜在客户。 确保在发生更改时更新这些连接。

## <a name="next-steps"></a>后续步骤

- [配置 Azure 应用程序属性](azure-app-properties.md)
