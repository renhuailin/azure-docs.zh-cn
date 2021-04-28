---
title: Azure Monitor 日志查询中的函数
description: 本文介绍了如何在 Azure Monitor 中从一个日志查询中使用函数调用另一个查询。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752814"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Azure Monitor 日志查询中的函数
函数是 Azure Monitor 中的日志查询，可在其他日志查询中使用，如同它是命令一样。 函数使开发人员可以为不同客户提供解决方案，并让你可在自己的环境中重复使用查询逻辑。 本文提供有关如何使用函数以及如何创建自己的函数的详细信息。

## <a name="types-of-functions"></a>函数类型
Azure Monitor 中有两种类型的函数：

- 解决方案函数：Azure Monitor 附带的预建函数。 这些函数在所有 Log Analytics 工作区中可用，无法修改。
- 工作区函数：在特定 Log Analytics 工作区中安装的函数，可以由用户进行修改和控制。

## <a name="viewing-functions"></a>查看函数
可以从 Log Analytics 工作区左窗格的“函数”选项卡查看当前工作区中的解决方案函数和工作区函数。 使用“筛选器”按钮可对列表中包含的函数进行筛选，使用“分组依据”可更改其分组 。 在“搜索”框中键入字符串可查找特定函数。 将鼠标悬停在函数上方可以查看其详细信息，包括说明和参数。

:::image type="content" source="media/functions/view-functions.png" alt-text="查看函数" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>使用函数
在查询中可使用函数，具体方法是使用任何参数的值键入其名称，就像在命令中键入一样。 函数的输出可以作为结果返回或通过管道传递给另一个命令。

双击函数名称，或是将鼠标悬停其上方，然后选择“在编辑器中使用”，可将函数添加到当前查询中。 随着你在查询中键入，工作区中的函数也会包含在 intellisense 中。 

如果查询需要参数，请使用以下语法提供它们：`function_name(param1,param2,...)`。

:::image type="content" source="media/functions/function-use.png" alt-text="使用函数" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>创建函数
若要在编辑器中通过当前查询创建函数，请选择“保存”，然后选择“保存为函数” 。 

:::image type="content" source="media/functions/function-save.png" alt-text="创建函数" lightbox="media/functions/function-save.png":::

在 Azure 门户中单击“保存”，然后提供下表中的信息，使用 Log Analytics 创建函数。

| 设置 | 说明 |
|:---|:---|
| 函数名称  | 函数的名称。 这不能包含空格或任何特殊字符。 它也不能以下划线 (_) 开头，因为此字符保留用于解决方案函数。 |
| 旧类别 | 用户定义的类别，用于帮助对函数进行筛选和分组。   |
| 保存为计算机组 | 将查询保存为[计算机组](computer-groups.md)。  |
| 参数 | 为函数中在使用时需要值的每个变量都添加一个参数。 有关详细信息，请参阅[函数参数](#function-parameters)。 |

:::image type="content" source="media/functions/function-details.png" alt-text="函数详细信息" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>函数参数 
可以为函数添加参数，以便在调用该函数时为某些变量提供值。 这样可在不同查询中使用同一函数，每个查询为参数提供不同值。 参数由以下属性定义。

| 设置 | 说明 |
|:---|:---|
| 类型  | 值的数据类型。 |
| 名称  | 参数的名称。 这是必须在查询中用于替换为参数值的名称。  |
| 默认值 | 在未提供值时要用于参数的值。 |

参数按照创建顺序进行排序，没有默认值的任何参数都位于具有默认值的参数前面。

## <a name="working-with-function-code"></a>使用函数代码
可以查看函数的代码以深入了解其工作原理或修改工作区函数的代码。 选择“加载函数代码”可将函数代码添加到编辑器中的当前查询。 如果将它添加到空查询或现有查询的第一行，则它会将函数名称添加到选项卡。如果它是工作区函数，则这会启用编辑函数详细信息的选项。

:::image type="content" source="media/functions/function-code.png" alt-text="加载函数代码" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>编辑函数
通过创建新查询，然后将鼠标悬停在该函数的名称上方并选择“加载函数代码”，可编辑函数的属性或代码。 对代码进行所需的任何修改并选择“保存”，然后选择“编辑函数详细信息” 。 在单击“保存”之前对函数的属性和参数进行所需的任何更改。

:::image type="content" source="media/functions/function-edit.png" alt-text="编辑函数" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>示例
下面的示例函数返回 Azure 活动日志中自特定日期以来，与特定类别匹配的所有事件。 

使用硬编码值从以下查询开始。 这会验证查询是否按预期方式工作。

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="示例函数 - 初始查询" lightbox="media/functions/example-query.png":::

接下来，将硬编码值替换为参数名称，然后通过选择“保存”并选择“保存为函数”来保存该函数 。

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="示例函数 - 保存函数" lightbox="media/functions/example-save-function.png":::

 为函数属性提供以下值。

| 属性 | 值 |
|:---|:---|
| 函数名称 | AzureActivityByCategory |
| 旧类别 | 演示函数 |

在保存函数之前定义以下参数。

| 类型 | 名称 | 默认值 |
|:---|:---|:---|
| string   | CategoryParam | “Administrative” |
| datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="示例函数 - 函数属性" lightbox="media/functions/example-function-properties.png":::

创建一个新查询，并通过鼠标悬停在其上方来查看新函数。 请注意参数的顺序，因为这是在使用函数时必须指定的顺序。

:::image type="content" source="media/functions/example-view-details.png" alt-text="示例函数 - 查看详细信息" lightbox="media/functions/example-view-details.png":::

选择“在编辑器中使用”以将新函数添加到查询中，然后添加参数的值。 请注意，无需指定 CategoryParam 的值，因为它具有默认值。

:::image type="content" source="media/functions/example-use-function.png" alt-text="示例函数 - 使用函数" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>后续步骤
参阅有关编写 Azure Monitor 日志查询的其他课：

- [字符串操作](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

