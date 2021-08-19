---
title: 适用于 Visual Studio Code 的 Azure Policy 扩展
description: 了解如何使用适用于 Visual Studio Code 的 Azure Policy 扩展来查找 Azure 资源管理器别名。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 870559a2f3d36257cb1246e340163f25ef016eb2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323172"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>使用适用于 Visual Studio Code 的 Azure Policy 扩展

> 适用于 Azure Policy 扩展 0.1.2 及更新版本

了解如何使用适用于 Visual Studio Code 的 Azure Policy 扩展来查找[别名](../concepts/definition-structure.md#aliases)、查看资源和策略定义、导出对象以及评估策略定义。 首先，本文将介绍如何在 Visual Studio Code 中安装 Azure Policy 扩展。 接下来演练如何查找别名。

可以在 Linux、Mac 和 Windows 上安装适用于 Visual Studio Code 的 Azure Policy 扩展。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下各项：

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- [Visual Studio Code](https://code.visualstudio.com)。

## <a name="install-and-configure-the-azure-policy-extension"></a>安装和配置 Azure Policy 扩展

满足先决条件后，可遵循以下步骤安装适用于 Visual Studio Code 的 Azure Policy 扩展：

1. 打开 Visual Studio Code。
1. 在菜单栏中，转到“视图” > “扩展”。 
1. 在搜索框中输入 **Azure Policy**。
1. 在搜索结果中选择“Azure Policy”，然后选择“安装”。 
1. 根据需要选择“重载”。

国家云用户请先遵循以下步骤设置 Azure 环境：

1. 选择“文件”>“首选项”>“设置”。
1. 搜索以下字符串：_Azure:_ 云”
1. 在列表中选择国家云：

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="选择 Visual Studio Code 的 Azure 国家云登录的屏幕截图。" border="false":::

## <a name="using-the-policy-extension"></a>使用 Policy 扩展

> [!NOTE]
> 在适用于 Visual Studio Code 的 Azure Policy 扩展中查看到的对策略定义作出的本地更改不会同步到 Azure。

### <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

若要评估资源和查找别名，必须连接到 Azure 帐户。 遵循以下步骤从 Visual Studio Code 连接到 Azure：

1. 通过 Azure Policy 扩展或命令面板登录到 Azure。

   - Azure Policy 扩展

     在 Azure Policy 扩展中，选择“登录到 Azure”。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Visual Studio Code 的屏幕截图，显示 Azure Policy 扩展的图标。" border="false":::

   - 命令面板

     在菜单栏中，转到“视图” > “命令面板”，然后输入“Azure:  登录”。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="命令面板中 Visual Studio Code 的 Azure 云登录选项的屏幕截图。" border="false":::

1. 按照登录说明登录到 Azure。 连接后，Visual Studio Code 窗口底部的状态栏上会显示 Azure 帐户名称。

### <a name="select-subscriptions"></a>选择订阅

首次登录时，Azure Policy 扩展只会加载默认的订阅资源和策略定义。 若要从显示的资源和策略定义中添加或删除订阅，请遵循以下步骤：

1. 通过命令面板或窗口页脚启动订阅命令。

   - 命令面板：

     在菜单栏中，转到“视图”>“命令面板”，然后输入“Azure:  选择订阅”。

   - 窗口页脚

     在屏幕底部的窗口页脚中，选择与“Azure: \<your account\>”匹配的段。

1. 使用筛选框按名称快速查找订阅。 然后，选中或清除每个订阅对应的复选框，以设置 Azure Policy 扩展要显示的订阅。 添加或删除要显示的订阅后，选择“确定”。

### <a name="search-for-and-view-resources"></a>搜索和查看资源

Azure Policy 扩展在“资源”窗格中按资源提供程序和资源组列出所选订阅中的资源。 树视图包含所选订阅中或订阅级别的以下资源分组：

- **资源提供程序**
  - 每个已注册的资源提供程序，其中包含具有策略别名的资源和相关子资源
- **资源组**
  - 按资源所在的资源组列出所有资源

默认情况下，该扩展将按现有资源以及具有策略别名的资源筛选“资源提供程序”部分。 在“设置” > “扩展” > “Azure Policy”中更改此行为可在不进行筛选的情况下查看所有资源提供程序。  

在单个订阅中包含数百甚至数千个资源的客户可能更倾向于通过搜索来查找其资源。 在 Azure Policy 扩展中可通过以下步骤搜索特定的资源：

1. 通过 Azure Policy 扩展或命令面板启动搜索界面。

   - Azure Policy 扩展

     在 Azure Policy 扩展中，将鼠标悬停在“资源”面板上，选择省略号图标，然后选择“搜索资源”。 

   - 命令面板：

     在菜单栏中，转到“视图”>“命令面板”，然后输入“Azure Policy：搜索资源”  。

1. 如果已选择显示多个订阅，请使用筛选器来选择要搜索的订阅。

1. 使用筛选器选择属于前面所选订阅的、要搜索的资源组。

1. 使用筛选器选择要显示的资源。 可对资源名称和资源类型使用筛选器。

### <a name="discover-aliases-for-resource-properties"></a>发现资源属性的别名

选择某个资源时（不管是通过搜索界面选择，还是在树视图中选择），Azure Policy 扩展将会打开表示该资源及其所有 Azure 资源管理器属性值的 JSON 文件。

打开某个资源后，将鼠标悬停在资源管理器属性名称或值上可显示 Azure Policy 别名（如果存在）。 在此示例中，资源的类型是 `Microsoft.Compute/virtualMachines`，鼠标已悬停在 **properties.storageProfile.imageReference.offer** 属性上。 悬停鼠标可显示匹配的别名。

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Visual Studio Code 的 Azure Policy 扩展的屏幕截图，鼠标悬停在属性上以显示别名。" border="false":::

> [!NOTE]
> VS Code 扩展仅支持评估资源管理器模式属性。 有关模式的详细信息，请参阅[模式定义](../concepts/definition-structure.md#mode)。

### <a name="search-for-and-view-policy-definitions-and-assignments"></a>搜索并查看策略定义和分配

对于选择显示的订阅，Azure Policy 扩展将在“策略”窗格中以树视图的形式列出策略类型和策略分配。 如果客户的单个订阅中有数百或数千个策略定义或分配，则可能更偏好使用可搜索的方式来查找其策略定义或分配。 在 Azure Policy 扩展中可通过以下步骤搜索特定的策略或分配：

1. 通过 Azure Policy 扩展或命令面板启动搜索界面。

   - Azure Policy 扩展

     在 Azure Policy 扩展中，将鼠标悬停在“策略”面板上，选择省略号图标，然后选择“搜索策略”。 

   - 命令面板：

     在菜单栏中，转到“视图”>“命令面板”，然后输入“Azure Policy：搜索策略”  。

1. 如果已选择显示多个订阅，请使用筛选器来选择要搜索的订阅。

1. 使用筛选器选择属于前面所选订阅的、要搜索的策略类型或分配。

1. 使用筛选器选择要显示的策略。 可对策略定义或策略分配的 _displayName_ 使用筛选器。

选择策略或分配时（不管是通过搜索界面选择，还是在树视图中选择），Azure Policy 扩展将会打开表示该策略或分配及其所有资源管理器属性值的 JSON。 该扩展可以验证已打开的 Azure Policy JSON 架构。

### <a name="export-objects"></a>导出对象

订阅中的对象可以导出到本地 JSON 文件。 在“资源”或“策略”窗格中，将鼠标悬停在可导出对象上或将其选中 。 在突出显示的行的末尾，选择“保存”图标，然后选择一个文件夹来保存资源 JSON。

可以在本地导出以下对象：

- 资源窗格
  - 资源组
  - 单个资源（在资源组中或资源提供程序下）
- 策略窗格
  - 策略分配
  - 内置策略定义
  - 自定义策略定义
  - 计划

### <a name="on-demand-evaluation-scan"></a>按需评估扫描

可以使用适用于 Visual Studio Code 的 Azure Policy 扩展启动评估扫描。 若要开始评估，请选择并固定以下各对象：资源、策略定义和策略分配。

1. 若要固定每个对象，请在“资源”窗格或“策略”窗格中找到该对象，然后选择“固定到编辑选项卡”图标 。 固定对象会将其添加到扩展的“评估”窗格中。
1. 在“评估”窗格中，选择其中一个对象，并使用“选择用于评估”图标将其标记为包括在评估中。
1. 在“评估”窗格的顶部，选择“运行评估”图标。 随即在 Visual Studio Code 中打开一个新窗格，其中包含 JSON 格式的生成的评估详细信息。

> [!NOTE]
> 对于 [AuditIfNotExists](../concepts/effects.md#auditifnotexists) 或 [DeployIfNotExists](../concepts/effects.md#deployifnotexists) 策略定义，请使用“评估”窗格中的加号图标或命令面板中的“Azure 策略：为存在检查选择一个资源（仅限 if-not-exists 策略）”来选择用于存在检查的相关资源。

评估结果提供有关策略定义和策略分配以及“policyEvaluations.evaluationResult”属性的信息。 输出与以下示例类似：

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> VS Code 扩展仅支持评估资源管理器模式属性。 有关模式的详细信息，请参阅[模式定义](../concepts/definition-structure.md#mode)。
>
> 评估功能不适用于扩展的 macOS 和 Linux 安装。

### <a name="sign-out"></a>注销

在菜单栏中，转到“视图” > “命令面板”，然后输入“Azure:  注销”。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略定义](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
