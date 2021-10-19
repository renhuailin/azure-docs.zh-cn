---
title: 在 Azure Sentinel 中管理计划分析规则的模板版本
description: 了解如何管理计划分析规则模板与从这些模板创建的规则之间的关系。 将模板更新合并到规则中，以及将规则中的更改还原到原始模板。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2021
ms.author: yelevin
ms.openlocfilehash: c08b346e5ab6dad939b441d60f9f68908568c840
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730524"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-azure-sentinel"></a>在 Azure Sentinel 中管理计划分析规则的模板版本

> [!IMPORTANT]
>
> 此功能目前以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="introduction"></a>简介

Azure Sentinel 随附了[分析规则模板](detect-threats-built-in.md)，你可以通过有效创建这些模板的副本来将其转化为活动规则 - 这就是从模板创建规则时发生的行为。 但那时，活动规则不再连接到模板。 如果 Microsoft 工程师或其他任何人对规则模板进行了更改，则此前从该模板创建的任何规则将不会动态更新以匹配新模板。

但是，从模板创建的规则确实会记住它们来自于哪些模板，这可为你带来两项优势：

- 如果在从模板创建规则时（或之后的任何时间）对该规则进行了更改，始终都可以将该规则还原为其原始版本（作为模板的副本）。

- 你可以在模板更新时收到通知，并且可以选择将规则更新为其模板的新版本，或将其保持原样。

本文将介绍如何管理这些任务以及需要注意的事项。 下面所述的过程适用于从模板创建的任何[计划](detect-threats-built-in.md#scheduled)分析规则。

## <a name="discover-your-rules-template-version-number"></a>找到规则的模板版本号

通过实施模板版本控制，可以查看和跟踪规则模板以及从其创建的规则的版本。 其模板已更新的规则会在规则名称的旁边显示“有可用更新”徽章。

1. 在“分析”边栏选项卡上，选择“活动规则”选项卡 。

1. 选择“计划”类型的任一规则。  

    - 如果该规则显示了“有可用更新”徽章，则其详细信息窗格中的“编辑”按钮旁边会出现一个“查看并更新”按钮（参阅下面的下一步骤中的图 1） 。

    - 如果该规则是从模板创建的且未显示“有可用更新”徽章，则其详细信息窗格中的“编辑”按钮旁边会出现一个“与模板进行比较”按钮（参阅下面的下一步骤中的图 2 和 3） 。

    - 如果只显示了“编辑”按钮，则表示该规则是从头开始创建的，而不是从模板创建的。

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="活动规则列表的屏幕截图，其中的徽章指示有可用的模板更新。" lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. 向下滚动到详细信息窗格底部，在此处会看到两个版本号：从中创建了该规则的模板的版本，以及该模板的最新可用版本。 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="详细信息窗格的屏幕截图。向下滚动以查看模板版本号。" border="false":::

    版本号采用“1.0.0”格式 – 主版本、次版本和内部版本。  
    （目前不使用内部版本号，它始终为 0。）

    - 主版本号的差异指示模板中的一些重要内容发生了更改，这可能会影响规则检测威胁的方式，甚至会影响到整个规则能否正常运行。 这是你想要包含在规则中的更改。

    - 次版本号的差异指示模板中的次要改进 – 外观更改或类似的更改 – 这只是一些“锦上添花”的更改，但对于保持规则的功能、功效或性能并不是至关重要。 这是你可以轻松取舍的更改。

    > [!NOTE]
    > 上面的图 2 和 3 显示了从模板创建的规则的两个示例，其中的模板尚未更新。
    > - 图 2 显示了具有其当前模板版本号的规则。 这表明该规则是在 Azure Sentinel 于 2021 年 10 月最初实施模板版本控制之后创建的。
    > - 图 3 显示了没有当前模板版本的规则。 这表明该规则是在 2021 年 10 月之前创建的。 如果有最新的模板版本可用，那么它可能比用于创建规则的版本更新。

## <a name="compare-your-active-rule-with-its-template"></a>将活动规则与其模板进行比较

根据你要执行的操作选择以下选项卡之一，以查看该操作的说明：

# <a name="update-template"></a>[更新模板](#tab/update)

选择规则并决定要考虑更新该规则之后，在详细信息窗格中选择“查看并更新”（参阅上文）。 你将看到，“分析规则向导”现在包含一个“与最新版本进行比较”选项卡 。

在此选项卡上，会显示现有规则的 YAML 表示形式与最新模板版本之间的并列比较结果。 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="分析规则向导中“与最新版本进行比较”选项卡的屏幕截图。":::

> [!NOTE]
> 更新此规则会使用最新模板版本来覆盖现有规则。
应该验证任何引用现有规则的自动化步骤或逻辑（如果引用的名称已更改）。 此外，可能会覆盖在创建原始规则时提供的任何自定义设置 - 对查询、计划、分组或其他设置所做的更改。

### <a name="update-your-rule-with-the-new-template-version"></a>使用新模板版本更新规则

- 如果你可以接受对新模板版本所做的更改，并且原始规则中的任何其他设置均不受影响，请选择“查看并更新”以验证并应用更改。 

- 如果你想要进一步自定义该规则或重新应用可能已被覆盖的任何更改，请选择“下一步: 自定义更改”。 如果选择此操作，则需要依次访问 [分析规则向导](detect-threats-custom.md) 的其余选项卡来做出这些更改，然后在“查看并更新”选项卡上验证并应用这些更改。

- 如果你不想对现有规则进行任何更改，而是要保留现有模板版本，只需选择右上角的“X”退出向导即可。

# <a name="revert-to-template"></a>[还原到模板](#tab/revert)

选择规则并决定要将其还原到原始版本之后，在详细信息窗格中选择“与模板进行比较”（参阅上文）。 你将看到，“分析规则向导”现在包含一个“与最新版本进行比较”选项卡 。

在此选项卡上，会显示现有规则的 YAML 表示形式与最新模板版本之间的并列比较结果。 这两个版本号可能是相同的，但左侧显示的是活动规则，其中包括从模板创建该规则期间或之后对其进行的任何更改，而右侧显示的是未经更改的模板。

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="分析规则向导中“与最新版本进行比较”选项卡的屏幕截图。":::

> [!NOTE]
> 更新此规则会使用最新模板版本来覆盖现有规则。
应该验证任何引用现有规则的自动化步骤或逻辑（如果引用的名称已更改）。 此外，可能会覆盖在创建原始规则时提供的任何自定义设置 - 对查询、计划、分组或其他设置所做的更改。

### <a name="revert-your-rule-to-its-original-template-version"></a>将规则还原到其原始模板版本

- 如果你想要完全还原到此规则的原始版本（模板的干净副本），请选择“查看并更新”以验证并应用更改。 

- 如果你想要以不同的方式自定义该规则或重新应用可能已被覆盖的任何更改，请选择“下一步: 自定义更改”。 如果选择此操作，则需要依次访问 [分析规则向导](detect-threats-custom.md) 的其余选项卡来做出这些更改，然后在“查看并更新”选项卡上验证并应用这些更改。

- 如果你不想对现有规则进行任何更改，只需选择右上角的“X”退出向导即可。

---

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何跟踪 Azure Sentinel 分析规则模板的版本，以及如何将活动规则还原到现有模板版本，或将其更新到新版本。 要详细了解 Azure Sentinel，请参阅以下文章：

- 详细了解[分析规则](detect-threats-built-in.md)。
- 参阅有关[分析规则向导](detect-threats-custom.md)的更多详细信息。
