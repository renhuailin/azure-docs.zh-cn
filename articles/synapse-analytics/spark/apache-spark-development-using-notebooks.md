---
title: 如何使用 Synapse 笔记本
description: 本文介绍如何创建和开发 Synapse 笔记本，以便进行数据准备和可视化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/08/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 244d7b7d2ff6fe88b883b2e8adbeeaa0e7fb167e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593223"
---
# <a name="create-develop-and-maintain-synapse-notebooks-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中创建、开发和维护 Synapse 笔记本

Synapse 笔记本是一种 Web 界面，用于创建包含实时代码、可视化效果和叙述性文本的文件。 笔记本是验证想法并使用快速试验从数据中获取见解的好地方。 笔记本还广泛用于数据准备、数据可视化、机器学习和其他大数据方案。

使用 Synapse 笔记本，可以： 

* 无需设置即可开始工作。
* 利用内置企业安全功能确保数据安全。
* 针对 Spark 和 SQL，分析跨原始格式（CSV、txt、JSON 等）、已处理的文件格式（parquet、Delta Lake、ORC 等）以及 SQL 表格数据文件的数据。
* 利用增强的创作功能和内置的数据可视化功能提高工作效率。

本文介绍如何在 Synapse Studio 中使用笔记本。

## <a name="preview-of-the-new-notebook-experience"></a>全新笔记本体验预览
Synapse 团队将新的笔记本组件引入 Synapse Studio，为 Microsoft 客户提供一致的笔记本体验，并最大限度地提高可发现性、生产力、共享和协作功能。 全新笔记本体验已就绪，可供预览。 检查笔记本工具栏中的“预览功能”按钮将其打开。 下表提供现有笔记本（称为“经典笔记本”）与新预览版之间的功能比较。  

|功能|经典笔记本|预览笔记本|
|--|--|--|
|%run| 不支持 | &#9745;|
|%history| 不支持 |&#9745;|
|%load| 不支持 |&#9745;|
|%%html| 不支持 |&#9745;|
|拖放移动单元格| 不支持 |&#9745;|
|大纲（目录）| 不支持 |&#9745;|
|变量资源管理器| 不支持 |&#9745;|
|通过工具栏按钮设置文本单元格的格式|&#9745;| 不支持|
|代码单元格注释| 不支持 | &#9745;|

> [!NOTE]
> 变量资源管理器仅支持 Python。
## <a name="create-a-notebook"></a>创建笔记本

可通过两种方法创建笔记本。 可以从“对象资源管理器”创建新笔记本或将现有笔记本导入到 Synapse 工作区。 Synapse 笔记本可识别标准 Jupyter Notebook IPYNB 文件。

