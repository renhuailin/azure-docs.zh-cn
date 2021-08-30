---
title: 使用警报抑制规则在 Azure 安全中心中抑制误报或其他不需要的安全警报。
description: 本文介绍如何使用 Azure 安全中心的抑制规则隐藏不需要的安全警报
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 5d6a9ca83060c074740985b87af841bfa4be17f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728680"
---
# <a name="suppress-alerts-from-azure-defender"></a>禁止来自 Azure Defender 的警报

此页说明如何使用警报抑制规则抑制来自 Azure Defender 的误报或其他不需要的安全警报。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|免费<br>（大多数安全警报仅适用于 Azure Defender）|
|所需角色和权限：|“安全管理员”和“所有者”可以创建/删除规则 。<br>“安全读取者”和“读取者”可以查看规则 。|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png":::国家/地区/主权（Azure 政府、Azure 中国世纪互联）|
|||


## <a name="what-are-suppression-rules"></a>什么是抑制规则？

各种 Azure Defender 计划检测环境中任何区域的威胁，并生成安全警报。

当单个警报不感兴趣或不相关时，可以手动关闭它。 或者，使用抑制规则功能在将来自动关闭类似警报。 通常，将使用抑制规则来执行以下操作：

- 抑制已标识为“误报”的警报

- 抑制过于频繁地触发而失去作用的警报

抑制规则定义了自动取消警报所应遵循的条件。

> [!CAUTION]
> 抑制安全警报会降低 Azure Defender 的威胁防护效果。 应仔细检查任何抑制规则的潜在影响，并在一段时间内对其进行监视。

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="创建警报抑制规则。":::

## <a name="create-a-suppression-rule"></a>创建抑制规则

有几种方法可以创建规则以取消不需要的安全警报：

- 若要在管理组级别取消警报，请使用 Azure Policy
- 若要在订阅级别取消警报，可以使用 Azure 门户或 REST API，如下所述

抑制规则只能关闭已在选定订阅上触发的警报。

直接在 Azure 门户中创建规则：

1. 从安全中心的“安全警报”页：

    - 选择你不想再查看的特定警报，然后从详细信息窗格中选择“执行操作”。

    - 或者，选择页面顶部的“抑制规则”链接，然后从“抑制规则”页面中选择“创建新的抑制规则” ：

        ![“创建新的抑制规则”按钮。](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. 在“新建抑制规则”窗格中，输入新规则的详细信息。
    - 你的规则可以消除对“所有资源”的警报，因此以后就不会收到类似于此的警报。     
    - 你的规则可以消除“特定条件下的”警报，当它与特定的 IP 地址、进程名、用户帐户、Azure 资源或位置相关时。

    > [!TIP]
    > 如果从特定警报打开“新规则”页，则将在新规则中自动配置警报和订阅。 如果使用“创建新的抑制规则”链接，则所选订阅将与门户中的当前筛选器匹配。

    [![抑制规则创建窗格。](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. 输入规则的详细信息：
    - **名称** - 输入规则的名称。 规则名称必须以字母或数字开头，介于 2 到 50 个字符之间，并且不包含除破折号 (-) 或下划线 (_) 以外的任何符号。 
    - **状态** - 已启用或已禁用。
    - **原因** - 选择其中一个内置原因，如果不能满足你的需求，则选择“其他”。
    - **到期日期** - 规则的结束日期和时间。 规则最多可运行六个月。
1. （可选）使用“模拟”按钮测试规则，以查看如果此规则处于活动状态，将关闭的警报数。
1. 保存规则。 


## <a name="edit-a-suppression-rule"></a>编辑抑制规则

若要编辑已创建的规则，请使用“抑制规则”页。

1. 从安全中心的“安全警报”页面中，选择页面顶部的“抑制规则”链接。
1. 此时将打开“抑制规则”页，其中包含选定订阅的所有规则。

    [![抑制规则列表。](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 若要编辑单个规则，请打开该规则的省略号菜单 (...) 并选择“编辑”。
1. 进行必要的更改并选择“应用”。 

## <a name="delete-a-suppression-rule"></a>删除抑制规则

若要删除已创建的一个或多个规则，请使用“抑制规则”页。

1. 从安全中心的“安全警报”页面中，选择页面顶部的“抑制规则”链接。
1. 此时将打开“抑制规则”页，其中包含选定订阅的所有规则。
1. 若要删除单个规则，请打开该规则的省略号菜单 (...)，然后选择“删除”。
1. 若要删除多个规则，请选中要删除的规则的复选框，然后选择“删除”。
    ![删除一个或多个抑制规则。](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>通过 API 创建和管理抑制规则

可以通过安全中心的 REST API 创建、查看或删除警报抑制规则。 

REST API 中抑制规则的相关 HTTP 方法是：

- **PUT**：创建或更新指定订阅中的抑制规则。

- **获取**：

    - 列出为指定订阅配置的所有规则。 此方法返回适用规则的数组。

    - 获取指定订阅上特定规则的详细信息。 此方法返回一个抑制规则。

    - 以模拟仍处于设计阶段的抑制规则的影响。 此调用标识如果规则处于活动状态，将消除哪些现有警报。

- **DELETE**：删除现有规则（但不会更改它已消除的警报的状态）。

有关完整的详细信息和使用示例，请参阅 [API 文档](/rest/api/securitycenter/)。 


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心中自动取消不需要的警报的抑制规则。

有关 Azure Defender 安全警报的详细信息，请参阅以下页面：

- [安全警报和意向攻击链](alerts-reference.md) - 可能会从 Azure Defender 获取的安全警报的参考指南。