---
title: Azure Sentinel 中的搜寻功能 | Microsoft Docs
description: 使用 Azure Sentinel 的内置搜寻式查询来引导你提出正确的问题，以发现数据中的问题。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2021
ms.author: yelevin
ms.openlocfilehash: ecb8559af1d2aaf70bee0031930a748ff534f1c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622524"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>使用 Azure Sentinel 搜寻威胁

> [!IMPORTANT]
>
> 跨资源查询体验和对搜寻仪表板的升级（请查阅下面的标记项）当前处于预览状态 。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

作为安全分析师和调查员，你希望在查找安全威胁方面具有前瞻性，但各种系统和安全设备生成了海量数据，导致难以分析和筛选有意义的事件。 Azure Sentinel 提供强大的搜寻和查询工具，用于在组织的各个数据源中搜寻安全威胁。 为了帮助安全分析师主动找出未由安全应用甚至计划分析规则检测到的新异常，Azure Sentinel 的内置搜寻式查询可引导你提出正确的问题，以便在你的网络上已有的数据中发现问题。 

例如，有一个内置查询可提供有关在基础结构上运行的最不常见进程的数据。 你不希望在每次运行这些进程时都出现警报 - 它们可能完全没有恶意 - 但你可能偶尔想要查看查询以了解是否存在任何异常情况。

## <a name="use-built-in-queries"></a>使用内置查询

[搜寻仪表板](#use-the-hunting-dashboard-public-preview)提供现成的查询示例，旨在帮助你入门并熟悉表和查询语言。 查询针对存储在日志表中的数据运行，用于进程创建、DNS 事件或其他事件类型。

内置搜寻式查询是由 Microsoft 安全研究人员持续努力开发出来的，他们添加了新查询并微调了现有查询，目的是为你提供一个用于查找新检测结果的入口点，并确定从何处开始搜寻新攻击的起始点。

在发生泄漏之前、期间和之后使用查询来执行以下操作：

- 事件发生之前：等待检测是不够的。 通过每周至少运行一次与你引入工作区中的数据相关的任何威胁搜寻查询，采取主动行动。

    通过主动搜寻得出的结果，可以提前了解可确定是否正在发生泄漏的事件，或者这些结果至少可显示环境中存在风险且需要关注的较薄弱区域。

- 发生泄漏期间：使用 [livestream](livestream.md) 持续运行特定的查询，从而在结果出现时显示出来。 当你需要主动监视用户事件时（例如，如果你需要验证特定的泄漏是否仍会发生），可使用 livestream 来帮助确定威胁行动者的下一个操作，并在调查接近结束时确认泄漏确实已结束。

- 发生泄漏后：发生泄漏或事件后，确保改进覆盖范围和洞察力，以防将来发生类似事件。

    - 根据你从泄漏或事件中获得的见解，修改现有查询或创建新的查询，以帮助进行早期检测。

    - 如果发现或创建了一个可以提供有关潜在攻击的宝贵见解的搜寻式查询，基于该查询创建自定义检测规则，并将这些见解作为警报传达给安全事件响应者。

        查看查询的结果，然后选择“新建警报规则” > “创建 Azure Sentinel 警报”。  使用“分析规则向导”根据查询创建新规则。 有关详细信息，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。


> [!TIP]
> - 现在，在公共预览期间，你还可以针对 Azure 数据资源管理器中存储的数据创建搜寻式查询和 livestream 查询。 有关详细信息，请参阅 Azure Monitor 文档中[构造跨资源查询](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)的详细信息。
>
> - 使用社区资源（例如 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries)）查找其他查询和数据源。
>

## <a name="use-the-hunting-dashboard-public-preview"></a>使用搜寻仪表板（公共预览版）

只需在搜寻仪表板中进行一项选择操作，即可运行所有查询或所选子集。 在 Azure Sentinel 门户中，选择“搜寻”。

所示的表列出了由 Microsoft 安全分析师团队编写的所有查询，以及你创建或修改的任何其他查询。 每个查询都提供它搜寻的内容以及它在哪种类型的数据上运行。 这些模板按各种策略分组 - 右侧的图标对威胁类型进行分类，例如初始访问、暂留和外泄。

:::image type="content" source="media/hunting/hunting-start.png" alt-text="Azure Sentinel 开始搜寻" lightbox="media/hunting/hunting-start.png":::