![新建或导入笔记本的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>开发笔记本

笔记本由单元格组成，单元格是单独的代码块或可单独运行或作为组运行的文本块。

我们提供有丰富的操作供你开发笔记本：
+ [添加单元格](#add-a-cell)
+ [设置主要语言](#set-a-primary-language)
+ [使用多种语言](#use-multiple-languages)
+ [使用临时表跨语言引用数据](#use-temp-tables-to-reference-data-across-languages)
+ [IDE 样式 IntelliSense](#ide-style-intellisense)
+ [代码片段](#code-snippets)
+ [通过工具栏按钮设置文本单元格的格式](#format-text-cell-with-toolbar-buttons)
+ [撤消/重做单元格操作](#undo-redo-cell-operation)
+ [代码单元格注释](#Code-cell-commenting)
+ [移动单元格](#move-a-cell)
+ [删除单元格](#delete-a-cell)
+ [折叠单元格输入](#collapse-a-cell-input)
+ [折叠单元格输出](#collapse-a-cell-output)
+ [笔记本大纲](#notebook-outline)

<h3 id="add-a-cell">添加单元格</h3>

有多种方法可向笔记本添加新单元格。

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

1. 展开左上方“+ 单元格”按钮，然后选择“添加代码单元格”或“添加文本单元格”  。

    ![使用单元格按钮添加单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 将鼠标悬停在两个单元格之间的空白地方，然后选择“添加代码”或“添加文本” 。

    ![在空白之间添加代码的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 A 在当前单元格上方插入单元格。 按 B 在当前单元格下方插入单元格。


# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

1. 将鼠标悬停在两个单元格之间的空白地方，然后选择“代码”或“Markdown” 。
    ![使用单元格按钮添加 Azure Notebook 单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)

2. 使用 [aznb 命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 A 在当前单元格上方插入单元格。 按 B 在当前单元格下方插入单元格。

---

<h3 id="set-a-primary-language">设置主要语言</h3>

Synapse 笔记本支持四种 Apache Spark 语言：

* PySpark (Python)
* Spark (Scala)
* Spark SQL
* .NET Spark (C#)

可以从顶部命令栏中的下拉列表为新添加的单元格设置主要语言。

   ![默认 Synapse 语言的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-default-language.png)


<h3 id="use-multiple-languages">使用多种语言</h3>

通过在单元格开始位置指定正确的语言 magic 命令，可以在一个笔记本中使用多种语言。 下表列出了用于切换单元格语言的 magic 命令。

|magic 命令 |语言 | 说明 |  
|---|------|-----|
|%%pyspark| Python | 针对 Spark 上下文执行 Python 查询。  |
|%%spark| Scala | 针对 Spark 上下文执行 Scala 查询。  |  
|%%sql| SparkSQL | 针对 Spark 上下文执行 SparkSQL 查询。  |
|%%csharp | .NET for Spark C# | 针对 Spark 上下文执行 .NET for Spark C# 查询。 |

下图是一个示例，说明如何在 Spark(Scala) 笔记本中使用 %%pyspark magic 命令编写 PySpark 查询，或使用 %%sql magic 命令编写 SparkSQL 查询  。 请注意，笔记本的主要语言设置为 pySpark。

   ![Synapse Spark magic 命令的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)


<h3 id="use-temp-tables-to-reference-data-across-languages&quot;>使用临时表跨语言引用数据</h3>

不能直接在 Synapse 笔记本中跨不同语言引用数据或变量。 在 Spark 中，可以跨语言引用临时表。 下面是一个示例，说明如何使用 Spark 临时表作为解决方法，在 `PySpark` 和 `SparkSQL` 中读取 `Scala` 数据帧。

1. 在单元格 1 中，使用 Scala 从 SQL 池连接器读取 DataFrame，并创建一个临时表。

   ```scala
   %%spark
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. 在单元格 2 中，使用 Spark SQL 查询数据。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在单元格 3 中，使用 PySpark 中的数据。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

<h3 id="ide-style-intellisense">IDE 样式 IntelliSense</h3>

Synapse 笔记本集成了 Monaco 编辑器，将 IDE 样式的 IntelliSense 引入到了单元格编辑器中。 语法突出显示、错误标记和自动代码补全功能有助于你编写代码并更快地找出问题。

对于不同的语言，IntelliSense 功能处于不同的成熟度级别。 请参阅下表了解支持的功能。

|Languages| 语法突出显示 | 语法错误标记  | 语法代码补全 | 变量代码补全| 系统函数代码补全| 用户函数代码补全| 智能缩进 | 代码折叠|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|是|是|是|是|是|是|是|是|
|Spark (Scala)|是|是|是|是|是|是|-|是|
|SparkSQL|是|是|是|是|是|-|-|-|
|.NET for Spark (C#)|是|是|是|是|是|是|是|是|

>[!Note]
> 需要建立活动 Spark 会话，才能在 .NET for Spark (C#) 中享受变量代码完成、系统函数代码完成和用户函数代码完成所带来的好处。

<h3 id="code-snippets">代码段</h3>

Synapse 笔记本提供代码片段，可更轻松地输入常用的代码模式，例如配置 Spark 会话、将数据作为 Spark 数据帧读取，或者使用 matplotlib 等绘制图表。

代码片段与其他建议混合在一起显示在 [IDE 样式 IntelliSense 的快捷键](#ide-style-intellisense)中。 代码片段内容与代码单元格语言一致。 可通过在代码单元格编辑器中键入“代码片段”或代码片段标题中出现的任何关键字来查看可用的代码片段。 例如，通过键入“读取”，可查看用于从各种数据源读取数据的代码片段列表。

![Synapse 代码片段的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)


<h3 id="format-text-cell-with-toolbar-buttons">通过工具栏按钮设置文本单元格的格式</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

可以使用文本单元格工具栏中的格式按钮执行常见的 markdown 操作。 它包括将文本设为粗体、将文本设为斜体、插入代码片段、插入未排序列表、插入已排序列表以及插入 URL 中的图像。

  ![Synapse 文本单元格工具栏的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

预览笔记本体验尚不提供“格式”按钮工具栏。 

---

<h3 id="undo-redo-cell-operation">撤消/重做单元格操作</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

选择“撤消 / 重做”按钮或按 Ctrl+Z / Ctrl+Y 可撤销最近的单元格操作   。 现在可以撤消/重做最近的 20 个历史单元格操作。 

   ![Synapse 撤消单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

选择“撤消 / 重做”按钮或按 Z / Shift+Z 可撤销最近的单元格操作   。 现在可以撤消/重做最近的 10 个历史单元格操作。

   ![aznb 的 Synapse 撤消单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-undo-cells-aznb.png)

支持的撤消单元格操作：
+ 插入/删除单元格：可以通过选择“撤消”来撤销删除操作，文本内容将与单元格一起保留。
+ 对单元格进行重新排序。
+ 切换参数。
+ 在代码单元格和 Markdown 单元格之间进行转换。

> [!NOTE]
> 单元格内文本操作和代码单元格注释操作是不可撤消的。
> 现在可以撤消/重做最近的 10 个历史单元格操作。


---

<h3 id="Code-cell-commenting">代码单元格注释</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

不支持。

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

目前，我们支持在预览笔记本中对代码单元格进行注释。

1. 在笔记本工具栏上选择“注释”按钮以打开“注释”窗格 。

   ![Synapse 注释按钮的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-comments-button.png)

2. 在代码单元格中选择代码，单击“注释”窗格中的“新建”，添加注释，然后单击“发布注释”按钮以保存  。

   ![Synapse 新建注释的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-new-comments.png)

3. 可以通过单击注释旁的“更多”按钮来执行“编辑注释”、“解析线程”或“删除线程”的操作   。 

   ![Synapse 编辑注释的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-edit-comments.png)

---


<h3 id="move-a-cell">移动单元格</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

选择省略号 (…) 以访问最右侧的其他单元格操作菜单。 然后选择“上移单元格”或“下移单元格”移动当前单元格 。 

还可使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 Ctrl+Alt+↑，上移当前单元格。 按 Ctrl+Alt+↓，下移当前单元格。

   ![移动单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

单击单元格左侧，然后将其拖到所需位置。 
    ![Synapse 移动单元格的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

<h3 id="delete-a-cell">删除单元格</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

要删除单元格，请选择省略号 (…) 以访问最右侧的其他单元格操作菜单，然后选择“删除单元格”。 

还可使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 D,D，删除当前单元格。
  
   ![删除单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

若要删除单元格，请选择单元格右侧的删除按钮。 

还可使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 Shift+D 删除当前单元格。 

   ![Azure Notebook 删除单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

<h3 id="collapse-a-cell-input">折叠单元格输入</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

选择当前单元格底部的箭头按钮将其折叠。 若要展开，请在单元格处于折叠状态时选择箭头按钮。

   ![折叠单元格输入的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

 在单元格工具栏上选择“更多命令”省略号 (...) 和“隐藏输入”以折叠当前单元格的输入。 若要展开它，请在单元格处于折叠状态时选择“显示输入”。

   ![Azure Notebook 折叠单元格输入的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

<h3 id="collapse-a-cell-output">折叠单元格输出</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

选择当前单元格输出左上角的“折叠输出”按钮，将其折叠。 要展开，请在单元格输出处于折叠状态时选择“显示单元格输出”。

   ![折叠单元格输出的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

 在单元格工具栏上选择“更多命令”省略号 (...) 和“隐藏输出”以折叠当前单元格的输出。 若要展开它，请在单元格的输出处于隐藏状态时选择“显示输出”。

   ![Azure Notebook 折叠单元格输出的动画 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

<h3 id="notebook-outline">笔记本大纲</h3>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

不支持。

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

大纲（目录）在边栏窗口中显示任何 markdown 单元的第一个 markdown 标题，用于快速导航。 大纲边栏可根据最适合屏幕的方式调整大小和折叠。 你可以选择笔记本命令栏上的“大纲”按钮，以打开或隐藏边栏

![Azure Notebook 大纲的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-outline.png)

---


## <a name="run-notebooks"></a>运行笔记本

你可以逐个或同时在笔记本中运行代码单元格。 每个单元格的状态和进度都在笔记本中表示。

### <a name="run-a-cell"></a>运行单元格

可以通过多种方法在单元格中运行代码。

1. 将鼠标悬停在要运行的单元格上，并选择“运行单元格”按钮，或按 Ctrl+Enter 。

   ![运行单元格 1 的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 Shift+Enter 运行当前单元格并选择下面的单元格。 按 Alt+Enter 运行当前单元格并在下面插入一个新单元格。

---

### <a name="run-all-cells"></a>运行所有单元格
选择“全部运行”按钮，按顺序运行当前笔记本中的所有单元格。

   ![运行所有单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>运行上方或下方的所有单元格

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

若要访问最右侧的其他单元格操作菜单，请选择省略号 (…)。然后选择“运行上方的单元格”，按顺序运行当前单元格上方的所有单元格 。 选择“运行下方的单元格”，按顺序运行当前单元格下方的所有单元格。

   ![运行上方或下方单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

展开“全部运行”中的下拉列表，然后选择“运行上面的单元”，按顺序运行当前单元格上方的所有单元格。  选择“运行下方的单元格”，按顺序运行当前单元格下方的所有单元格。

   ![Azure Notebook 运行上方或下方单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>取消所有正在运行的单元格

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)
选择“全部取消”按钮可取消正在运行的单元格或在队列中等待的单元格。 
   ![取消所有单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

选择“全部取消”按钮可取消正在运行的单元格或在队列中等待的单元格。 
   ![Azure Notebook 取消所有单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>笔记本引用

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

不支持。

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

可以使用 ```%run <notebook path>``` magic 命令在当前笔记本上下文中引用另一个笔记本。 引用笔记本中定义的所有变量在当前笔记本中都可用。 ```%run``` magic 命令支持嵌套调用，但不支持递归调用。 如果语句超过五行，你将收到异常。  

示例：``` %run /<path>/Notebook1 { "parameterInt": 1, "parameterFloat": 2.5, "parameterBool": true,  "parameterString": "abc" } ```。

可以在交互模式和 Synapse 管道中使用笔记本引用。

> [!NOTE]
> - ```%run``` 命令当前只支持将绝对路径或笔记本名称仅作为参数传递，不支持相对路径。 
> - ```%run``` 命令当前仅支持 4 种参数值类型：`int`、`float`、`bool` 和 `string`，不支持变量替换操作。
> - 需要发布引用的笔记本。 需要发布笔记本才能引用它们。 Synapse Studio无法识别 Git 存储库中未发布的笔记本。 
> - 引用的笔记本不支持超过五行的语句。
>

---

### <a name="variable-explorer"></a>变量资源管理器

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

不支持。

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

Synapse 笔记本提供内置的变量资源管理器，让你可以在 PySpark (Python) 单元的当前 Spark 会话中查看变量名称、类型、长度和值的列表。 在代码单元中定义多个变量时，系统会自动显示它们。 单击每个列标题时，表中的变量会进行排序。

你可以选择笔记本命令栏上的“变量”按钮，以打开或隐藏变量资源管理器。

![Azure Notebook 变量资源管理器的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)


---

### <a name="cell-status-indicator"></a>单元格状态指示器

逐步单元格执行状态显示在单元格下方，有助于你查看其当前进度。 单元格运行完成后，将显示具有总持续时间和结束时间的执行摘要，并将其保留在此处供将来参考。

![单元格状态的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 进度指示器

Synapse 笔记本仅基于 Spark。 代码单元格在无服务器 Apache Spark 池上远程执行。 Spark 作业进度指示器提供有实时进度栏，可帮助你了解作业执行状态。
每个作业或阶段的任务数有助于识别 spark 作业的并行级别。 你还可以通过选择作业（或阶段）名称上的链接，深入了解特定作业（或阶段）的 Spark UI。


![Spark 进度指示器的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 会话配置

可以在“配置会话”中为当前 Spark 会话指定超时持续时间、数量和执行器的大小。 重启 Spark 会话，以使配置更改生效。 缓存的所有笔记本变量都将被清除。

[![会话管理的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark 会话 config magic 命令
还可以通过 magic 命令 %%configure 指定 spark 会话设置。 spark 会话需要重启才能使设置生效。 建议你在笔记本开头运行 %%configure。 下面是一个示例，有关有效参数的完整列表，请参阅 https://github.com/cloudera/livy#request-body 。 

```json
%%configure
{
    //You can get a list of valid parameters to config the session from https://github.com/cloudera/livy#request-body.
    "driverMemory":"28g", // Recommended values: ["28g", "56g", "112g", "224g", "400g", "472g"]
    "driverCores":4, // Recommended values: [4, 8, 16, 32, 64, 80]
    "executorMemory":"28g",
    "executorCores":4, 
    "jars":["abfs[s]://<file_system>@<account_name>.dfs.core.windows.net/<path>/myjar.jar","wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>/myjar1.jar"],
    "conf":{
    //Example of standard spark property, to find more available properties please visit:https://spark.apache.org/docs/latest/configuration.html#application-properties.
        "spark.driver.maxResultSize":"10g",
    //Example of customized property, you can specify count of lines that Spark SQL returns by configuring "livy.rsc.sql.num-rows".
        "livy.rsc.sql.num-rows&quot;:&quot;3000" 
    }
}
```
> [!NOTE]
> - 建议在 %%configure 中将“DriverMemory”和“ExecutorMemory”设置为相同的值，对“driverCores”和“executorCores”也进行同样的设置。
> - 可以在 Synapse 管道中使用 Spark 会话配置 magic 命令。 该命令只有在最高级别调用时才会生效。 将忽略引用的笔记本中使用的 %%configure。
> - 必须在“conf”正文中使用 Spark 配置属性。 不支持 Spark 配置属性的顶级引用。
>

## <a name="bring-data-to-a-notebook"></a>将数据引入笔记本

如以下代码示例所示，可以从 Azure Blob 存储、Azure Data Lake Store Gen 2 和 SQL 池加载数据。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>从 Azure Data Lake Store Gen2 读取 CSV 作为 Spark 数据帧

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>从 Azure Blob 存储读取 CSV 作为 Spark 数据帧

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>从主存储帐户读取数据

你可以直接访问主存储帐户中的数据。 无需提供密钥。 在数据资源管理器中，右键单击某个文件，然后选择“新建笔记本”以查看自动生成了数据提取器的新笔记本。

![数据引入到单元格的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)


## <a name="ipython-widgets"></a>IPython 小组件


# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

不支持。

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

小组件是事件化的 python 对象，在浏览器中具有表示形式，通常作为控件（例如滑块、文本框等）提供。IPython 小组件仅适用于 Python 环境，在其他语言（例如 Scala、SQL、C#）中尚不受支持。 

### <a name="to-use-ipython-widget"></a>使用 IPython 小组件
1. 需要首先导入 `ipywidgets` 模块才能使用 Jupyter 小组件框架。
   ```python
   import ipywidgets as widgets
   ```
2. 你可以使用顶级 `display` 函数来呈现小部件，或者在代码单元格的最后一行保留 widget 类型的表达式。
   ```python
   slider = widgets.IntSlider()
   display(slider)
   ```

   ```python
   slider = widgets.IntSlider()
   slider
   ```
   
3. 运行单元格，小组件将显示在输出区域。

   ![IPython 小组件滑块的屏幕截图](./media/apache-spark-development-using-notebooks/ipython-widgets-slider.png)

4. 你可以使用多个 `display()` 调用来多次呈现同一小组件实例，但它们会相互保持同步。

   ```python
   slider = widgets.IntSlider()
   display(slider)
   display(slider)
   ```

   ![IPython 小组件滑块的屏幕截图](./media/apache-spark-development-using-notebooks/ipython-widgets-multiple-sliders.png)

5. 若要呈现两个彼此独立的小组件，请创建两个小组件实例：

   ```python
   slider1 = widgets.IntSlider()
   slider2 = widgets.IntSlider()
   display(slider1)
   display(slider2)
   ```


### <a name="supported-widgets"></a>支持的小组件

|小组件类型|小组件|
|--|--|
|数字小组件|IntSlider、FloatSlider、FloatLogSlider、IntRangeSlider、FloatRangeSlider、IntProgress、FloatProgress、BoundedIntText、BoundedFloatText、IntText、FloatText|
|布尔小组件|ToggleButton、Checkbox、Valid|
|选择小组件|Dropdown、RadioButtons、Select、SelectionSlider、SelectionRangeSlider、ToggleButtons、SelectMultiple|
|字符串小组件|Text、TextArea、Combobox、Password、Label、HTML、HTML Math、Image、Button|
|播放（动画）小组件|DatePicker、ColorPicker、Controller|
|容器/布局小组件|Box、HBox、VBox、GridBox、Accordion、Tabs、Stacked|


### <a name="known-limitations"></a>已知限制

1. 尚不支持以下小组件，但你可以按照以下相应的解决方法进行操作：

   |功能|解决方法|
   |--|--|
   |`Output` 小组件|可以改用 `print()` 函数将文本写入 stdout。|
   |`widgets.jslink()`|可以使用 `widgets.link()` 函数链接两个相似的小组件。|
   |`FileUpload` 小组件| 尚不支持。|

2. Synapse 提供的全局 `display` 函数不支持在 1 个调用中显示多个小组件（即 `display(a, b)`），这与 IPython `display` 函数不同。
3. 如果关闭包含 IPython 小组件的笔记本，那么在再次执行相应的单元格之前，你将无法看到它或与之交互。


---
## <a name="save-notebooks"></a>保存笔记本

可以在工作区中保存单个笔记本或所有笔记本。

1. 要保存对单个笔记本所做的更改，请在笔记本命令栏上选择“发布”按钮。

   ![发布笔记本的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 要保存工作区中的所有笔记本，请在工作区命令栏上选择“发布所有”按钮。 

   ![“全部发布”的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在笔记本属性中，可以配置保存时是否包括单元格输出。

   ![笔记本属性的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>magic 命令
可以在 Synapse 笔记本中使用熟悉的 Jupyter magic 命令。 查看以下列表了解当前可用的 magic 命令。 告诉我们[你在 GitHub 上的用例](https://github.com/MicrosoftDocs/azure-docs/issues/new)，以便我们可以继续构建更多 magic 命令来满足你的需求。

> [!NOTE]
> Synapse 管道仅支持以下 magic 命令：%%pyspark、%%spark、%%csharp 和 %%sql。 
>
>

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

可用行 magic：[%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用单元格 magic：[%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)、[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

可用行 magic：[%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history)、[%run](#notebook-reference)、[%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

可用单元格 magic：[%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)、[%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html)、[%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>集成笔记本

### <a name="add-a-notebook-to-a-pipeline"></a>将笔记本添加到管道

选择右上角的“添加到管道”按钮，将笔记本添加到现有管道或创建新管道。

![将笔记本添加到管道的屏幕截图](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>指定参数单元格

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

若要对笔记本进行参数化，请选择省略号 (…) 以访问最右侧的其他单元格操作菜单。 然后选择“切换参数单元格”，将该单元格指定为参数单元格。

![切换参数的屏幕截图](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

若要对笔记本进行参数化，选择省略号 (…) 以访问单元格工具栏上的“更多命令”。 然后选择“切换参数单元格”，将该单元格指定为参数单元格。

![Azure Notebook 切换参数的屏幕截图](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure 数据工厂查找参数单元格，并将此单元格作为执行时传入的参数的默认单元格。 执行引擎将使用输入参数在参数单元格下面添加新的单元格，以覆盖默认值。 


### <a name="assign-parameters-values-from-a-pipeline"></a>从管道分配参数值

使用参数创建笔记本后，可以从具有 Synapse 笔记本活动的管道执行该笔记本。  将活动添加到管道画布后，将能够在“设置”选项卡的“基本参数”部分设置参数值。 

![分配参数的屏幕截图](./media/apache-spark-development-using-notebooks/assign-parameter.png)

分配参数值时，可以使用[管道表达式语言](../../data-factory/control-flow-expression-language-functions.md)或[系统变量](../../data-factory/control-flow-system-variables.md)。



## <a name="shortcut-keys"></a>快捷键

与 Jupyter Notebooks 类似，Synapse 笔记本具有模式用户界面。 键盘根据笔记本单元格的具体模式执行不同的任务。 Synapse 笔记本支持给定代码单元格的以下两种模式：命令模式和编辑模式。

1. 当没有文本光标提示你键入时，单元格处于命令模式。 当单元格处于命令模式时，可以将笔记本作为一个整体进行编辑，但不能键入单独的单元格。 按 `ESC` 或使用鼠标选择单元格编辑器区域外部，进入命令模式。

   ![命令模式的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. 编辑模式由文本光标指示，提示你在编辑器区域中键入内容。 当单元格处于编辑模式时，可以在单元格中键入。 按 `Enter` 或使用鼠标选择单元格的编辑器区域，进入编辑模式。
   
   ![编辑模式的屏幕截图](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快捷键

# <a name="classical-notebook"></a>[经典笔记本](#tab/classical)

使用以下击键快捷方式，你可以更轻松地在 Synapse 笔记本中导航并运行代码。

| 操作 |Synapse 笔记本快捷方式  |
|--|--|
|运行当前单元格并选择下方的单元格 | Shift+Enter |
|运行当前单元格并在下方插入 | Alt+Enter |
|选择上方的单元格| 向上 |
|选择下方的单元格| 向下 |
|在上方插入单元格| A |
|在下方插入单元格| B |
|展开上方的所选单元格| Shift+Up |
|展开下方的所选单元格| Shift+Down|
|上移单元格| Ctrl+Alt+↑ |
|下移单元格| Ctrl+Alt+↓ |
|删除所选单元格| D, D |
|切换到编辑模式| Enter |

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

| 操作 |Synapse 笔记本快捷方式  |
|--|--|
|运行当前单元格并选择下方的单元格 | Shift+Enter |
|运行当前单元格并在下方插入 | Alt+Enter |
|运行当前单元格| Ctrl+Enter |
|选择上方的单元格| 向上 |
|选择下方的单元格| 向下 |
|选择上一个单元格| K |
|选择下一个单元格| J |
|在上方插入单元格| A |
|在下方插入单元格| B |
|删除所选单元格| Shift+D |
|切换到编辑模式| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>编辑模式下的快捷键


使用以下击键快捷方式，可以在编辑模式下更轻松地在 Synapse 笔记本中导航并运行代码。

| 操作 |Synapse 笔记本快捷方式  |
|--|--|
|上移光标 | 向上 |
|下移光标|向下|
|撤消|Ctrl+Z|
|重做|Ctrl+Y|
|注释/撤销注释|Ctrl+/|
|删除插入提示之前的字|Ctrl+Backspace|
|删除插入提示之后的字|Ctrl+Delete|
|转到单元格开头|Ctrl+Home|
|转到单元格末尾 |Ctrl+End|
|左移一个字|Ctrl+Left|
|右移一个字|Ctrl+Right|
|全选|Ctrl+A|
|缩进| Ctrl +]|
|取消缩进|Ctrl+[|
|切换到命令模式| Esc |

---

## <a name="next-steps"></a>后续步骤
- [查看 Synapse 示例笔记本](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [快速入门：使用 Web 工具在 Azure Synapse Analytics 中创建 Apache Spark 池](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics 中的 Apache Spark 是什么](apache-spark-overview.md)
- [将 .NET for Apache Spark 与 Azure Synapse Analytics 配合使用](spark-dotnet.md)
- [.NET for Apache Spark 文档](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
