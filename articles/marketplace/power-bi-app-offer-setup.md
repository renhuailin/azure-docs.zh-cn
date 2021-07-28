---
title: 在 Microsoft AppSource（Microsoft Azure 市场）上创建 Power BI 应用产品/服务
description: 在 Microsoft AppSource（Microsoft Azure 市场）上创建 Power BI 应用产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: e86a97b0f4aa9fef24203128970b8d9cc857cd41
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955370"
---
# <a name="create-a-power-bi-app-offer"></a>创建 Power BI 应用套餐

本文介绍如何创建 Power BI 应用产品/服务。 所有产品/服务须经认证，在此过程中，我们会检查相关解决方案的标准要求、兼容性和适当做法。

开始之前，先在[合作伙伴中心](./create-account.md)创建一个商业市场帐户，并确保该帐户已在商业市场计划中注册。

## <a name="before-you-begin"></a>开始之前

查看[规划 Power BI 产品/服务](marketplace-power-bi.md)。 其中说明了此产品/服务的技术需求，并列出了创建该产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，选择“+ 新建产品/服务” > “Power BI 应用”。

    :::image type="content" source="media/power-bi/new-offer-power-bi-app.png" alt-text="左窗格菜单选项和“新产品/服务”按钮。":::

> [!IMPORTANT]
> 发布某个产品/服务后，在合作伙伴中心对其进行的任何编辑都不会出现在 Microsoft AppSource 中，除非重新发布该产品和服务。 对产品/服务进行更改后，请确保一律重新发布该产品/服务。

如果未显示或未启用“Power BI 应用”，则帐户无权创建此产品/服务类型。 请检查是否已满足此产品/服务类型的所有[要求](marketplace-dynamics-365.md)，包括开发人员帐户注册。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在产品/服务及 Azure 资源管理器模板的 web 地址中看到此 ID（如有）。
- 只使用小写字母和数字。 该 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果发布者 ID 为 `testpublisherid`，你输入“test-offer-1”，则产品/服务 web 地址为 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称不用于 AppSource。 它不同于向客户显示的产品/服务名称和其他值。
- 选择“创建”后，便无法更改此名称。

选择“创建”，以生成产品/服务。 将打开合作伙伴中心的“产品/服务设置”页。

## <a name="alias"></a>Alias

输入描述性名称，此名称将仅用于在合作伙伴中心内指示此产品/服务。 此产品/服务的别名（预先填入你在创建该产品/服务时输入的内容）不会在市场中使用，并且与向客户显示的产品/服务名称不同。 如果以后要更新产品/服务名称，请参阅[产品/服务列表](power-bi-app-offer-listing.md)页面。

## <a name="setup-details"></a>设置详细信息

本部分为空白，不适用于 Power BI 应用。

## <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

有关详细信息，请参阅[来自商业市场产品/服务的客户销售线索](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“属性”。 

## <a name="next-steps"></a>后续步骤

- [配置产品/服务属性](power-bi-app-properties.md)