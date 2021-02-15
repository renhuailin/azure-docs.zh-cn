---
title: 工作项集成 (预览) -Application Insights
description: 了解如何在 GitHub 或 Azure DevOps 中创建工作项，其中嵌入了 Application Insights 数据。
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: 0c1d6ffd6a5a39fa49eadc558aa80f365f856df2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101185"
---
# <a name="work-item-integration-preview"></a> (预览版的工作项集成) 

使用工作项集成功能，可以轻松地在 GitHub 或 Azure DevOps 中创建工作项，这些工作项具有嵌入在其中的相关 Application Insights 数据。

> [!IMPORTANT]
> 工作项集成目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-and-configure-a-work-item-template"></a>创建和配置工作项模板

1. 若要创建工作项模板，Application Insights 请在 "配置选择 **工作项**" 下面的左侧，选择 "*配置* 选择工作项"，然后选择 "**创建新模板**"。

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" &quot;工作项&quot; 选项卡的屏幕截图，其中选择了 &quot;创建新模板&quot;。" lightbox="./media/work-item-integration/create-work-item-template.png":::

    如果当前不存在任何模板，你还可以从 "端到端事务详细信息" 选项卡创建工作项模板。 选择一个事件，然后在右侧选择 " **创建工作项**"，然后 **从工作簿模板开始**。

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" &quot;端到端事务详细信息&quot; 选项卡的屏幕截图，其中包含 &quot;创建工作项&quot;，请从所选工作簿模板开始。" lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. 选择 " **创建新模板**" 后，你可以选择跟踪系统、命名你的工作簿、链接到所选跟踪系统，并选择用于存储该模板的区域 (默认为 Application Insights 资源所在的区域) 。 URL 参数是存储库的默认 URL，例如 `https://github.com/myusername/reponame` 或 `https://mydevops.visualstudio.com/myproject` 。

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" 创建新工作项工作簿模板的屏幕截图。":::

## <a name="create-a-work-item"></a>创建工作项

 你可以从任何可从性能、故障、可用性或其他选项卡访问的端到端事务详细信息访问新模板。

1. 若要创建工作项，请使用端对端事务详细信息，选择一个事件，然后选择 " **创建工作项** " 并选择工作项模板。

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" 选择了 &quot;创建工作项&quot; 的 &quot;端到端事务详细信息&quot; 选项卡的屏幕截图。" lightbox="./media/work-item-integration/create-work-item.png":::

1. 浏览器中的新选项卡将打开你的选择跟踪系统。 在 Azure DevOps 中，你可以创建一个 bug 或任务，在 GitHub 中，你可以在存储库中创建新问题。 将自动创建一个新的工作项，其中包含 Application Insights 提供的上下文信息。

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" 自动创建的 GitHub 问题的屏幕截图" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" 在 Azure DevOps 中自动创建 bug 的屏幕截图。" lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>编辑模板

若要编辑模板，请在 "*配置*" 下，单击 "**工作项**" 选项卡，然后选择要更新的工作簿旁边的铅笔图标。

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" 选中了 &quot;编辑铅笔&quot; 图标的 &quot;工作项&quot; 选项卡的屏幕截图。":::

选择 ![ 顶部的 "编辑编辑 ](./media/work-item-integration/edit-icon.png) " 图标，开始编辑模板。 工作项模板基于 [Azure Monitor 工作簿](../platform/workbooks-overview.md)。 工作项信息使用关键字查询语言生成。 您可以修改查询以添加对您的团队至关重要的上下文。 完成编辑后，通过选择 ![ 顶部工具栏中的 "保存" 图标保存图标来保存工作簿 ](./media/work-item-integration/save-icon.png) 。

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" 处于编辑模式的工作项模板工作簿的屏幕截图。" lightbox="./media/work-item-integration/edit-workbook.png":::

可以创建多个工作项配置，并使用自定义工作簿来满足每个方案。 还可以通过 Azure 部署工作簿，资源管理器确保环境中的标准实现。