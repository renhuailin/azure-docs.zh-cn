---
title: '数据目录沿袭用户指南 (预览) '
description: 本文概述了 Azure 监控范围的目录沿袭功能。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 6af183c16238c6630b194b112f0c09fd4399d443
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694064"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure 监控范围数据目录沿袭用户指南

本文提供 Azure 监控范围数据目录中的数据沿袭功能的概述。

## <a name="background"></a>背景

Azure 监控范围的平台功能之一是能够显示数据进程创建的数据集之间的沿袭。 数据工厂、数据共享和 Power BI 等系统在数据移动时捕获数据沿袭。 还可以通过阿特拉斯挂钩和 REST API 来支持自定义沿袭报告。

## <a name="lineage-collection"></a>沿袭集合 
 在 Azure 监控范围中从企业数据系统收集的元数据将在拼接，以显示端到端数据沿袭。 将沿袭收集到监控范围中的数据系统大致分为以下三种类型。

### <a name="data-processing-system"></a>数据处理系统
数据集成和 ETL 工具可以在执行时将沿袭推送到 Azure 监控范围。 数据工厂、数据共享、Synapse、Azure Databricks 等工具属于此类数据系统。 数据处理系统通过不同的数据库和存储解决方案来引用数据集，以创建目标数据集。 下表列出了当前与监控范围进行沿袭集成的数据处理系统的列表。


| 数据处理系统 | 支持的范围 |
| ---------------------- | ------------|
| Azure 数据工厂 | [Copy 活动](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [数据流活动](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [执行 SSIS 包活动](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [共享快照](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>数据存储系统
数据库 & 存储解决方案，如 SQL Server、Teradata 和 SAP，使用脚本语言来转换数据的查询引擎。 来自存储过程的数据沿袭将从其他系统的沿袭收集到监控范围和拼接中。

| 数据存储系统 | 支持的范围 |
| ---------------------- | ------------|
| Teradata | 存储过程

### <a name="data-analytics--reporting-systems"></a>数据分析 & 报告系统
数据系统（如 Azure ML 和 Power BI）将报表沿袭纳入 Azure 监控范围。 这些系统将使用存储系统中的数据集，并通过其元模型进行处理，以创建 BI 仪表板、ML 试验等。

| 数据分析 & 报告系统 | 支持的范围 |
| ---------------------- | ------------|
| Power BI | [数据集、数据流、报表 & 面板](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>沿袭入门

监控范围中的沿袭包括数据集和进程。 数据集也称为节点，而进程也可称为边缘：

* **数据集 (节点)**：作为进程的输入提供 (结构化或非结构化) 的数据集。 例如，SQL 表、Azure blob 和文件 (例如 .csv 和 .xml) ，都是被视为的数据集。 在监控范围的沿袭部分中，数据集由矩形框表示。

* **处理 (边缘)**：在数据集上执行的活动或转换称为进程。 例如，ADF 复制活动、数据共享快照等。 在监控范围的沿袭部分中，进程由圆角框表示。

若要访问监控范围中资产的沿袭信息，请执行以下步骤：

1. 在 Azure 门户中，请参阅 [Azure 监控范围帐户页](https://aka.ms/purviewportal)。

1. 从列表中选择你的 Azure 监控范围帐户，然后在 "**概述**" 页中选择 "**启动监控范围帐户**"。

1. 在 "Azure 监控范围" **主页** 上，搜索数据集名称或进程名称（如 ADF 复制或数据流活动）。 然后按 Enter。

1. 从搜索结果中选择资产，并选择其 " **沿袭** " 选项卡。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="显示如何选择 &quot;沿袭&quot; 选项卡的屏幕截图。" border="true":::

## <a name="asset-level-lineage"></a>资产级沿袭

Azure 监控范围支持数据集和进程的资产级别沿袭。 若要查看资产级别的沿袭，请访问目录中当前资产的 " **沿袭** " 选项卡。 选择 "当前数据集资产" 节点。 默认情况下，将在左窗格中显示属于数据的列的列表。

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="显示如何在沿袭页中选择查看列的屏幕截图" border="true":::

## <a name="dataset-column-lineage"></a>数据集列沿袭

若要查看数据集的列级沿袭，请访问目录中当前资产的 " **沿袭** " 选项卡，然后执行以下步骤：

1. 进入 "沿袭" 选项卡后，在左窗格中，选中要在数据沿袭中显示的每个列旁边的复选框。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="显示如何选择要在沿袭页中显示的列的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. 将鼠标悬停在左侧窗格中的所选列或沿袭画布的数据集中，以查看列映射。 所有列实例都已突出显示。

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="显示如何将鼠标悬停在列名称上以突出显示数据沿袭路径中的列流的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. 如果列数大于左窗格中可显示的列数，请使用筛选器选项按名称选择特定列。 或者，您可以使用鼠标滚动列表。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="显示如何按沿袭页上的列名筛选列的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. 如果沿袭画布包含更多节点和边缘，请使用筛选器按名称选择数据资产或进程节点。 或者，您可以使用鼠标在 "沿袭" 窗口周围平移。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="显示沿袭页上按名称显示的数据资产节点的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. 使用左窗格中的切换在沿袭画布中突出显示数据集列表。 如果关闭切换功能，则会显示至少包含其中一种选定列的任何资产。 如果打开切换，则只显示包含所有列的数据集。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="屏幕截图，显示如何使用切换来筛选沿袭页上的节点列表。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>处理列沿袭
数据进程可以采用一个或多个输入数据集生成一个或多个输出。 在监控范围中，列级沿袭可用于进程节点。 
1. 从 "列" 面板中的下拉选项之间切换输入和输出数据集。
2. 从一个或多个表中选择列，以查看从输入数据集流向相应输出数据集的沿袭。

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="显示进程节点的沿袭列的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>按沿袭浏览资产
1. 选择 " **切换到** 任何资产上的资产" 可从 "沿袭" 视图查看其相应的元数据。 这样做是从沿袭视图浏览到目录中的另一个资产的有效方法。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="屏幕截图如何选择在沿袭数据资产中切换到资产。" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. 沿袭画布对于常用的数据集可能会变得复杂。 为避免混乱，默认视图将仅为焦点的资产显示五个级别的沿袭。 可以通过单击沿袭画布中的气泡展开其他沿袭。 数据使用者还可以隐藏不感兴趣的画布中的资产。 若要进一步减少混乱，请关闭沿袭画布顶部的 "切换 **更多沿袭** "。 此操作将隐藏沿袭画布中的所有气泡。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="显示如何切换更多沿袭的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. 使用沿袭画布中的智能按钮获取沿袭的最佳视图。 在目录中，可以使用自动布局、缩放到合适大小、放大/缩小、全屏和导航地图。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="显示如何选择沿袭智能按钮的屏幕截图。" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>后续步骤

* [链接到 Azure 数据工厂进行沿袭](how-to-link-azure-data-factory.md)
* [链接到 Azure 数据共享进行沿袭](how-to-link-azure-data-share.md)