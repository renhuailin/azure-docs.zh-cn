---
title: 使用 Azure Sentinel 中的 Azure Monitor 工作簿可视化数据 |Microsoft Docs
description: 学习如何使用 Azure Sentinel 中的工作簿可视化数据。
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
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: 0b54396efb80aabc5c4a0959d7d4bc2657cf0506
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778638"
---
# <a name="use-azure-monitor-workbooks-to-visualize-and-monitor-your-data"></a>使用 Azure Monitor 工作簿可视化并监视你的数据

[将数据源连接](quickstart-onboard.md)到 Azure Sentinel 后，可以使用采用 Azure Sentinel 的 Azure Monitor 工作簿来可视化和监视数据，这在创建自定义仪表板方面提供了多样性。 虽然工作簿在 Azure Sentinel 中的显示方式有所不同，但有助于你了解如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)。 Azure Sentinel 可让你跨数据创建自定义工作簿，并且还附带了内置的工作簿模板，使你可以在连接数据源后快速获得对数据的见解。

本文介绍了如何在 Azure Sentinel 中可视化数据。

> [!div class="checklist"]
> * 使用内置工作簿
> * 创建新工作簿

## <a name="prerequisites"></a>先决条件

对于 Azure Sentinel 工作区的资源组，你必须至少具有工作簿读取者或工作簿参与者权限 。

> [!NOTE]
> 可在 Azure Sentinel 中查看的工作簿保存在 Azure Sentinel 工作区的资源组中，并由创建它们的工作区进行标记。

## <a name="use-built-in-workbooks"></a>使用内置工作簿

1. 转到“工作簿”，然后选择“模板”以查看 Azure Sentinel 内置工作簿的完整列表。 

    若要查看与你连接的数据类型相关的数据类型，每个工作簿中“必需的数据类型”字段将在绿色复选标记旁边列出数据类型（如果已将相关数据流式传输到 Azure Sentinel）。

    [ ![转到工作簿。](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. 选择“查看模板”，查看用你的数据填充的模板。

1. 若要编辑工作簿，请选择“保存”，然后选择要保存模板 JSON 文件的位置。

   > [!NOTE]
   > 这会基于相关模板创建 Azure 资源，并保存工作簿的 JSON 文件，而不是数据。


1. 选择“查看已保存的工作簿”。 

    [ ![查看工作簿。](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    选择工作簿工具栏中的“编辑”按钮，根据需要自定义工作簿。 完成后，选择“保存”以保存更改。

    有关详细信息，请参阅如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)。

> [!TIP]
> 若要克隆工作簿，请选择“编辑”，然后选择“另存为”，确保在同一订阅和资源组下将其另存为其他名称 。
> 克隆的工作簿显示在“我的工作簿”选项卡下。
>
## <a name="create-new-workbook"></a>创建新的工作簿

1. 若要从头开始创建新工作簿，请选择“工作簿”，然后选择“添加工作簿”。 

    [ ![新建工作簿。](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. 若要编辑工作簿，请选择“编辑”，然后根据需要添加文本、查询和参数。 有关如何自定义工作簿的详细信息，请参阅如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)。 

1. 生成查询时，确保将“数据源”设置为“日志”，“资源类型”设置为“日志分析”，然后选择相关工作区。 

1. 创建工作簿后，保存工作簿以确保将其保存在 Azure Sentinel 工作区的订阅和资源组下。

1. 如果要让组织中的其他人使用该工作簿，请在“保存到”下选择“共享报表”。 如果希望此工作簿仅供你使用，请选择“我的报表”。

1. 若要在工作区中的工作簿之间切换，请在任何工作簿的工具栏中选择“打开”![用于打开工作簿的图标。](./media/tutorial-monitor-data/switch.png) 。 屏幕将切换到可以切换到的其他工作簿的列表。

    选择要打开的工作簿：

    [ ![切换工作簿。](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>刷新工作簿数据

刷新工作簿以显示更新的数据。 在工具栏中，选择下列选项之一：

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::刷新：手动刷新工作簿数据。

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::自动刷新：将工作簿设置为按配置的间隔自动刷新。

    - 支持的自动刷新间隔范围为“5 分钟”到“1 天” 。

    - 在编辑工作簿时，自动刷新会暂停，且每次从编辑模式切换回视图模式时，将重启间隔。

    - 如果手动刷新数据，自动刷新间隔也会重启。

    > [!TIP]
    > 默认情况下，自动刷新处于关闭状态。 为了优化性能，自动刷新会在每次关闭工作簿时被关闭，并且不会在后台运行。 当你下次打开工作簿时，请根据需要重新启用自动刷新。
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>打印工作簿或另存为 PDF

若要打印工作簿或将其另存为 PDF，请使用工作簿标题右侧的选项菜单。

1. 选择选项 > :::image type="icon" source="media/whats-new/print-icon.png" border="false":::“打印内容”。 
2. 在打印屏幕中，根据需要调整打印设置，或选择“另存为 PDF”将其保存在本地。

例如：

[ ![打印工作簿或另存为 PDF。](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>如何删除工作簿

若要删除一个已保存的工作簿（无论是保存的模板还是自定义的工作簿），请在“工作簿”页中，选择要删除的已保存工作簿，然后选择“删除”。 这将删除已保存的工作簿。

> [!NOTE]
> 这会删除工作簿资源以及对模板所做的任何更改。 原始模板仍可用。

## <a name="next-steps"></a>后续步骤

在本文中，你学会了如何在 Azure Sentinel 中使用 Azure 工作簿对数据进行可视化。

若要了解如何自动完成对威胁的响应，请参阅[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

若要了解常用的内置工作簿，请参阅[常用 Azure Sentinel 工作簿](top-workbooks.md)。 
