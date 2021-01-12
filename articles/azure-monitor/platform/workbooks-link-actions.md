---
title: Azure Monitor 工作簿链接操作
description: 如何使用 Azure Monitor 工作簿中的链接操作
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 3e94f1a70d5fa2a6e132dc6dc6f95439959b07f0
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122031"
---
# <a name="link-actions"></a>链接操作

可以通过工作簿链接步骤或 [网格](workbooks-grid-visualizations.md)、 [标题](workbooks-tile-visualizations.md)或 [图形](workbooks-graph-visualizations.md)的列设置来访问链接操作。

## <a name="general-link-actions"></a>常规链接操作

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Generic Details` | 显示属性网格上下文视图中的行值。 |
| `Cell Details` | 显示属性网格上下文视图中的单元值。 当单元包含带有信息的动态类型（例如，包含位置、角色实例等请求属性的 JSON）时，此操作非常有用。 |
| `Url` | 单元格的值应为有效的 http url，该单元格将是在新选项卡中打开该 url 的链接。|

## <a name="application-insights"></a>Application Insights

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Custom Event Details` | 用单元格中的自定义事件 ID () 打开 Application Insights 搜索详细信息 `itemId` 。 |
| `* Details` | 类似于自定义事件详细信息，只是在依赖项、异常、页面视图、请求和跟踪方面不同。 |
| `Custom Event User Flows` | 在单元中打开对自定义事件名称透视的 Application Insights 用户流体验。 |
| `* User Flows` | 类似于自定义事件用户流例外、页面视图和请求除外。 |
| `User Timeline` | 打开用户 ID (单元格中) 的用户时间线 `user_Id` 。 |
| `Session Timeline` | 打开单元中的值 Application Insights 搜索体验 (例如，搜索文本 "abc"，其中 abc 是单元) 中的值。 |

`*` 表示上表的通配符

