---
title: 视觉对象创作
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂和 Synapse Analytics 中使用视觉对象创作
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788042"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure 数据工厂中的视觉对象创作

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂和 Synapse Analytics 用户界面 (UI) 体验让你可以直观地为数据工厂或 Synapse 管道创作和部署资源，而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。

目前，只有 Microsoft Edge 和 Google Chrome 支持 UI。

## <a name="authoring-canvas"></a>创作画布

# <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
若要打开 **创作画布**，请单击铅笔图标。 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="创作画布":::

在这里，创作构成工厂的管道、活动、数据集和数据流。 同样，可以在[“管理”选项卡](#management-hub)上编辑链接服务、触发器和集成运行时。若要开始使用创作画布构建管道，请参阅[使用复制活动复制数据](tutorial-copy-data-portal.md)。 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
若要打开“创作画布”，请单击管道/集成图标。 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="创作画布":::

在这里，你将在 Synapse 中创作管道和活动。 同样，管道使用的数据流可以在“开发”选项卡上进行编辑，并且与它们关联的链接服务、触发器和集成运行时可以在[“管理”选项卡](#management-hub)上进行编辑。若要开始使用创作画布构建管道，请参阅[使用复制活动复制数据](tutorial-copy-data-portal.md)。 

---

默认的可视化创作体验是直接使用该服务。 还支持 Azure Repos Git 或 GitHub 集成，这样可以在生成管道时进行源代码管理和协作。 若要详细了解这些创作体验的区别，请参阅[源代码管理](source-control.md)。

### <a name="properties-pane"></a>属性窗格

对于管道、数据集和数据流等顶层资源，可以在画布右侧的“属性”窗格中编辑顶层属性。 “属性”窗格包含“名称”、“说明”、“注释”和其他顶层属性等属性。 管道活动和数据流转换等子资源使用画布底部的面板进行编辑。 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="属性窗格":::

默认情况下，“属性”窗格只在创建资源时打开。 若要编辑它，请单击画布右上角的“属性”窗格图标。

### <a name="related-resources"></a>相关资源

在“属性”窗格中，可以通过选择“相关”选项卡查看依赖于所选资源的资源。此处将列出引用当前资源的任何资源。

:::image type="content" source="media/author-visually/related-resources.png" alt-text="相关资源":::

例如，在上图中，一个管道和两个数据流使用当前选定的数据集。

## <a name="management-hub"></a>管理中心

管理中心由 UI 中的“管理”选项卡访问，是托管服务的全局管理操作的门户。 在这里，你可以管理与数据存储和外部计算的连接、源代码管理配置以及触发器设置。 有关详细信息，请参阅[管理中心](author-management-hub.md)功能。

# <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="管理链接服务":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="管理链接服务":::

---

## <a name="expressions-and-functions"></a>表达式和函数

可以使用表达式和函数代替静态值来指定服务中的许多属性。

若要指定属性值的表达式，请选择“添加动态内容”或在焦点在字段上时单击 **Alt + P**。

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="添加动态内容":::

这会打开“表达式生成器”，在其中可以通过支持的系统变量、活动输出、函数和用户指定的变量或参数来生成表达式。 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="表达式生成器":::

若要了解表达式语言，请参阅[表达式和函数](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供反馈

选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="反馈":::

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
