---
title: 将数据工厂世系数据推送到 Azure Purview
description: 了解如何将数据工厂世系数据推送到 Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: a49c9af5899ce847d2081d3294b32ec904e5c1f4
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228671"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>将数据工厂世系数据推送到 Azure Purview（预览）

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，将使用数据工厂用户界面 (UI) 创建一个管道，用于运行活动并将世系数据报告给 Azure Purview 帐户。 然后，可以查看 Azure Purview 帐户中的所有世系信息。 

目前，复制、数据流和执行 SSIS 活动都支持世系。 有关受支持功能的详细信息，请参阅[支持的 Azure 数据工厂活动](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 数据工厂**。 如果没有 Azure 数据工厂，请参阅[创建 Azure 数据工厂](./quickstart-create-data-factory-portal.md)。
* Azure Purview 帐户。 Purview 帐户捕获数据工厂生成的所有世系数据。 如果没有 Azure Purview 帐户，请参阅[创建 Azure Purview](../purview/create-catalog-portal.md)。

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>运行管道并将世系数据推送到 Azure Purview

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>步骤 1：将数据工厂连接到 Purview 帐户

可以按照[将数据工厂连接到 Azure Purview](connect-data-factory-to-azure-purview.md) 中的步骤，在数据工厂和 Purview 帐户之间建立连接。

### <a name="step-2-run-pipeline-in-data-factory"></a>步骤 2：在数据工厂中运行管道

可在数据工厂中创建管道、“复制”活动和“数据流”活动。 对于世系数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获世系数据。

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="“复制”和“数据流”活动的屏幕截图。":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="“执行 SSIS 包”活动的屏幕截图。":::

要详细了解如何创建复制、数据流和执行 SSIS 活动，请参阅[使用 Azure 数据工厂将数据从 Azure Blob 存储复制到 Azure SQL 数据库中的数据库](./tutorial-copy-data-portal.md)、[使用映射数据流转换数据](./tutorial-data-flow.md)和[在 Azure 中运行 SSIS 包](./tutorial-deploy-ssis-packages-azure.md)。

### <a name="step-3-monitor-lineage-reporting-status"></a>步骤 3：监视世系报告状态

运行管道后，在[管道监视视图](monitor-visually.md#monitor-pipeline-runs)中，可以通过单击以下“世系状态”按钮来查看世系报告状态。 活动输出 JSON -> `reportLineageToPurvew` 部分中也提供了相同的信息。

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="在管道监视视图中监视世系报告状态。":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>步骤 4：在 Purview 帐户中查看世系信息

在 Purview 用户界面上，可以浏览资产并选择“Azure 数据工厂”类型。 还可以使用关键字搜索数据目录。

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="在 Purview 中浏览资产的屏幕截图。" lightbox="./media/data-factory-purview/view-dataset.png":::

在活动资产上，单击“世系”选项卡后，可以看到所有世系信息。

- 复制活动：

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Purview 中的复制活动世系的屏幕截图。" lightbox="./media/data-factory-purview/copy-lineage.png":::

- 数据流活动：

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Purview 中的数据流世系的屏幕截图。" lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > 对于“数据流”活动的世系，我们只支持源和接收器。 尚不支持“数据流”转换的世系。

- 执行 SSIS 包活动：

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Purview 中的执行 SSIS 世系的屏幕截图。" lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > 对于“执行 SSIS 包”活动的世系，我们只支持源和目标。 尚不支持转换的世系。

## <a name="next-steps"></a>后续步骤

[目录世系用户指南](../purview/catalog-lineage-user-guide.md)

[将数据工厂连接到 Azure Purview](connect-data-factory-to-azure-purview.md)