使用搜寻仪表板，通过查看结果计数、峰值或 24 小时内结果计数的变化，确定开始搜寻的位置。 按收藏夹、数据源、MITRE ATT&CK 策略或方法、结果或结果差异进行排序和筛选。 查看仍需要连接数据源的查询**，并获取有关启用此类查询的建议。

下表介绍了搜寻仪表板中可用的详细操作：

|操作  |说明  |
|---------|---------|
|了解查询如何应用于你的环境     |   选择“运行所有查询(预览版)”按钮，或者使用每行左侧的复选框选择一部分查询，然后选择“运行选定的查询(预览版)”按钮 。 <br><br>运行查询可能需要几秒到几分钟时间，具体取决于选择的查询数目、时间范围以及要查询的数据量。      |
|查看返回结果的查询    |      运行完查询后，使用“结果”筛选器查看返回了结果的查询： <br>- 排序以查看哪些查询的结果最多或最少。 <br>- 通过在“结果”筛选器中选择“N/A”来查看在环境中完全处于非活动状态的查询。 <br>- 将鼠标悬停在“不适用”旁边的信息图标 (i) 上可以查看需要哪些数据源才能使此查询处于活动状态。  |
|确定数据高峰     |   根据“结果差异”进行排序或筛选以识别数据峰值。 <br><br>这会将过去 24 小时的结果与前 24-48 小时的结果进行比较，突出显示任何较大的数据量差异。     |
|查看映射到 MITRE Att&CK 策略的查询     | 表顶部的 MITRE ATT&CK 策略栏中列出了映射到每个 MITRE ATT&CK 策略的查询数。 策略栏会根据当前应用的筛选器集动态更新。 <br><br>这样你便可在按照给定结果计数、较大结果差异、“不适用”结果或任何其他筛选器集进行筛选时查看显示的 MITRE ATT&CK 策略。        |
|查看映射到 MITRE ATT&CK 方法的查询     | 查询还可映射到 MITRE ATT&CK 方法。 可以使用“方法”筛选器按照 MITRE ATT&CK 方法进行筛选或排序。 打开查询后，你将能够选择方法来查看 MITRE ATT&CK 方法的说明。        |
|将查询保存到收藏夹     |   每次访问“搜寻”页时，都会自动运行已保存到收藏夹的查询。 可以创建自己的搜寻式查询或是克隆并自定义现有搜寻式查询模板。      |
|运行查询     |   在搜寻式查询详细信息页中选择“运行查询”，可直接从搜寻页运行该查询。 匹配项数将显示在表中的“结果”列内。 查看搜寻式查询及其匹配项的列表。     |
|查看基础查询     | 在查询详细信息窗格中快速查看基础查询。 可以单击“查看查询结果”链接（在查询窗口下方）或“查看结果”按钮（在窗格底部）来查看结果 。 查询将在“日志”(Log Analytics) 边栏选项卡中打开。在查询下方，可以查看查询的匹配项。         |
|     |         |


## <a name="create-your-own-bookmarks"></a>创建自己的书签

在搜寻和调查过程中，你可能会遇到看上去异常或可疑的查询结果。 为这些项加入书签，以供将来引用它们（如在创建或扩充事件以进行调查的情况下）。

- 在结果中，标记要保留的任何行的复选框，然后选择“添加书签”。 这会针对标记的每个行创建一条记录（一个书签），其中包含行结果、创建了结果的查询，以及用于提取用户、主机和 IP 地址的实体映射。 你可以在每个书签中添加自己的标记和备注。

- 通过在“搜寻”主页中单击“书签”选项卡来查看所有添加了书签的结果 。 将标记添加到书签，以将这些书签分类，从而方便筛选。 例如，如果你正在调查某种攻击活动，可为该活动创建一个标记，将该标记应用到所有相关书签，然后根据该活动筛选所有书签。

- 调查单个添加了书签的结果，方法是选择该书签，然后在详细信息窗格中单击“调查”以打开调查体验。

    还可以从一个或多个书签创建事件，或向现有事件添加一个或多个书签。 选择要使用的任何书签左侧的复选框，然后选择“事件操作” > “创建新事件”或“添加到现有事件”。   像对待任何其他事件一样会审和调查该事件。


> [!TIP]
> 书签代表值得注意的关键事件，如果事件严重到需要进行调查，则书签应升级为事件。 事件（例如潜在的根本原因、泄漏的征兆或其他值得注意的事件）应该作为书签引发。
>

