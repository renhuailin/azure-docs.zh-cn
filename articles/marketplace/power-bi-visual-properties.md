---
title: 在 Microsoft AppSource 合作伙伴中心配置 Power BI 视觉对象产品/服务的属性
description: 了解如何在 Microsoft AppSource 合作伙伴中心配置 Power BI 视觉对象产品/服务的属性。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: a1426d8ed0a97c2ec1715f4b0c368b9b9b9770f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699365"
---
# <a name="configure-power-bi-visual-offer-properties"></a>配置 Power BI 视觉对象产品/服务属性

通过此页，可定义用于对 Microsoft AppSource 上的产品/服务进行分组的[类别](/azure/marketplace/categories)、应用程序版本以及支持产品/服务的法律协定。

## <a name="general-info"></a>常规信息

- 选择最多三个[类别](/azure/marketplace/categories)，用于将产品/服务分组到适当的市场搜索区域。
- 最多选择两个行业，当客户在在线商店中筛选其关于行业的搜索时，这些行业将用于显示产品/服务。

## <a name="legal-and-support-info"></a>法律和支持信息

提供产品/服务的条款和条件。 可以使用两个选项：

- 使用[标准协定](#use-the-standard-contract)
- 使用[自己的条款和条件](#use-your-own-terms-and-conditions) (EULA)

要了解标准协定，请参阅 [Microsoft 商业市场的标准协定](standard-contract.md)或下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178) PDF（确保已关闭弹出窗口阻止程序）。

### <a name="use-the-standard-contract"></a>使用标准协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

1. 选中“使用标准协定...”复选框。

    :::image type="content" source="media/power-bi-visual/use-standard-contract.png" alt-text="显示“使用 Microsoft 商业市场的标准协定”复选框。":::

1. 在“确认”对话框中，选择“接受” 。 根据屏幕的大小，可能需要向上滚动才能看到它。

   > [!NOTE]
   > 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件了。 要么根据具有可选修订的标准协定提供解决方案，要么根据你自己的条款和条件提供解决方案。

### <a name="use-your-own-terms-and-conditions"></a>使用自己的条款和条件

你可以提供自己的条款和条件，而不是使用标准协定，或使用特定于 Power BI 视觉对象的 EULA。 客户必须接受这些条款，才能试用你的产品/服务。

1. 清除“使用 Microsoft 商业市场的标准协定”复选框。
1. 在“EULA”字段中（参见上图），输入条款和条件的单个 Web 地址。 或者，指向 `https://visuals.azureedge.net/app-store/Power%20BI%20-%20Default%20Custom%20Visual%20EULA.pdf` 处的 Power BI 视觉对象协定 (PDF)。 两者都将在 AppSource 中显示为活动链接。

### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的链接 (URL)。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="support-document-link"></a>支持文档链接

AppSource 上的用户将可以看到此链接。 在 URL 中包含 http:// 或 https://。

选择“保存草稿”，然后再转到左侧导航菜单中的下一个选项卡“产品/服务列表” 。

## <a name="next-steps"></a>后续步骤

- [**产品/服务一览**](power-bi-visual-offer-listing.md)
