---
title: 在 Microsoft AppSource 合作伙伴中心创建 Power BI 视觉对象产品/服务
description: 了解如何在合作伙伴中心创建 Power BI 视觉对象产品/服务。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: eca02cf776eb1f6fa690ecefd865dadbc3d9be78
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080050"
---
# <a name="create-a-power-bi-visual-offer"></a>创建 Power BI 视觉对象产品/服务

本文介绍如何使用合作伙伴中心向 [Microsoft AppSource](https://appsource.microsoft.com) 提交 Power BI 视觉对象产品/服务，供他人发现和使用。

在开始之前，先在[合作伙伴中心](./create-account.md)创建一个商业市场帐户，并确保该帐户已在商业市场计划中注册。

## <a name="before-you-begin"></a>开始之前

查看[计划 Power BI 视觉对象产品/服务](marketplace-power-bi-visual.md)。 其中说明了此产品/服务的技术需求，并列出了创建该产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在“主页”上，选择“市场产品/服务”磁贴。

    [ ![说明合作伙伴中心主页上的“市场产品/服务”磁贴。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. 在“市场产品/服务”页上，选择“+ 新建产品/服务” > “Power BI 视觉对象”。

    [ ![显示左窗格菜单选项和“新建产品/服务”按钮。](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
1. 在“概述”页上，选择“+ 新建产品/服务” > “Power BI 视觉对象”。

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="显示左窗格菜单选项和“新建产品/服务”按钮。":::

---

> [!IMPORTANT]
> 发布某个产品/服务后，在合作伙伴中心对其进行的任何编辑都不会出现在 AppSource 中，除非重新发布该产品和服务。 对产品/服务进行更改后，请确保一律重新发布该产品/服务。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在产品/服务及 Azure 资源管理器模板的 web 地址中看到此 ID（如有）。
- 只使用小写字母和数字。 该 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果发布者 ID 为 `testpublisherid`，你输入“test-offer-1”，则产品/服务 web 地址为 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。
- 产品/服务 ID 在合作伙伴中心的所有其他 Power BI 视觉对象产品/服务的列表中应该是独一无二的。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称不用在 AppSource 中。 它不同于向客户显示的产品/服务名称和其他值。

选择“创建”，以生成产品/服务。 将打开合作伙伴中心的“产品/服务设置”页。

## <a name="setup-details"></a>设置详细信息

对于“其他购买”，请选择你的产品/服务是否要求用户购买某项服务或进行其他应用内购买。

对于“Power BI 认证”（可选），请仔细阅读说明。如果需要请求 Power BI 认证，请选中相应的复选框。 [经认证的](https://aka.ms/PBIvisualcertification) Power BI 视觉对象满足 Microsoft Power BI 团队测试和批准的某些指定代码要求。 建议你在请求认证之前提交并发布 Power BI 视觉对象，因为认证过程需要额外的时间，可能导致产品/服务发布延迟。

## <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

连接到 CRM 是可选的选项。 有关详细信息，请参阅[来自商业市场产品/服务的客户销售线索](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“属性”。 

## <a name="next-steps"></a>后续步骤

- [产品/服务属性](power-bi-visual-properties.md)