有关书签的详细信息，请参阅[在搜寻中使用书签](bookmarks.md)。

## <a name="use-notebooks-to-power-investigations"></a>使用笔记本为调查提供支持

当搜寻和调查变得更加复杂时，使用 Azure Sentinel 笔记本的机器学习、可视化和数据分析功能来增强活动。

笔记本提供一种虚拟沙盒，它配备有自己的内核，可在其中执行完整的调查。 笔记本可以包含原始数据、对该数据运行的代码、结果及其可视化效果。 保存笔记本，从而可与他人共享并在组织中重用它们。

当搜寻或调查变得太大而难以记住或无法查看详细信息，或者需要保存查询和结果时，笔记本可能会很有帮助。 为了帮助你创建和共享笔记本，Azure Sentinel 提供了 [Jupyter Notebook](https://jupyter.org)，它是一个开源的、交互式开发和数据操作环境，直接集成在 Azure Sentinel“笔记本”页中。

有关详细信息，请参阅：

- [使用 Jupyter Notebook 搜寻安全威胁](notebooks.md)
- [Jupyter 项目文档](https://jupyter.org/documentation)
- [Jupyter 简介文档](https://jupyter.readthedocs.io/en/latest/tryjupyter.html)
- [Infosec Jupyter 书籍](https://infosecjupyterbook.com)
- [Real Python 教程](https://realpython.com)

下表介绍了一些使用 Juypter Notebook 的方法，以便为 Azure Sentinel 中的进程提供帮助：

|方法  |说明  |
|---------|---------|
|数据暂留、可重复性和回溯     |  如果正在处理许多查询和结果集，则很可能遭遇无法连接的情况。 需要确定要保留哪些查询和结果，以及如何在单个报告中累积有用的结果。 <br><br> 使用 Jupyter Notebook 在运行时保存查询和数据，使用变量重新运行具有不同值或日期的查询，或保存查询以在将来的调查中重新运行。       |
|**脚本和编程**     |    使用 Jupyter Notebook 向查询添加编程，包括： <br><br>- [Kusto 查询语言 (KQL)](/azure/kusto/query/) 或 SQL 等“声明性”语言，以在可能比较复杂的单个语句中对逻辑进行编码。<br>- “过程”编程语言，以在一系列步骤中运行逻辑。 <br><br>将逻辑拆分为步骤有助于查看和调试中间结果，添加查询语言中可能不可用的功能以及在之后的处理步骤中重用部分结果。     |
|指向外部数据的链接     | 尽管 Azure Sentinel 表包含大多数遥测数据和事件数据，但 Jupyter Notebook 可以链接到通过网络或文件访问的任何数据。 使用 Jupyter Notebook 可以包括以下数据： <br><br>- 不拥有的外部服务数据，例如地理位置数据或威胁情报源<br>- 仅存储在组织内部的敏感数据，例如人力资源数据库或高价值资产列表<br>- 尚未迁移到云的数据。        |
|专用数据处理、机器学习和可视化工具     | Jupyter Notebook 提供其他可视化效果、机器学习库以及数据处理和转换功能。 <br><br>例如，将 Jupyter Notebook 与以下 [Python](https://python.org) 功能结合使用：<br>- [pandas](https://pandas.pydata.org/)，用于数据处理、清理和工程<br>- [Matplotlib](https://matplotlib.org)、[HoloViews](https://holoviews.org) 和 [Plotly](https://plot.ly)，用于可视化效果<br>- [NumPy](https://www.numpy.org) 和 [SciPy](https://www.scipy.org)，用于高级数值和科学处理<br>- [scikit-learn](https://scikit-learn.org/stable/index.html)，用于机器学习<br>- [TensorFlow](https://www.tensorflow.org/)、[PyTorch](https://pytorch.org) 和 [Keras](https://keras.io/)，用于深度学习<br><br>提示：Jupyter Notebook 支持多种语言内核。 通过 *magics*，可以使用其他语言执行单个单元格，从而在同一个笔记本中混合使用不同语言。 例如，可以使用 PowerShell 脚本单元格检索数据，采用 Python 处理数据，并使用 JavaScript 呈现可视化效果。        |
|     |         |

### <a name="mstic-jupyter-and-python-security-tools"></a>MSTIC、Jupyter 和 Python 安全工具

[Microsoft 威胁情报中心 (MSTIC)](https://msrc-blog.microsoft.com/tag/mstic/) 是由 Microsoft 安全分析师和工程师组成的团队，他们为多个 Microsoft 平台创作安全检测，并负责威胁识别和调查。

MSTIC 构建了 [MSTICPy](https://github.com/Microsoft/msticpy)，它是用于在 Jupyter Notebook 中进行信息安全调查和搜寻的库。 MSTICPy 提供可重用的功能，旨在加快笔记本创建速度，让用户更轻松地在 Azure Sentinel 中读取笔记本。

例如，MSTICPy 可以：

- 查询来自多个源的日志数据。
- 使用威胁情报、地理位置和 Azure 资源数据扩充数据。
- 从日志中提取活动指标 (IoA)，并解压缩编码数据。
- 执行复杂的分析，例如异常会话检测和时序分解。
- 使用交互式时间线、进程树和多维变形图表可视化数据。

MSTICPy 还包括一些省时的笔记本工具，例如用于设置查询时间边界、从列表选择和显示项以及配置笔记本环境的小组件。

有关详细信息，请参阅：

- [MSTICPy 文档](https://msticpy.readthedocs.io/en/latest/)
- [教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy](notebook-get-started.md)
- [适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置](notebooks-msticpy-advanced.md)


## <a name="useful-operators-and-functions"></a>有用的运算符和函数

搜寻式查询是以 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 生成的。KQL 是一种使用 IntelliSense 语言的强大查询语言，提供所需的强大功能和灵活性，可让你进一步提升搜寻的覆盖范围。

它是分析规则中的查询使用的语言，也是 Azure Sentinel 中别处的查询使用的语言。 有关详细信息，请参阅[查询语言引用](../azure-monitor/logs/get-started-queries.md)。

以下运算符对于 Azure Sentinel 搜寻查询特别有用：

- where - 筛选表，获取满足谓词的行子集。

- summarize - 生成可聚合输入表内容的表。

- join - 通过匹配每个表中指定列的值，合并两个表的行以组成新表。

- count - 返回输入记录集中的记录数。

- top - 返回按指定列排序的前 N 个记录。

- limit - 返回指定的行数。

- project - 选择要包含、重命名或删除的列，并插入新的计算列。

- extend - 创建计算列并将其追加到结果集中。

- makeset - 返回表达式在组中所获取非重复值集的动态 (JSON) 数组

- find - 在一组表中查找与谓词相匹配的行。

- adx()（预览）- 此函数从 Azure Sentinel 搜寻体验和 Log Analytics 对 Azure 数据资源管理器数据源执行跨资源查询。 有关详细信息，请参阅[使用 Azure Monitor 跨资源查询 Azure 数据资源管理器](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)。

## <a name="save-a-query"></a>保存查询

创建或修改查询并将它保存为自己的查询，或是将它与同一租户中的用户共享。

:::image type="content" source="./media/hunting/save-query.png" alt-text="保存查询" lightbox="./media/hunting/save-query.png":::

创建新查询：

1. 选择“新建查询”。

1. 填写所有空白字段并选择“创建”。

    :::image type="content" source="./media/hunting/new-query.png" alt-text="新查询" lightbox="./media/hunting/new-query.png":::

克隆和修改现有的查询：

1. 在表中选择要修改的搜寻式查询。

1. 在要修改的查询行中选择省略号 (...)，然后选择“克隆查询”。

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="克隆查询" lightbox="./media/hunting/clone-query.png":::

1. 修改查询，然后选择“创建”。

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="自定义查询" lightbox="./media/hunting/custom-query.png":::



## <a name="sample-query"></a>示例查询

典型查询以表名开头，后跟一系列由  分隔的\|运算符。

在上面的示例中，从表名 SecurityEvent 开始，并根据需要添加以管道字符分隔的元素。

1. 定义一个时间筛选器，以便仅查看前七天的记录。

1. 在查询中添加筛选器，以便仅显示事件 ID 4688。

1. 在命令行上向查询中添加筛选器，以便仅包含 cscript.exe 的实例。

1. 仅投影有兴趣探索的列，并将结果限制为 1000 个，然后选择“运行查询”。

1. 选择绿色三角形并运行查询。 可以测试查询，并运行它以查找异常行为。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure Sentinel 运行搜寻调查。 

有关详细信息，请参阅：

- [使用笔记本运行自动搜寻活动](notebooks.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)

学习此示例：通过[自定义连接器](create-custom-connector.md)[监视 Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) 时使用自定义分析规则。

