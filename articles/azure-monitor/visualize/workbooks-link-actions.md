---
title: Azure Monitor 工作簿链接操作
description: 如何使用 Azure Monitor 工作簿中的链接操作
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100607660"
---
# <a name="link-actions"></a>链接操作

可以通过工作簿链接步骤或者[网格](../visualize/workbooks-grid-visualizations.md)、[标题](../visualize/workbooks-tile-visualizations.md)或[图形](../visualize/workbooks-graph-visualizations.md)的列设置来访问链接操作。

## <a name="general-link-actions"></a>常规链接操作

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Generic Details` | 在属性网格上下文视图中显示行值。 |
| `Cell Details` | 在属性网格上下文视图中显示单元格值。 当单元包含带有信息的动态类型（例如，包含位置、角色实例等请求属性的 JSON）时，此操作非常有用。 |
| `Url` | 单元格的值应该是有效的 HTTP URL，此单元格将是在新选项卡中打开该 URL 的链接。|

## <a name="application-insights"></a>Application Insights

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Custom Event Details` | 使用单元格中的自定义事件 ID (`itemId`) 打开 Application Insights 搜索详细信息。 |
| `* Details` | 类似于自定义事件详细信息，只是在依赖项、异常、页面视图、请求和跟踪方面不同。 |
| `Custom Event User Flows` | 打开围绕单元格中自定义事件名称构建的 Application Insights 用户流体验。 |
| `* User Flows` | 类似于自定义事件用户流，只是在异常、页面视图和请求方面不同。 |
| `User Timeline` | 使用单元格中的用户 ID (`user_Id`) 打开用户时间线。 |
| `Session Timeline` | 针对单元格中的值打开 Application Insights 搜索体验（例如，当单元格中的值为 abc 时，搜索文本“abc”）。 |

`*` 表示上表的通配符