## <a name="azure-resource"></a>Azure 资源

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `ARM Deployment` | 部署 Azure 资源管理器模板。  选择此项后，会显示其他字段，使作者可以配置要打开哪个 Azure 资源管理器模板、模板的参数等。 [请参阅 Azure 资源管理器部署链接设置](#azure-resource-manager-deployment-link-settings)。 |
| `Create Alert Rule` | 创建资源的警报规则。  |
| `Custom View` | 打开自定义视图。 选择此项后，会显示其他字段，使作者可以配置用于打开视图的视图扩展、视图名称和任何参数。 [请参阅自定义视图](#custom-view-link-settings)。 |
| `Metrics` | 打开度量值视图。  |
| `Resource overview` | 根据单元格中的资源 ID 值，在门户中打开资源视图。 作者还可以根据需要设置一个 `submenu` 值，该值将在资源视图中打开特定菜单项。 |
| `Workbook (template)` | 打开工作簿模板。  选择此项后，会显示其他字段，使作者可以配置要打开的模板等。  |

## <a name="link-settings"></a>链接设置

使用链接呈现器时，可以使用以下设置：

![链接设置的屏幕截图](./media/workbooks-link-actions/link-settings.png)

| 设置 | 说明 |
|:------------- |:-------------|
| `View to open` | 允许作者选择上面枚举的操作之一。 |
| `Menu item` | 如果选择了 "资源概述"，则这是要打开的资源概述中的菜单项。 这可用于打开警报或活动日志，而不是资源的 "概述"。 每个 Azure 的菜单项值各不相同 `Resourcetype` 。|
| `Link label` | 如果指定此值，则此值将显示在网格列中。 如果未指定此值，则将显示该单元格的值。 如果希望显示其他值（如热度地图或图标），请不要使用 `Link` 呈现器，而应使用相应的呈现器并选择 `Make this item a link` 选项。 |
| `Open link in Context Blade` | 如果指定此项，则该链接将以弹出窗口的形式打开，而不是作为完整视图打开。 |

使用选项时 `Make this item a link` ，可以使用以下设置：

| 设置 | 说明 |
|:------------- |:-------------|
| `Link value comes from` | 将单元格显示为带有链接的呈现器时，此字段指定要在链接中使用的 "链接" 值的位置，使作者可以在网格中的其他列的下拉列表中进行选择。 例如，该单元可能是一个热度地图值，但您想要该链接打开行中资源 ID 的资源概述。 在这种情况下，您可以将链接值设置为来自 `Resource Id` 字段。
| `View to open` | 同上。 |
| `Menu item` | 同上。 |
| `Open link in Context Blade` | 同上。 |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure 资源管理器部署链接设置

如果所选链接类型为 `ARM Deployment` "作者"，则必须指定其他设置才能打开 Azure 资源管理器部署。 有两个用于配置的主要选项卡。

### <a name="template-settings"></a>模板设置

本部分定义模板应来自的位置以及用于运行 Azure 资源管理器部署的参数。

| 源 | 说明 |
|:------------- |:-------------|
|`Resource group id comes from` | 资源 ID 用于管理已部署的资源。 订阅用于管理已部署的资源和成本。 资源组与文件夹一起使用，以组织和管理所有资源。 如果未指定此值，部署将失败。 从 `Cell` 、 `Column` 、 `Static Value` 或 `Parameter` [链接源](#link-sources)中进行选择。|
|`ARM template URI from` | Azure 资源管理器模板本身的 URI。 需要为将部署模板的用户访问模板 URI。 从 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` [链接源](#link-sources)中进行选择。 对于初学者，请查看 [快速入门模板](https://azure.microsoft.com/resources/templates/)。|
|`ARM Template Parameters` | 本节定义用于上面定义的模板 URI 的模板参数。 这些参数将用于在 "运行" 页上部署模板。 该网格包含一个展开工具栏按钮，可帮助使用模板 URI 中定义的名称填充参数，并将其设置为静态空值。 仅当网格中没有参数并且已设置模板 URI 时，才可以使用此选项。 下半部分是参数输出的外观的预览。 选择 "刷新" 可更新当前更改的预览。 参数通常是值，而引用则可能指向用户有权访问的 keyvault 机密。 <br/><br/> **模板查看器边栏选项卡限制** -不会正确呈现引用参数，并显示为 null/值，因此用户将无法从 "模板查看器" 选项卡正确部署引用参数。|

![Azure 资源管理器模板设置的屏幕截图](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 设置

本部分配置用户在运行 Azure 资源管理器部署之前将看到的内容。

| 源 | 说明 |
|:------------- |:-------------|
|`Title from` | 在运行视图上使用的标题。 从 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` [链接源](#link-sources)中进行选择。|
|`Description from` | 这是 markdown 文本，用于在用户需要部署模板时为用户提供有帮助的说明。 从 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` [链接源](#link-sources)中进行选择。 <br/><br/> **注意：** 如果 `Static Value` 选择了，将显示多行文本框。 在此文本框中，您可以使用解析参数 `{paramName}` 。 还可以通过在列名称后面追加，将列视为参数 `_column` `{columnName_column}` 。 在下面的示例图中，可以 `VMName` 通过编写来引用列 `{VMName_column}` 。 冒号后的值为 [参数格式化程序](workbooks-parameters.md#parameter-options)，在本例中为 `value` 。|
|`Run button text from` | 用于 "运行 (执行) " 按钮用于部署 Azure 资源管理器模板的标签。 这是用户将选择开始部署 Azure 资源管理器模板的内容。|

![Azure 资源管理器 UX 设置的屏幕截图](./media/workbooks-link-actions/ux-settings.png)

设置这些配置后，当用户选择该链接时，将打开具有 UX 设置中所述 UX 的视图。 如果用户选择 `Run button text from` 此设置，将使用 [模板设置](#template-settings)中的值部署 Azure 资源管理器模板。 "查看模板" 将打开 "模板查看器" 选项卡，供用户在部署前检查模板和参数。

![运行 Azure 资源管理器视图的屏幕截图](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>自定义视图链接设置

使用此项可打开 Azure 门户中的自定义视图。 验证所有配置和设置。 不正确的值将导致门户中出现错误，或者无法正确打开视图。 可以通过两种方式通过或配置设置 `Form` `URL` 。

> [!NOTE]
> 无法在上下文选项卡中打开具有菜单的视图。如果将具有菜单的视图配置为在上下文选项卡中打开，则在选择该链接时不会显示任何上下文选项卡。

### <a name="form"></a>窗体

| 源 | 说明 |
|:------------- |:-------------|
|`Extension name` | 承载视图名称的扩展的名称。|
|`View name` | 要打开的视图的名称。|

#### <a name="view-inputs"></a>查看输入

有两种类型的输入：网格和 JSON。 用于 `Grid` 简单键和值选项卡输入，或选择 `JSON` 指定嵌套的 JSON 输入。

- Grid
    - `Parameter Name`：视图输入参数的名称。
    - `Parameter Comes From`： View 参数的值应来自的位置。 从 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` [链接源](#link-sources)中进行选择。
    > [!NOTE]
    > 如果 `Static Value` 选择了，则可以在文本框中使用方括号链接来解析参数 `{paramName}` 。 通过在列名称后面追加，可以将列视为参数列 `_column` `{columnName_column}` 。

    - `Parameter Value`：根据 `Parameter Comes From` ，这将是可用参数、列或静态值的下拉列表。

    ![编辑列的屏幕截图设置从窗体显示自定义视图设置。](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - 在编辑器中以 JSON 格式指定选项卡输入。 与 `Grid` 模式、参数和列一样，可以使用 `{paramName}` 参数和列来引用 `{columnName_column}` 。 如果选择 `Show JSON Sample` ，将显示视图输入的所有已解析参数和列用户的预期输出。

    ![显示 JSON 上带有 view input 的开放自定义视图设置的屏幕截图。](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

粘贴包含扩展名的门户 URL、视图的名称以及打开视图所需的任何输入。 选择后 `Initialize Settings` ，它将填充 `Form` 供作者添加/修改/删除任何视图输入的。

![显示列设置从 URL 显示自定义视图设置的屏幕截图。 ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>工作簿 (模板) 链接设置

如果所选链接类型为 `Workbook (Template)` ，则作者必须指定其他设置以打开正确的工作簿模板。 以下设置包含网格如何查找每个设置的相应值的选项。

| 设置 | 说明 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | 这是 "拥有" 工作簿的 Azure 资源的资源 ID。 通常是 Application Insights 资源或 Log Analytics 工作区。 在 Azure Monitor 中，这也可能是文字字符串 `"Azure Monitor"` 。 保存工作簿时，工作簿将链接到该工作簿。 |
| `Workbook resources` | 指定工作簿中使用的默认资源的 Azure 资源 Id 的数组。 例如，如果正在打开的模板显示虚拟机指标，则此处的值将是虚拟机资源 Id。  很多时候，所有者和资源设置为相同的设置。 |
| `Template Id` | 指定要打开的模板的 ID。 如果这是库中的社区模板 (最常见的情况) ，请将模板的路径作为前缀 `Community-` ，如 `Community-Workbooks/Performance/Apdex` `Workbooks/Performance/Apdex` 模板。 如果这是一个指向已保存工作簿/模板的链接，则它是该项目的完整 Azure 资源 ID。 |
| `Workbook Type` | 指定要打开的工作簿模板的类型。 最常见的情况是使用 `Default` 或 `Workbook` 选项来使用当前工作簿中的值。 |
| `Gallery Type` | 这将指定将显示在打开的模板的 "库" 视图中的库类型。 最常见的情况是使用 `Default` 或 `Workbook` 选项来使用当前工作簿中的值。 |
|`Location comes from` | 如果要打开特定工作簿资源，则应指定 "位置" 字段。 如果未指定位置，则查找工作簿内容的速度要慢得多。 如果知道位置，请指定它。 如果您不知道该位置或打开的模板没有特定位置，请将此字段保留为 "默认值"。|
|`Pass specific parameters to template` | 选择此参数可以将特定参数传递给模板。 如果选择此选项，则仅将指定的参数传递给模板，否则当前工作簿中的所有参数都将传递到模板，并且在这种情况下，此参数值在这两个工作簿中的参数 *名称* 必须相同。|
|`Workbook Template Parameters` | 本节定义传递给目标模板的参数。 该名称应与目标模板中的参数名称匹配。 从、、和中选择值 `Cell` `Column` `Parameter` `Static Value` 。 Name 和 value 不能为空以将该参数传递到目标模板。|

对于上述每个设置，作者必须选择链接工作簿中的值将来自的位置。 请参阅 [链接源](#link-sources)

打开工作簿链接时，将向新的工作簿视图传递所有从上述设置配置的值。

![工作簿链接设置的屏幕截图](./media/workbooks-link-actions/workbook-link-settings.png)

![工作簿模板参数设置的屏幕截图](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>链接源

| 源 | 说明 |
|:------------- |:-------------|
| `Cell` | 这将使用网格中该单元中的值作为链接值 |
| `Column` | 选中此选项后，将显示另一个字段，使作者可以在网格中选择另一列。  该行的列值将在链接值中使用。 这通常用于启用网格中的每一行，通过将 `Template Id` "字段" 设置为 `column` ，或打开不同资源的相同工作簿模板（如果 `Workbook resources` 字段设置为包含 Azure 资源 ID 的列）来打开不同的模板。 |
| `Parameter` | 选中此选项后，将显示另一个字段，让作者选择一个参数。 单击链接时，该参数的值将用于值 |
| `Static value` | 选中此选项后，将显示另一个字段，使作者键入将在链接工作簿中使用的静态值。 当网格中的所有行都为字段使用相同的值时，通常使用此值。 |
| `Step` | 使用工作簿的当前步骤中设置的值。 这是查询和度量值的常见步骤，用于将链接工作簿中的工作簿资源设置为 *查询/指标步骤* 中使用的资源，而不是当前工作簿 |
| `Workbook` | 使用在当前工作簿中设置的值。 |
| `Default` | 如果未指定任何值，则使用默认值。 这对于库类型很常见，其中默认库将由所有者资源的类型设置 |

## <a name="next-steps"></a>后续步骤

- [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
- 了解如何 [在工作簿中使用组](workbooks-groups.md)。