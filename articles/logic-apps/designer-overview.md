---
title: 关于单租户工作流设计器
description: 了解单租户 Azure 逻辑应用中的设计器如何帮助你通过 Azure 门户直观地创建工作流。 了解此最新版本中的优势和功能。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129170"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>关于单租户 Azure 逻辑应用中的工作流设计器

使用 Azure 门户中的 Azure 逻辑应用时，可以直观地编辑[工作流](logic-apps-overview.md#workflow)或以编程方式编辑。 在门户中打开[“逻辑应用”资源](logic-apps-overview.md#logic-app)后，在“开发人员”下的“资源”菜单中，可以在[“代码”视图](#code-view)和“设计器”视图之间进行选择。 如果想要直观地开发、编辑和运行工作流时，请选择“设计器”视图。 可以随时在“设计器”视图和“代码”视图之间切换。

> [!IMPORTANT]
> 目前，最新版本的设计器仅适用于在单租户 Azure 逻辑应用环境下运行的“标准”逻辑应用资源。 有关逻辑应用中不同资源类型和运行时环境的详细信息，请查看 [Azure 逻辑应用的单租户与多租户和集成服务环境](single-tenant-overview-compare.md)。

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="Azure 门户中逻辑应用资源页的屏幕截图，其中显示了在“代码”或“设计器”视图中查看工作流的侧栏选项。":::

选择“设计器”视图后，工作流将在工作流设计器中打开。

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="屏幕截图显示工作流设计器，以及使用开关和条件的示例企业工作流。" lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>先决条件

- Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。
- [单租户环境中](single-tenant-overview-compare.md)的“标准”逻辑应用资源。 有关详细信息，请参阅[在 Azure 门户中使用单租户 Azure 逻辑应用（标准版）创建集成工作流](create-single-tenant-workflows-azure-portal.md)。
- 单租户逻辑应用的工作流。

## <a name="latest-version-features"></a>最新版本功能

最新的工作流设计器提供了具有值得关注的功能和优势的全新体验，例如：

- 新的布局引擎，支持更复杂的工作流。 
- 可以通过新的布局引擎、更紧凑的画布以及对基于卡片的布局的更新，简单明了地创建和查看复杂的工作流。
- 使用独立于工作流布局的面板添加和编辑步骤。 此更改提供一个更清晰明了的画布来查看工作流布局。 有关详细信息，请参阅[添加工作流步骤](#add-steps-to-workflows)。
- 使用键盘导航在设计器的工作流中的步骤之间移动。
  - 移至下一张卡片：Ctrl + 向下键 (&darr;)
  - 移至上一张卡片：Ctrl + 向上键 (&uarr;)

## <a name="add-steps-to-workflows"></a>向工作流添加步骤

工作流设计器提供了一种直观的方式在工作流中添加、编辑和删除步骤。 作为工作流的第一步，请始终添加[触发器](logic-apps-overview.md#trigger)。 然后，通过添加一个或多个[操作](logic-apps-overview.md#action)来完成工作流。

要在工作流中添加触发器或操作，请执行以下步骤：

1. 在设计器中打开工作流。
1. 在设计器中，选择“选择操作”，这将打开一个名为“添加触发器”或“添加操作”的窗格。 
1. 在打开的窗格中，通过以下方式筛选列表来查找操作：
    1. 在搜索栏中输入服务、连接器或类别以显示相关操作。 例如，`Azure Cosmos DB` 或 `Data Operations`。 
    1. 如果你知道要使用的具体操作，请在搜索栏中输入名称。 例如，`Call an Azure function` 或 `When an HTTP request is received`。
    1. 选择“内置”选项卡，只显示[内置操作](logic-apps-overview.md#built-in-operations)的类别。 或者，选择“Azure”选项卡以显示 Azure 中可用的其他类别操作。
    1. 可以通过选择“触发器”或“操作”选项卡来仅查看触发器或操作。不过，你只能在第一步添加触发器，在接下来的步骤中添加操作。 根据操作类别，只能使用触发器或操作。
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="Azure 门户中逻辑应用设计器的屏幕截图，显示正在编辑的工作流以添加新操作。" lightbox="./media/designer-overview/designer-add-operation.png":::
1. 选择要使用的操作。 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="逻辑应用设计器的屏幕截图，显示可按服务或名称筛选的可能操作窗格。" lightbox="./media/designer-overview/designer-filter-operations.png":::
1. 根据需要配置触发器或操作。
    1. 必填字段在名称前面带有红色星号 (&ast;)。
    1. 某些触发器和操作可能要求你创建与其他服务的连接。 你可能需要登录到帐户，或输入服务凭据。 例如，如果想要使用 Office 365 Outlook 连接器发送电子邮件，则需要授权 Outlook 电子邮件帐户。
    1. 某些触发器和操作使用动态内容，你可以在其中选择变量，而不是输入信息或表达式。
1. 要保存更改，请在工具栏中选择“保存”。 此步骤还验证工作流是否有效。 

## <a name="code-view"></a>代码视图

通过“代码”视图，可以直接编辑 JSON 格式的工作流定义文件。 请确保选择“保存”，以保存在此视图中所做的任何更改。 

> [!TIP]
> “代码”视图也是查找和复制工作流定义的一种简单方法，而不是使用 Azure 命令行接口 (Azure CLI) 或其他方法。

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="“代码”视图中的逻辑应用工作流的屏幕截图，显示正在 Azure 门户中编辑的 JSON 工作流定义。":::


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 门户中使用单租户 Azure 逻辑应用（标准版）创建集成工作流](create-single-tenant-workflows-azure-portal.md)
