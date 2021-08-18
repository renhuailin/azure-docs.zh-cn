---
title: 数据目录世系用户指南（预览版）
description: 本文概述了 Azure Purview 的目录世系功能。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 4708cdc0a7937749e0df9a65267067f5dc31198d
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201693"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure Purview 数据目录世系用户指南

本文概述了 Azure Purview 数据目录中的数据世系功能。

## <a name="background"></a>背景

Azure Purview 的其中一个平台功能是能够显示数据进程创建的数据集之间的世系。 数据工厂、Data Share 和 Power BI 等系统在数据移动时可捕获数据世系。 还可通过 Atlas 挂钩和 REST API 来支持自定义世系报表。

## <a name="lineage-collection"></a>世系集合 
 在 Azure Purview 中从企业数据系统收集的元数据拼结在一起以显示端到端数据世系。 将世系收集到 Purview 中的数据系统大致分为以下三种类型。

### <a name="data-processing-system"></a>数据处理系统
数据集成和 ETL 工具可以在执行时将世系推送到 Azure Purview。 数据工厂、Data Share、Synapse、Azure Databricks 等工具属于此类数据系统。 数据处理系统从不同的数据库和存储解决方案引用数据集作为来源，以创建目标数据集。 下表列出了当前与 Purview 世系集成的数据处理系统的列表。

| 数据处理系统 | 支持的范围 |
| ---------------------- | ------------|
| Azure 数据工厂 | [Copy 活动](how-to-link-azure-data-factory.md#copy-activity-support) <br> [数据流活动](how-to-link-azure-data-factory.md#data-flow-support) <br> [执行 SSIS 包活动](how-to-link-azure-data-factory.md#execute-ssis-package-support) |
| Azure Synapse Analytics | [Copy 活动](how-to-lineage-azure-synapse-analytics.md#copy-activity-support) |
| Azure Data Share | [共享快照](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>数据存储系统
数据库和存储解决方案（如 SQL Server、Teradata 和 SAP）具有查询引擎，可使用脚本语言转换数据。 存储过程中的数据世系将收集到 Purview 中，并与其他系统中的世系拼结。

| 数据存储系统 | 支持的范围 |
| ---------------------- | ------------|
| Teradata | 存储过程

### <a name="data-analytics--reporting-systems"></a>数据分析和报表系统
数据系统（如 Azure ML 和 Power BI）将世系报告到 Azure Purview 中。 这些系统将使用存储系统中的数据集并通过其元模型进行处理，以创建 BI 仪表板、ML 试验等。

| 数据分析和报表系统 | 支持的范围 |
| ---------------------- | ------------|
| Power BI | [数据集、数据流、报表和仪表板](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>世系入门

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

Purview 中的世系包括数据集和进程。 数据集也称为节点，而进程也可称为边缘：

* **数据集（节点）** ：数据集（结构化或非结构化）作为进程的输入提供。 例如，SQL 表、Azure blob 和文件（如 .csv 和 .xml）都被视为数据集。 在 Purview 的世系部分，数据集用矩形框表示。

* **进程（边缘）** ：在数据集上执行的活动或转换称为进程。 例如，ADF 复制活动、Data Share 快照等。 在 Purview 的世系部分，进程用圆边框表示。

若要访问 Purview 中资产的世系信息，请执行以下步骤：

1. 在 Azure 门户中，转到 [Azure Purview 帐户页](https://aka.ms/purviewportal)。

1. 从列表中选择 Azure Purview 帐户，然后在“概述”页中选择“启动 Purview 帐户”。

1. 在 Azure Purview 的“主页”上，搜索数据集名称或进程名称（如 ADF 复制或数据流活动）。 然后按 Enter。

1. 从搜索结果中选择资产，并选择其“世系”选项卡。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="屏幕截图显示如何选择“世系”选项卡。" border="true":::

## <a name="asset-level-lineage"></a>资产级世系

Azure Purview 支持数据集和进程的资产级世系。 若要查看资产级世系，请转到目录中当前资产的“世系”选项卡。 选择当前数据集资产节点。 默认情况下，属于该数据的列的列表显示在左窗格中。

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="屏幕截图显示如何在世系页中选择“查看列”" border="true":::

## <a name="dataset-column-lineage"></a>数据集列世系

若要查看数据集的列级世系，请访问目录中当前资产的“世系”选项卡，然后执行以下步骤：

1. 进入“世系”选项卡后，在左窗格中，选中要在数据世系中显示的每个列旁边的复选框。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="屏幕截图显示如何选择要在世系页中显示的列。" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. 将鼠标悬停在左窗格中选定的列或世系画布的数据集中选定的列，以查看列映射。 所有列实例都已突出显示。

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="屏幕截图显示如何将鼠标悬停在列名称上以突出显示数据世系路径中的列流。" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. 如果列数大于左窗格中可显示的列数，请使用筛选器选项按名称选择特定列。 或者，可以使用鼠标滚动列表。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="屏幕截图显示如何按世系页上的列名筛选列。" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. 如果世系画布包含更多节点和边缘，请使用筛选器按名称选择数据资产或进程节点。 或者，可以使用鼠标在世系窗口周围平移。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="屏幕截图显示在世系页上按名称显示数据资产节点。" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. 使用左窗格中的切换键在世系画布中突出显示数据集列表。 如果关闭切换键，将显示至少包含其中一个选定列的任何资产。 如果打开切换键，将只显示包含所有列的数据集。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="屏幕截图显示如何使用切换键来筛选世系页上的节点列表。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>进程列世系
数据进程可以采用一个或多个输入数据集生成一个或多个输出。 在 Purview 中，列级世系可用于进程节点。 
1. 从“列”面板的下拉菜单中，在输入和输出数据集之间切换。
2. 从一个或多个表中选择列，以查看从输入数据集流向相应输出数据集的世系。

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="屏幕截图显示进程节点的世系列。" lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>浏览世系中的资产
1. 在任何资产上选择“切换到资产”，可从“世系”视图中查看其相应的元数据。 这样做可有效地从“世系”视图浏览到目录中的其他资产。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="屏幕截图显示如何在世系数据资产中选择“切换到资产”。" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. 对于常用的数据集，世系画布可能会变得很复杂。 为避免混乱，默认视图将仅显示受关注资产的五个级别的世系。 其余世系可以通过单击世系画布中的气泡展开。 数据使用者还可以隐藏不感兴趣的画布中的资产。 若要进一步减少混乱，请关闭世系画布顶部的切换键“更多世系”。 此操作将隐藏世系画布中的所有气泡。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="屏幕截图显示如何切换“更多世系”。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. 使用世系画布中的智能按钮获取最佳世系视图。 目录中的“自动布局”、“缩放到合适大小”、“放大/缩小”、“全屏”和“导航地图”可用于获取沉浸式世系体验。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="屏幕截图显示如何选择世系智能按钮。" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>后续步骤

* [链接到 Azure 数据工厂以获取世系](how-to-link-azure-data-factory.md)
* [链接到 Azure Data Share 以获取世系](how-to-link-azure-data-share.md)