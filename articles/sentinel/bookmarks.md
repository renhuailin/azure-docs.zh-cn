---
title: 在 Azure Sentinel 中使用搜寻书签进行数据调查
description: 本文介绍了如何使用 Azure Sentinel 搜寻书签来跟踪数据。
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 0fecd1fca22b003892817f8fd33db88b1eea6127
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729765"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>在通过 Azure Sentinel 进行搜寻期间跟踪数据

威胁搜寻通常要检查大量的日志数据，查找恶意行为的证据。 在此过程中，调查者可能会发现某些事件，为了验证存在可能性的假想和了解整个攻击情况，需要记住、重新访问和分析这些事件。

Azure Sentinel 中的搜寻书签可以保留你在“Azure Sentinel - 日志”中运行的查询，以及你认为相关的查询结果，从而帮助搜寻威胁。 还可以通过添加注释和标记来记录在上下文中的观察结果和引用发现结果。 你和你的团队成员可以看到带有书签的数据，这样可便于轻松协作。

可以随时在“搜寻”窗格的“书签”选项卡上重新查看带有书签的数据 。 可以使用筛选和搜索选项快速查找要用于当前调查的特定数据。 也可以直接在 Log Analytics 工作区中的 **HuntingBookmark** 表中查看带有书签的数据。 例如：

> [!div class="mx-imgBorder"]
> ![查看 HuntingBookmark 表](./media/bookmarks/bookmark-table.png)

通过从表中查看书签，可以筛选、汇总和联接带有书签的数据及其他数据源，从而可以轻松查找证据。

目前在预览版中，如果在日志中搜寻时发现了某些急需解决的问题，只需单击几次鼠标，即可创建一个书签并将其提升为事件，或将书签添加到现有事件。 有关事件的详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](investigate-cases.md)。 

此外在预览版中，还可以通过单击书签详细信息中的“调查”，将带有书签的数据可视化。 这将启动调查体验，你可以使用交互式实体图形关系图和时间线来查看、调查和直观地传达你的发现。

## <a name="add-a-bookmark"></a>添加书签

1. 在 Azure 门户中，导航到“Sentinel > “威胁管理” > “搜寻”，以运行对可疑和异常行为的查询。

2. 选择其中一个搜寻查询，在搜寻查询详细信息的右侧，选择“运行查询”。 

3. 选择“查看查询结果”。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻中查看查询结果](./media/bookmarks/new-processes-observed-example.png)
    
    此操作将打开“日志”窗格中的查询结果。

4. 从日志查询结果列表中，使用复选框来选择包含了感兴趣信息的一个或多个行。

5. 选择“添加书签”：
    
    > [!div class="mx-imgBorder"]
    > ![向查询中添加搜寻书签](./media/bookmarks/add-hunting-bookmark.png)

6. 在右侧的“添加书签”窗格中，可以选择更新书签名称、添加标记和注释，以帮助确定对该项感兴趣的内容。

7. 在“查询信息”部分中，使用下拉框从查询结果中提取有关“帐户”、“主机”和“IP 地址”等实体类型的信息。 此操作会将所选的实体类型映射到查询结果中的特定列。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![为搜寻书签映射实体类型](./media/bookmarks/map-entity-types-bookmark.png)
    
    若要在调查图（当前为预览版）中查看书签，必须映射至少一种实体类型，可以是“帐户”、“主机”或“IP 地址”  。 

5. 单击“保存”以提交更改并添加书签。 所有带有书签的数据都与其他调查者共享，这是协作调查体验的第一步。

 
> [!NOTE]
> 每次从 Azure Sentinel 打开此窗格时，日志查询结果都支持书签。 例如，从导航栏中选择“常规” > “日志”，然后在调查关系图中选择事件链接，或从事件的完整详细信息（当前为预览版）中选择一个警报 ID。 当“日志”窗格已从其他位置打开（例如，直接从 Azure Monitor 打开）时，不能创建书签。

