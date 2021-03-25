---
title: 工作项集成（预览版）- Application Insights
description: 了解如何在 GitHub 或 Azure DevOps 中创建工作项，并在其中嵌入 Application Insights 数据。
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731912"
---
# <a name="work-item-integration-preview"></a>工作项集成（预览版）

使用工作项集成功能，你可以轻松地在 GitHub 或 Azure DevOps 中创建工作项，并在其中嵌入相关的 Application Insights 数据。

> [!IMPORTANT]
> 工作项集成目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-and-configure-a-work-item-template"></a>创建和配置工作项模板

1. 若要创建工作项模板，请转到你的 Application Insights 资源，并在左侧的“配置”下选择“工作项”，然后在顶部选择“创建新模板”。 

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text="“工作项”选项卡的屏幕截图，其中选择了“创建新模板”。" lightbox="./media/work-item-integration/create-work-item-template.png":::

    如果当前不存在任何模板，你还可以从“端到端事务详细信息”选项卡创建工作项模板。 选择一个事件，在右侧选择“创建工作项”，然后选择“从工作簿模板开始”。

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text="“端到端事务详细信息”选项卡的屏幕截图，其中选择了“创建工作项”>“从工作簿模板开始”。" lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. 选择“创建新模板”后，你可以选择你的跟踪系统，为你的工作簿命名，链接到所选的跟踪系统，并选择用于存储该模板的区域（默认为你的 Application Insights 资源所在的区域）。 URL 参数是你的存储库的默认 URL，例如 `https://github.com/myusername/reponame` 或 `https://mydevops.visualstudio.com/myproject`。

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text="“新建工作项工作簿模板”的屏幕截图。":::

## <a name="create-a-work-item"></a>创建工作项

 你可以从任何可从“性能”、“故障”、“可用性”或其他选项卡访问的“端到端事务详细信息”来访问新模板。

1. 若要创建工作项，请转到“端到端事务详细信息”，选择一个事件，然后选择“创建工作项”并选择你的工作项模板。

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text="“端到端事务详细信息”选项卡的屏幕截图，其中选择了“创建工作项”。" lightbox="./media/work-item-integration/create-work-item.png":::

1. 浏览器中的一个新选项卡将打开你选择的跟踪系统。 在 Azure DevOps 中，你可以创建 bug 或任务，在 GitHub 中，你可以在存储库中创建新问题。 将自动创建一个新的工作项，其中包含 Application Insights 提供的上下文信息。

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text="自动创建的 GitHub 问题的屏幕截图" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text="在 Azure DevOps 中自动创建 bug 的屏幕截图。" lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>编辑模板

若要编辑模板，请转到“配置”下的“工作项”选项卡，并选择你要更新的工作簿旁边的铅笔图标。

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text="“工作项”选项卡的屏幕截图，其中选择了“编辑”铅笔图标。":::

选择顶部的“编辑”![“编辑”图标](./media/work-item-integration/edit-icon.png)，开始编辑模板。 工作项模板基于 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)。 工作项信息是使用关键字查询语言生成的。 你可以修改查询来添加对你的团队至关重要的更多上下文。 完成编辑后，通过选择顶部工具栏中的“保存”图标![“保存”图标](./media/work-item-integration/save-icon.png)来保存工作簿。

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text="编辑模式下的工作项模板工作簿的屏幕截图。" lightbox="./media/work-item-integration/edit-workbook.png":::

你可以创建多个工作项配置，并使用自定义工作簿来满足每个方案。 还可以通过 Azure 资源管理器来部署工作簿，以确保在你的整个环境中执行标准实现。