---
title: 创建映射数据流
description: 如何创建 Azure 数据工厂映射数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614459"
---
# <a name="create-azure-data-factory-data-flows"></a>创建 Azure 数据工厂数据流

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

映射数据流提供了一种无需进行任何编码即可大规模转换数据的方法。 通过构造一系列转换，可以在数据流设计器中设计数据转换作业。 从任意数量的源转换开始，然后是数据转换步骤。 然后，使用接收器完成数据流，以将结果放在目标提供程序中。

## <a name="steps-to-create-a-new-data-flow"></a>创建新数据流的步骤

# <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

首先从 Azure 门户[创建一个新的 V2 数据工厂](quickstart-create-data-factory-portal.md)。 创建新工厂后，在门户中选择“打开 Azure 数据工厂工作室”磁贴，以启动数据工厂工作室。

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="显示如何从 Azure 门户打开数据工厂工作室的屏幕截图。":::

可以从模板库添加示例数据流。 若要浏览库，请在数据工厂工作室中选择“作者”选项卡，然后单击加号选择“管道” | “模板库”。

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="显示如何在数据工厂中打开模板库。":::

在此处选择“数据流”类别，以从可用模板中进行选择。

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="显示针对数据流筛选的模板库。":::

你还可以在不使用模板的情况下将数据流直接添加到数据工厂。 在数据工厂工作室中选择“作者”选项卡，然后单击加号选择“数据流” | “数据流”。  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="显示如何直接创建空数据流的屏幕截图。":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

首先从 Azure 门户[创建一个新的 Synapse 工作区](../synapse-analytics/quickstart-create-workspace.md)。 创建新工作区后，选择“打开 Azure Studio”磁贴以启动数据工厂 UI。
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="显示如何从 Azure 门户打开 Synapse Studio 的屏幕截图。":::

可以从模板库添加示例数据流。  若要浏览库，请在 Synapse Studio 中选择“集成”选项卡，然后单击加号选择“浏览库”。

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="显示如何在数据工厂中打开模板库。":::

在此处根据“类别: 数据流”进行筛选，以从可用模板中进行选择。

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="显示针对数据流筛选的模板库。":::

你还可以在不使用模板的情况下将数据流直接添加到工作区。 在 Synapse Studio 中选择“集成”选项卡，单击加号选择“管道”。  然后，在管道中展开“移动和转换”活动部分，并将“数据流”拖到管道的画布上。

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="显示如何直接创建空管道的屏幕截图。":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="显示如何将空数据流直接添加到管道的屏幕截图。":::

---

## <a name="next-steps"></a>后续步骤

* [教程：使用映射数据流转换数据](tutorial-data-flow.md)
* 开始使用[源转换](data-flow-source.md)生成数据转换。