## <a name="azure-resource"></a>Azure 资源

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `ARM Deployment` | 部署 Azure 资源管理器模板。  选择此项后，会显示其他字段，使作者可以配置要打开哪个 Azure 资源管理器模板、模板的参数，等等。[请参阅 Azure 资源管理器部署链接设置](#azure-resource-manager-deployment-link-settings)。 |
| `Create Alert Rule` | 创建资源的警报规则。  |
| `Custom View` | 打开自定义视图。 选择此项后，会显示其他字段，使作者可以配置视图扩展、视图名称用于打开视图的任何参数。 [请参阅自定义视图](#custom-view-link-settings)。 |
| `Metrics` | 打开指标视图。  |
| `Resource overview` | 根据单元格中的资源 ID 值在门户中打开资源的视图。 作者还可以选择根据需要设置 `submenu` 值，该值会在资源视图中打开特定菜单项。 |
| `Workbook (template)` | 打开工作簿模板。  选择此项后，会显示其他字段，使作者可以配置要打开哪个模板等等。  |

## <a name="link-settings"></a>链接设置

使用链接呈现器时，有以下设置可用：

![链接设置的屏幕截图](./media/workbooks-link-actions/link-settings.png)

| 设置 | 说明 |
|:------------- |:-------------|
| `View to open` | 允许作者选择上面枚举的操作之一。 |
| `Menu item` | 如果已选择“资源概述”，则这是要打开的资源概述中的菜单项。 这可用于打开警报或活动日志而非资源的“概述”。 每个 Azure `Resourcetype` 的菜单项值并不相同。|
| `Link label` | 如果已指定该项，此值会显示在网格列中。 如果未指定此值，则会显示单元格的值。 如果希望显示其他值（如热度地图或图标），请不要使用 `Link` 呈现器，而应使用相应的呈现器并选择“`Make this item a link`”选项。 |
| `Open link in Context Blade` | 如果已指定该项，链接会在窗口右侧以弹出的“上下文”视图形式打开，而不是以完整视图形式打开。 |

使用“`Make this item a link`”选项时，有以下设置可用：

| 设置 | 说明 |
|:------------- |:-------------|
| `Link value comes from` | 将单元格显示为带链接的呈现器时，此字段指定要在链接中使用的“link”值来自何处，使作者可以在网格中的其他列的下拉列表中进行选择。 例如，单元格可能是一个热度地图值，但你想要该链接打开行中资源 ID 的“资源概述”。 在这种情况下，可以将“link”值设置为来自“`Resource Id`”字段。
| `View to open` | 同上。 |
| `Menu item` | 同上。 |
| `Open link in Context Blade` | 同上。 |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure 资源管理器部署链接设置

如果所选链接类型为“`ARM Deployment`”，则作者必须指定其他设置才能打开 Azure 资源管理器部署。 有两个用于配置的主要选项卡。

### <a name="template-settings"></a>模板设置

此部分定义模板应来自何处，以及用于运行 Azure 资源管理器部署的参数。

| 源 | 说明 |
|:------------- |:-------------|
|`Resource group id comes from` | 资源 ID 用于管理已部署的资源。 订阅用于管理已部署的资源和成本。 资源组可以像文件夹一样用来组织和管理所有资源。 如果未指定此值，部署会失败。 从[链接源](#link-sources)内的 `Cell`、`Column`、`Static Value` 或 `Parameter` 中进行选择。|
|`ARM template URI from` | Azure 资源管理器模板本身的 URI。 模板 URI 需要可供将要部署模板的用户访问。 从[链接源](#link-sources)内的 `Cell`、`Column`、`Parameter` 或 `Static Value` 中进行选择。 初学者可参阅我们的[快速启动模板](https://azure.microsoft.com/resources/templates/)。|
|`ARM Template Parameters` | 此部分定义了用于上面定义的模板 URI 的模板参数。 这些参数将用于在运行页上部署模板。 网格包含一个展开工具栏按钮，有助于用户使用模板 URI 中定义的名称来填充参数，并将其设置为静态空值。 只有在网格中没有参数且模板 URI 已经过设置的情况下，才可以使用此选项。 下半部分是参数输出的外观的预览。 选择“刷新”可以使用当前的更改更新预览。 参数通常是值，而引用则是可能指向用户有权访问的 keyvault 机密的项。 <br/><br/> “模板查看器”边栏选项卡限制 - 不会正确呈现引用参数，只是会将其显示为 null/值，因此用户将无法从“模板查看器”选项卡正确部署引用参数。|

![Azure 资源管理器模板设置的屏幕截图](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 设置

此部分配置用户在运行 Azure 资源管理器部署之前会看到的内容。

| 源 | 说明 |
|:------------- |:-------------|
|`Title from` | 在运行视图上使用的标题。 从[链接源](#link-sources)内的 `Cell`、`Column`、`Parameter` 或 `Static Value` 中进行选择。|
|`Description from` | 这是 Markdown 文本，用于在用户需要部署模板时为用户提供有用的说明。 从[链接源](#link-sources)内的 `Cell`、`Column`、`Parameter` 或 `Static Value` 中进行选择。 <br/><br/> **注意：** 如果选择“`Static Value`”，则会显示一个多行文本框。 在此文本框中，可以使用 `{paramName}` 来解析参数。 还可以通过在列名后面追加 `_column`（如 `{columnName_column}`）将列视为参数。 在下面的示例图中，可以通过编写 `{VMName_column}` 来引用列 `VMName`。 冒号后的值是[参数格式化程序](../visualize/workbooks-parameters.md#parameter-options)，在此示例中为 `value`。|
|`Run button text from` | 在部署 Azure 资源管理器模板所需的运行（执行）按钮上使用的标签。 这是用户开始部署 Azure 资源管理器模板时将要选择的内容。|

![Azure 资源管理器 UX 设置的屏幕截图](./media/workbooks-link-actions/ux-settings.png)

设置这些配置后，当用户选择该链接时，它就会打开包含 UX 设置中所述 UX 的视图。 如果用户选择“`Run button text from`”，它会使用[模板设置](#template-settings)中的值部署 Azure 资源管理器模板。 “查看模板”按钮会打开“模板查看器”选项卡，供用户在部署前检查模板和参数。

![“运行 Azure 资源管理器”视图的屏幕截图](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>自定义视图链接设置

使用此项可在 Azure 门户中打开“自定义视图”。 验证所有配置和设置。 值不正确会导致门户中出现错误，或者无法正确打开视图。 可以使用两种方法通过 `Form` 或 `URL` 来配置设置。

> [!NOTE]
> 无法在上下文选项卡中打开具有菜单的视图。如果将具有菜单的视图配置为在上下文选项卡中打开，则在选择链接时不会显示任何上下文选项卡。

### <a name="form"></a>窗体

| 源 | 说明 |
|:------------- |:-------------|
|`Extension name` | 承载视图名称的扩展的名称。|
|`View name` | 要打开的视图的名称。|

#### <a name="view-inputs"></a>视图输入

有两种类型的输入：网格和 JSON。 使用“`Grid`”进行简单键和值选项卡输入，或选择“`JSON`”以指定嵌套式 JSON 输入。

- 网格
    - `Parameter Name`：视图输入参数的名称。
    - `Parameter Comes From`：视图参数的值应来自何处。 从[链接源](#link-sources)内的 `Cell`、`Column`、`Parameter` 或 `Static Value` 中进行选择。
    > [!NOTE]
    > 如果选择“`Static Value`”，则可使用文本框中的方括号链接 `{paramName}` 来解析参数。 可以通过在列名后追加 `_column`（如 `{columnName_column}`）将列视为参数列。

    - `Parameter Value`：这将是一个包含可用参数、列或静态值的下拉列表，具体取决于“`Parameter Comes From`”选项。

    ![“编辑列设置”的屏幕截图，显示将从表单获取自定义视图设置。](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - 在编辑器中以 JSON 格式指定选项卡输入。 与“`Grid`”模式一样，引用参数和列时，可以使用 `{paramName}` 来表示参数，使用 `{columnName_column}` 来表示列。 选择“`Show JSON Sample`”会显示用于视图输入的所有已解析参数和列的预期输出。

    ![屏幕截图，显示了“打开自定义视图”设置，其中的视图输入采用 JSON 格式。](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

粘贴一个门户 URL，其中包含扩展、视图名称以及打开视图所需的任何输入。 在选择“`Initialize Settings`”后，它会填充供作者添加/修改/删除任何视图输入所需的“`Form`”。

![“编辑列设置”的屏幕截图，显示将从 URL 中获取自定义视图设置。 ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>工作簿（模板）链接设置

如果所选链接类型为“`Workbook (Template)`”，则作者必须指定其他设置才能打开正确的工作簿模板。 以下设置包含网格如何查找每个设置的相应值的选项。

| 设置 | 说明 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | 这是“拥有”工作簿的 Azure 资源的资源 ID。 通常情况下，它是 Application Insights 资源或 Log Analytics 工作区。 在 Azure Monitor 中，它也可能是文本字符串 `"Azure Monitor"`。 保存工作簿时，这是工作簿要链接到的位置。 |
| `Workbook resources` | Azure 资源 ID 的数组，用于指定工作簿中使用的默认资源。 例如，如果将要打开的模板显示虚拟机指标，则此处的值将是虚拟机资源 ID。  很多时候，所有者和资源设置为相同的设置。 |
| `Template Id` | 指定要打开的模板的 ID。 如果这是库中的社区模板（最常见的情况），请为模板的路径加上 `Community-` 前缀。例如，`Workbooks/Performance/Apdex` 模板的路径加上该前缀后变为 `Community-Workbooks/Performance/Apdex`。 如果这是一个指向已保存工作簿/模板的链接，则它是该项的完整 Azure 资源 ID。 |
| `Workbook Type` | 指定要打开的工作簿模板的类型。 最常见的情况是通过“`Default`”或“`Workbook`”选项来使用当前工作簿中的值。 |
| `Gallery Type` | 此项指定会显示在已打开模板的“库”视图中的库类型。 最常见的情况是通过“`Default`”或“`Workbook`”选项来使用当前工作簿中的值。 |
|`Location comes from` | 如果要打开特定工作簿资源，则应指定位置字段。 如果未指定位置，则查找工作簿内容的速度会慢得多。 如果知道位置，请指定它。 如果不知道位置或打开的模板没有特定位置，请将此字段保留为“Default”。|
|`Pass specific parameters to template` | 选择此项可以将特定参数传递给模板。 如果选择了此项，则仅将指定的参数传递给模板。否则，当前工作簿中的所有参数都会传递给模板，这种情况下，两个工作簿中的参数 names 必须相同，此参数值才能生效。|
|`Workbook Template Parameters` | 此部分定义传递给目标模板的参数。 名称应与目标模板中的参数的名称匹配。 从“`Cell`”、“`Column`”、“`Parameter`”和“`Static Value`”中选择值。 名称和值不得为空，否则无法将该参数传递给目标模板。|

对于上述每项设置，作者必须选择已链接工作簿中的值将来自何处。 请参阅[链接源](#link-sources)

打开工作簿链接时，将向新的工作簿视图传递在上述设置中配置的所有值。

![工作簿链接设置的屏幕截图](./media/workbooks-link-actions/workbook-link-settings.png)

![工作簿模板参数设置的屏幕截图](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>链接源

| 源 | 说明 |
|:------------- |:-------------|
| `Cell` | 此项将使用网格中该单元格中的值作为链接值 |
| `Column` | 选择此项后，会显示另一字段，使作者可以在网格中选择另一列。  与行对应的该列的值将用在链接值中。 这通常用于使网格的每一行都能通过将“`Template Id`”字段设置为“`column`”来打开不同的模板，或者使每一行都能为不同资源打开相同的工作簿模板，前提是已将“`Workbook resources`”字段设置为包含 Azure 资源 ID 的列。 |
| `Parameter` | 选择此项后，会显示另一字段，使作者可以选择一个参数。 单击链接时，该参数的值将用于值 |
| `Static value` | 选择此项后，会显示另一字段，使作者可以键入将在已链接工作簿中使用的静态值。 这通常适用于网格中的所有行都会对某个字段使用同一值的情形。 |
| `Step` | 使用在工作簿的当前步骤中设置的值。 这常见于查询和指标步骤，目的是将已链接工作簿中的工作簿资源设置为在查询/指标步骤中使用的工作簿资源，而不是在当前工作簿中使用的工作簿资源 |
| `Workbook` | 使用在当前工作簿中设置的值。 |
| `Default` | 使用在未指定任何值的情况下会使用的默认值。 这常见于库类型，其中的默认库将根据所有者资源的类型来设置 |

## <a name="next-steps"></a>后续步骤

- [控制](../visualize/workbooks-access-control.md)并共享对工作簿资源的访问权限。
- 了解如何使用[工作簿中的组](../visualize/workbooks-groups.md)。