## <a name="view-and-update-bookmarks"></a>查看和更新书签 

1. 在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻”。 

2. 选择“书签”选项卡以查看书签列表。

3. 若要帮助查找特定书签，请使用搜索框或筛选器选项。

4. 选择各个书签，并在右侧的详细信息窗格中查看书签详细信息。

5. 根据需要进行更改，所有更改将自动保存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>在调查图中浏览书签

> [!IMPORTANT]
> 在调查图中浏览书签和调查图本身目前都是公共预览版。
> 这些功能在提供时没有附带服务级别协议，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻” > “书签”选项卡，然后选择要调查的一个或多个书签。

2. 在书签详细信息中，确保至少映射一个实体。 例如，“ENTITIES”将具有“IP”、“计算机”或“帐户”等条目。

3. 单击“调查”可在调查图中查看书签。

有关使用调查图的说明，请参阅[使用调查图深入探讨](investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>将书签添加到新事件或现有事件

> [!IMPORTANT]
> 将书签添加到新事件或现有事件的功能当前是在公共预览版中提供。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻” > “书签”选项卡，然后选择要添加到事件的一个或多个书签。

2. 从命令栏中选择“事件操作(预览版)”：
    
    > [!div class="mx-imgBorder"]
    > ![向事件添加书签](./media/bookmarks/incident-actions.png)

3. 根据需要，选择“创建新事件”或“添加到现有事件”。 然后：
    
    - 若要添加到新事件：可以选择更新事件的详细信息，然后选择“创建”。
    - 若要将书签添加到现有事件：请选择一个事件，然后选择“添加”。 

若要查看事件中的书签：导航到“Sentinel” > “威胁管理” > “事件”，然后选择带有书签的事件。 选择“查看完整详细信息”，然后选择“书签”选项卡。

> [!TIP]
> 除了在命令栏上选择“事件操作(预览版)”选项之外，还可以使用一个或多个书签的上下文菜单 ( **...** ) 来选择“创建新事件”、“添加到现有事件”和“从事件中删除”选项。 

## <a name="view-bookmarked-data-in-logs"></a>在日志中查看带有书签的数据

若要查看带有书签的查询、结果或其历史记录，请从“搜寻” > “书签”选项卡中选择该书签，并使用详细信息窗格中提供的链接： 

- 选择“查看源查询”，在“日志”窗格中查看源查询。

- 选择“查看书签日志”，查看所有书签元数据，其中包括执行更新的人员、更新的值以及更新发生的时间。

还可以从命令栏的“搜寻” > “书签”选项卡上选择“书签日志”，以查看所有书签的原始书签数据：

> [!div class="mx-imgBorder"]
> ![书签日志](./media/bookmarks/bookmark-logs.png)

此视图显示了具有关联元数据的所有书签。 可以使用[关键字查询语言](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) 查询，筛选到要查找的特定书签的最新版本。

> [!NOTE]
> 从创建书签到“书签”选项卡中显示书签的这段时间内，存在一个明显的延迟（以分钟计）。

## <a name="delete-a-bookmark"></a>删除书签
 
1.  在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻” > “书签”选项卡，然后选择要删除的一个或多个书签。 

2. 右键单击所选内容，然后选择相应选项以删除一个或多个书签。 例如，仅选择一个书签时，单击“删除书签”；选择两个书签时，则单击“删除 2 个书签”。
    
删除书签会将书签从“书签”选项卡的列表中删除。Log Analytics 工作区所对应的“HuntingBookmark”表将继续包含之前的书签条目，但最新条目会将“SoftDelete”值更改为 true，这样即可轻松筛选出旧书签。 删除书签后，调查体验中与其他书签或警报相关联的任何实体并不会删除。 


## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Sentinel 中使用书签来运行搜寻调查。 要详细了解 Azure Sentinel，请参阅以下文章：


- [主动搜寻威胁](hunting.md)
- [使用笔记本运行自动搜寻活动](notebooks.md)