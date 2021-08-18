---
title: 工作项集成 - Application Insights
description: 了解如何在 GitHub 或 Azure DevOps 中创建工作项，并在其中嵌入 Application Insights 数据。
ms.topic: conceptual
ms.date: 06/27/2021
ms.openlocfilehash: 02fbb15f417d01d1f9c5b572fdb5553ad1127037
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989123"
---
# <a name="work-item-integration"></a>工作项集成 

使用工作项集成功能，你可以轻松地在 GitHub 或 Azure DevOps 中创建工作项，并在其中嵌入相关的 Application Insights 数据。


新的工作项集成提供[经典版](#classic-work-item-integration)所不具备的以下功能：
- 被分派人、项目或里程碑等高级字段。
- 存储库图标，从中可以区分 GitHub 工作簿与 Azure DevOps 工作簿。
- 任意数量的存储库或工作项的多个配置。
- 通过 Azure 资源管理器模板进行部署。
- 预生成的且可自定义的关键字查询语言 (KQL) 查询，可将 Application Insights 数据添加到工作项。
- 可自定义的工作簿模板。


## <a name="create-and-configure-a-work-item-template"></a>创建和配置工作项模板

1. 若要创建工作项模板，请转到你的 Application Insights 资源，并在左侧的“配置”下选择“工作项”，然后在顶部选择“创建新模板”。 

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text="“工作项”选项卡的屏幕截图，其中选择了“创建新模板”。" lightbox="./media/work-item-integration/create-work-item-template.png":::

    如果当前不存在任何模板，你还可以通过“端到端事务详细信息”选项卡创建工作项模板。 选择一个事件，在右侧选择“创建工作项”，然后选择“从工作簿模板开始”。

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text="“端到端事务详细信息”选项卡的屏幕截图，其中选择了“创建工作项”>“从工作簿模板开始”。" lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. 选择“创建新模板”后，你可以选择你的跟踪系统，为你的工作簿命名，链接到所选的跟踪系统，并选择用于存储该模板的区域（默认为你的 Application Insights 资源所在的区域）。 URL 参数是你的存储库的默认 URL，例如 `https://github.com/myusername/reponame` 或 `https://mydevops.visualstudio.com/myproject`。

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text="“新建工作项工作簿模板”的屏幕截图。":::

    可以直接从模板本身设置特定工作项属性。 这包括被分派人、迭代路径、项目等等，具体取决于你的版本控制提供程序。

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

## <a name="classic-work-item-integration"></a>经典工作项集成 

1. 在 Application Insights 资源中的“配置”下，选择“工作项”。
1. 选择“切换到经典版”，在字段中填写你的信息，然后授权。 

    :::image type="content" source="./media/work-item-integration/classic.png" alt-text="显示如何配置经典工作项的屏幕截图。" lightbox="./media/work-item-integration/classic.png":::

1. 转到“端到端事务详细信息”，选择一个事件，然后选择“创建工作项(经典)”以创建一个工作项。 


### <a name="migrate-to-new-work-item-integration"></a>迁移到新的工作项集成

若要迁移，请删除经典工作项配置，然后[创建并配置工作项模板](#create-and-configure-a-work-item-template)以重新创建集成。

若要删除配置，请转到你的 Application Insights 资源，在“配置”下选择“工作项”，选择“切换到经典版”，然后选择顶部的“删除” 。


## <a name="next-steps"></a>后续步骤
[可用性测试](availability-overview.md)

