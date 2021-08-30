---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: 了解 Azure Digital Twins Explorer 的功能和用途
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 873e1a9fc0fdda5c331112d02a1b6111e336193e
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253646"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer（预览版）

Azure Digital Twins Explorer 是一种开发人员工具，用于直观呈现 Azure 数字孪生实例（包括[模型](concepts-models.md)和[孪生体图](concepts-twins-graph.md)）中的数据并与之交互。 

>[!NOTE]
>此工具目前提供公共预览版。

下面是 Explorer 窗口的视图，其中显示已为示例图填充的模型和孪生体：

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了示例模型和孪生体。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

可视化界面是一款强大的工具，可用于浏览和理解图和模型集的形状，以及对各个孪生体和关系进行针对性的临时更改。

本文包含有关 Azure Digital Twins Explorer 的详细信息，包括其用例和功能概述。 如需各项功能的详细使用步骤，请参阅[使用 Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md)。

## <a name="when-to-use"></a>何时使用

Azure Digital Twins Explorer 是一种可视化工具，适用于想要浏览其孪生体图并在孪生体图的上下文中修改孪生体和关系的用户。

开发人员可能会发现，此工具在以下情况下特别有用：
* **探索**：使用 Explorer 了解 Azure 数字孪生及其表示实际环境的方式。 导入可以查看和编辑的样本模型和图形，以熟悉这项服务。 有关 Azure Digital Twins Explorer 的入门指导步骤，请参阅 [Azure Digital Twins Explorer 入门](quickstart-azure-digital-twins-explorer.md)。
* **开发**：使用 Explorer 查看和验证孪生体图，以及调查模型、孪生体和关系的特定属性。 对图形及其数据进行临时修改。 要详细了解如何使用各项功能，请参阅[使用 Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md)。 

Explorer 的主要目的是帮助你直观呈现和理解孪生体图，并根据需要将其更新。 对于大规模解决方案以及重复性或自动化的工作，请考虑改用 [API 和 SDK](./concepts-apis-sdks.md) 通过代码与实例进行交互。

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>特性和功能

Azure Digital Twins Explorer 按照面板的形式组织，每个面板都有一组不同的功能，可用于浏览和管理模型、孪生体和关系。

Explorer 的各个板块如下所示：
* **模型**：从列表视图添加、删除和查看模型的详细信息。
* **模型图**：以可自定义的模型图的形式直观呈现模型及其互连方式。
* **孪生体图**：以可自定义的孪生体图的形式直观呈现孪生体和关系。 创建和删除孪生体和关系，并查看或编辑其属性。
* **查询资源管理器**：针对孪生体图运行查询，并查看孪生体图面板中的视觉对象结果。

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中突出显示了上述每个面板。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

要详细了解如何使用各项功能，请参阅[使用 Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md)。 

## <a name="how-to-contribute"></a>如何参与

Azure Digital Twins Explorer 是一款开源工具，欢迎你参与相关代码和文档的编写。 托管应用程序通过 GitHub 中的源代码存储库定期进行部署。

若要查看该工具的源代码并阅读有关如何参与代码编写的详细说明，请访问其 GitHub 存储库：[digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer)。

若要查看本文档的投稿说明，请访问 [Microsoft Docs 参与者指南](/contribute/)。

## <a name="other-considerations"></a>其他注意事项

### <a name="region-support"></a>区域支持

Azure Digital Twins Explorer 可用于所有[受支持区域](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)中 Azure 数字孪生的所有实例。

但在公共预览期间，可能会通过并未托管实例的区域发送数据进行处理。 为避免出现数据主权问题，可下载[开放源代码](#how-to-contribute)，在你自己的计算机上创建 Explorer 的本地托管版本。

### <a name="billing"></a>计费

Azure Digital Twins Explorer 是一款免费的工具，可用于与 Azure 数字孪生服务进行交互。 虽然该工具本身是免费的，但在向 Azure 数字孪生服务发送请求时，可能会产生费用并且遵循以下定价准则：[Azure Digital Twins 定价](https://azure.microsoft.com/pricing/details/digital-twins/)。

## <a name="next-steps"></a>后续步骤 

要详细了解如何使用 Azure Digital Twins Explorer 功能，请参阅[使用 Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md)。