---
title: 在 Microsoft AppSource 合作伙伴中心设置 Power BI 视觉对象产品/服务技术配置
description: 了解如何在 Microsoft AppSource 合作伙伴中心设置 Power BI 视觉对象产品/服务技术配置。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 94f1a884eb2fbb10ced8c343d0408845f0ff34ba
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273015"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>设置 Power BI 视觉对象产品/服务技术配置

在“技术配置”选项卡上，提供 Power BI 视觉对象产品/服务所需的文件。

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="显示合作伙伴中心的“技术配置”页。":::

## <a name="pbiviz-package"></a>PBIVIZ 包

[将 Power BI 视觉对象打包](/power-bi/developer/visuals/package-visual)放入 Pbiviz 包，后者包含所有必要的元数据：

- 视觉对象名称
- 显示名称
- GUID（见下方备注）
- 版本（请参阅下面的注释）
- 说明
- 作者姓名和电子邮件

> [!NOTE]
> 如果要更新或重新提交视觉对象：
> - 产品/服务必须保持不变。
> - 包更新后，版本号应递增。

## <a name="sample-pbix-report-file"></a>PBIX 报表文件示例

为了展示你的视觉对象，请帮助用户了解该视觉对象。 强调该视觉对象可以为用户带来的价值，并提供使用示例和格式设置选项。 添加“提示”页，并在页面末尾附上一些提示和技巧以及操作注意事项。 示例 PBIX 报表文件必须脱机运行且无任何外部连接。

> [!NOTE]
> - PBIX 报表必须使用与 PBIVIZ 相同的视觉对象版本。
> - PBIX 报表文件必须脱机运行且无任何外部连接。

在左侧导航菜单中跳转到“产品/服务管理”选项卡之前，选择“保存草稿”。 

## <a name="next-steps"></a>后续步骤

- [产品/服务管理](power-bi-visual-manage-names.md)
