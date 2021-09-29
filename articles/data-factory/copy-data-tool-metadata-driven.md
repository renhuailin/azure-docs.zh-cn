---
title: 使用复制数据工具中元数据驱动的方法生成大规模数据复制管道
description: 提供有关 ADF 复制数据工具中元数据驱动的方法的信息
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 06/19/2021
ms.author: yexu
ms.openlocfilehash: 02d7b741ec0c3fb9547d10bde759900ce3a69dd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663338"
---
# <a name="build-large-scale-data-copy-pipelines-with-metadata-driven-approach-in-copy-data-tool-preview"></a>使用复制数据工具中元数据驱动的方法生成大规模数据复制管道（预览版）
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果你要复制巨量的对象（例如数千个表）或者要从大量不同的源中加载数据，适当的方法是在一个控制表中输入包含所需复制行为的对象的名称列表，然后使用参数化管道从该控制表中读取这些对象，并相应地将其应用于作业。  这样，只需更新控制表中的对象名称就能轻松维护（例如添加/删除）要复制的对象列表，而无需重新部署管道。 此外，你将有一个位置来轻松检查定义了复制行为的哪些管道/触发器复制了哪些对象。 

ADF 中的复制数据工具简化了这种元数据驱动的数据复制管道的生成过程。 通过基于向导的体验完成直观的流程后，该工具可为你生成参数化管道和 SQL 脚本，以便你相应地创建外部控制表。 运行生成的脚本在 SQL 数据库中创建控制表后，管道将读取控制表中的元数据，并自动将其应用于复制作业。

## <a name="create-metadata-driven-copy-jobs-from-copy-data-tool"></a>通过复制数据工具创建元数据驱动的复制作业

1. 在复制数据工具中选择“元数据驱动的复制任务”。

   需要输入连接以及控制表的表名称，使生成的管道从该控制表中读取元数据。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-task-type.png" alt-text="选择任务类型":::

2. 输入源数据库的连接。 也可以使用[参数化链接服务](parameterize-linked-services.md)。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-parameterized-linked-service.png" alt-text="选择参数化链接服务":::

3. 选择要复制的表名称。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-table.png" alt-text="选择表":::

   > [!NOTE]
   > 如果选择了表格数据存储，则可以在下一页中进一步选择完全加载或增量加载。 如果选择了存储，则在下一页中只能进一步选择完全加载。 目前不支持仅从存储中以增量方式加载新文件。  

4. 选择加载行为。
   >[!TIP]
   >如果你要对所有表执行完全复制，请选择“完全加载所有表”。 如果你要执行增量复制，可以选择“单独对每个表进行配置”，然后选择“增量加载”，以及用于为每个表开始复制的水印列名和值 。 

5. 选择“目标数据存储”。 

6. 在“设置”页中，可以通过“并发复制任务数”来确定用于从源存储中并发复制数据的复制活动数上限 。 默认值为 20。 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/settings.png" alt-text="“设置”页":::

7. 管道部署后，可以从 UI 复制或下载 SQL 脚本，用于创建控制表和存储过程。 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/download-scripts.png" alt-text="下载脚本":::

   你将看到两个 SQL 脚本。
   
    - 第一个 SQL 脚本用于创建两个控制表。 主控制表存储表列表、文件路径或复制行为。 连接控制表存储数据存储的连接值（如果使用的是参数化链接服务）。  
    - 第二个 SQL 脚本用于创建存储过程。 每次增量复制作业完成时，将使用该脚本来更新主控制表中的水印值。 

8. 打开 **SSMS** 以连接到控制表服务器，然后运行这两个 SQL 脚本来创建控制表和存储过程。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/create-control-table-script.png" alt-text="创建控制表脚本":::

9. 查询主控制表和连接控制表，以查看其中的元数据。

   主控制表
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-control-table.png" alt-text="查询控制表脚本 1":::

   连接控制表
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-connection-control-table.png" alt-text="查询控制表脚本 2":::

10. 返回 ADF 门户以查看和调试管道。 你将看到，已创建一个名为“MetadataDrivenCopyTask_### _######”的文件夹。单击名为“MetadataDrivenCopyTask_###_TopLevel”的管道，然后单击“调试运行” 。 

    需要输入以下参数：
   
    | 参数名称 | 说明 | 
    |:--- |:--- |
    |MaxNumberOfConcurrentTasks |在管道运行之前，始终可以更改最大并发复制活动运行数。 默认值是你在复制数据工具中输入的值。 |
    |MainControlTableName | 在运行之前，始终可以更改主控制表名称，使管道从该表中获取元数据。  |
    |ConnectionControlTableName |在运行之前，始终可以更改连接控制表名称（可选），使管道获取与数据存储连接相关的元数据。 |
    |MaxNumberOfObjectsReturnedFromLookupActivity |为了避免达到输出查找活动的限制，可使用该参数来定义查找活动返回的最大对象数。 在大多数情况下，无需更改默认值。  |
    |windowStart |输入动态值（例如 yyyyy/mm/dd）作为文件夹路径时，会使用该参数将当前触发器时间传递给管道，以填充动态文件夹路径。 当管道由计划触发器或翻转窗口触发器触发时，用户无需输入此参数的值。 示例值：2021-01-25T01:49:28Z | 
   

11. 启用触发器以使管道可运行。

    :::image type="content" source="./media/copy-data-tool-metadata-driven/enable-trigger.png" alt-text="启用触发器":::


## <a name="update-control-table-by-copy-data-tool"></a>通过复制数据工具更新控制表
始终可以通过添加或删除要复制的对象或者更改每个表的复制行为，来直接更新控制表。 我们还在复制数据工具中创建了 UI 体验，以简化控制表编辑过程。

1. 右键单击顶级管道“MetadataDrivenCopyTask_xxx_TopLevel”并选择“编辑控制表” 。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table.png" alt-text="编辑控制表 1":::

2. 在控制表中选择要编辑的行。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-select-tables.png" alt-text="编辑控制表 2":::

3. 完成复制数据工具中的每个步骤，最终它会为你创建一个新的 SQL 脚本。 重新运行 SQL 脚本以更新控制表。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-create-script.png" alt-text="编辑控制表 3":::

   > [!NOTE]
   > 不会重新部署管道。 新建的 SQL 脚本只能帮助你更新控制表。 

## <a name="control-tables"></a>控制表

### <a name="main-control-table"></a>主控制表
控制表中的每一行包含一个要复制的对象（例如一个表）的元数据。

| 列名称 | 说明 | 
|:--- |:--- |
| ID | 要复制的对象的唯一 ID。 |
| SourceObjectSettings | 源数据集的元数据。 可以是架构名称、表名称等。[此处](connector-azure-sql-database.md#dataset-properties)提供了示例。 |
| SourceConnectionSettingsName | 连接控制表中源连接设置的名称。 它是可选的。 |
| CopySourceSettings | 复制活动中源属性的元数据。 可以是查询、分区等。[此处](connector-azure-sql-database.md#azure-sql-database-as-the-source)提供了示例。 |
| SinkObjectSettings | 目标数据集的元数据。 可以是文件名、文件夹路径、表名称等。[此处](connector-azure-data-lake-storage.md#azure-data-lake-storage-gen2-as-a-sink-type)提供了示例。 如果指定了动态文件夹路径，则变量值不会写入到控制表中的此列。 |
| SinkConnectionSettingsName | 连接控制表中目标连接设置的名称。 它是可选的。 |
| CopySinkSettings | 复制活动中接收器属性的元数据。 可以是 preCopyScript、tableOption 等。[此处](connector-azure-sql-database.md#azure-sql-database-as-the-sink)提供了示例。 |
| CopyActivitySettings | 复制活动中转换器属性的元数据。 用于定义列映射。 |
| TopLevelPipelineName | 顶级管道名称，它可以复制此对象。 |
| TriggerName | 触发器名称，它可以触发管道来复制此对象。 如果调试运行，则名称为“沙盒”。 如果手动执行，则名称为“手动”。 如果计划内运行，则名称为关联的触发器名称。 它可以输入多个名称。 |
| DataLoadingBehaviorSettings |完全加载与增量加载。 |
| TaskId | 要按控制表中的 TaskId 复制的对象的顺序 (ORDER BY [TaskId] DESC)。 如果你要复制巨量的对象，但允许的并发复制数有限，则你可以更改每个对象的 TaskId，以确定哪些对象可以先复制。 默认值为 0。 |
| CopyEnabled | 指定是否在数据引入过程中启用了该项。 允许的值：1（已启用）、0（已禁用）。 默认值为 1。 |

### <a name="connection-control-table"></a>连接控制表
控制表中的每一行包含该数据存储的一个连接设置。

| 列名称 | 说明 | 
|:--- |:--- |
| 名称 | 主控制表中参数化连接的名称。 |
| ConnectionSettings | 连接设置。 可以是数据库名称、服务器名称等。 |

## <a name="pipelines"></a>管道
你将看到复制数据工具生成了三个级别的管道。

### <a name="metadatadrivencopytask_xxx_toplevel"></a>MetadataDrivenCopyTask_xxx_TopLevel
此管道将计算需要在此运行中复制的对象（表等）总数，根据允许的最大并发复制任务数提供顺序批数，然后执行另一个管道来按顺序复制不同的批。 

#### <a name="parameters"></a>参数
| 参数名称 | 说明 | 
|:--- |:--- |
| MaxNumberOfConcurrentTasks | 在管道运行之前，始终可以更改最大并发复制活动运行数。 默认值是你在复制数据工具中输入的值。 |
| MainControlTableName | 主控制表的表名称。 在运行之前，管道将从此表中获取元数据 |
| ConnectionControlTableName | 连接控制表的表名称（可选）。 在运行之前，管道将获取与数据存储连接相关的元数据 |
| MaxNumberOfObjectsReturnedFromLookupActivity | 为了避免达到输出查找活动的限制，可使用该参数来定义查找活动返回的最大对象数。 在大多数情况下，无需更改默认值。  |
| windowStart | 输入动态值（例如 yyyyy/mm/dd）作为文件夹路径时，会使用该参数将当前触发器时间传递给管道，以填充动态文件夹路径。 当管道由计划触发器或翻转窗口触发器触发时，用户无需输入此参数的值。 示例值：2021-01-25T01:49:28Z |

#### <a name="activities"></a>活动
| 活动名称 | 活动类型 | 说明 |
|:--- |:--- |:--- |
| GetSumOfObjectsToCopy  | 查找 | 计算需要在此运行中复制的对象（表等）总数。 |
| CopyBatchesOfObjectsSequentially | ForEach | 根据允许的最大并发复制任务数提供顺序批数，然后执行另一个管道来按顺序复制不同的批。  |
| CopyObjectsInOneBtach | 执行管道 | 执行另一个管道来复制一批对象。 属于此批的对象将会并行复制。 | 


### <a name="metadatadrivencopytask_xxx_-middlelevel"></a>MetadataDrivenCopyTask_xxx_MiddleLevel
此管道将复制一批对象。 属于此批的对象将会并行复制。 

#### <a name="parameters"></a>参数
| 参数名称 | 说明 | 
|:--- |:--- |
| MaxNumberOfObjectsReturnedFromLookupActivity | 为了避免达到输出查找活动的限制，可使用该参数来定义查找活动返回的最大对象数。  在大多数情况下，无需更改默认值。  | 
| TopLayerPipelineName | 顶层管道的名称。 | 
| TriggerName | 触发器的名称。 | 
| CurrentSequentialNumberOfBatch | 顺序批的 ID。 | 
| SumOfObjectsToCopy | 要复制的对象总数。 | 
| SumOfObjectsToCopyForCurrentBatch | 当前批中要复制的对象数。 | 
| MainControlTableName | 主控制表的名称。 |
| ConnectionControlTableName | 连接控制表的名称。 |

#### <a name="activities"></a>活动
| 活动名称 | 活动类型 | 说明 |
|:--- |:--- |:--- |
| DivideOneBatchIntoMultipleGroups | ForEach  | 将单个批中的对象划分为多个并行组，以避免达到查找活动的输出限制。 |
| GetObjectsPerGroupToCopy | 查找 | 从控制表中获取需要在此组中复制的对象（表等）。 要按控制表中的 TaskId 复制的对象的顺序 (ORDER BY [TaskId] DESC)。 |
| CopyObjectsInOneGroup | 执行管道 | 执行另一个管道以从一个组复制对象。 属于此组的对象将会并行复制。 |


### <a name="metadatadrivencopytask_xxx_-bottomlevel"></a>MetadataDrivenCopyTask_xxx_BottomLevel
此管道将从一个组复制对象。 属于此组的对象将会并行复制。  

#### <a name="parameters"></a>参数
| 参数名称 | 说明 | 
|:--- |:--- |
| ObjectsPerGroupToCopy | 当前组中要复制的对象数。 | 
| ConnectionControlTableName | 连接控制表的名称。 | 
| windowStart | 用于将当前触发器时间传递给管道，以填充动态文件夹路径（如果已由用户配置）。 | 

#### <a name="activities"></a>活动
| 活动名称 | 活动类型 | 说明 |
|:--- |:--- |:--- |
| ListObjectsFromOneGroup | ForEach | 列出一个组中的对象，并将其中的每个对象迭代到下游活动。 |
| RouteJobsBasedOnLoadingBehavior | 开关 | 检查每个对象的加载行为。 如果加载行为是默认行为或 FullLoad，则执行完全加载。 如果加载行为是 DeltaLoad，则通过水印列执行增量加载以识别更改 |
| FullLoadOneObject | 复制 | 获取此对象的完整快照并将其复制到目标。 |
| DeltaLoadOneObject | 复制 | 通过比较水印列中的值来识别更改，以便仅复制自上次复制以来已更改的数据。 |
| GetMaxWatermarkValue | 查找 | 查询源对象以获取水印列中的最大值。 |
| UpdateWatermarkColumnValue | StoreProcedure | 将新的水印值写回到控制表供下次使用。 |

### <a name="known-limitations"></a>已知的限制
- 复制数据工具目前不支持使用元数据驱动的引入来以增量方式仅复制新添加的文件。 但是，你可以使用自己的参数化管道来实现此目的。
- 无法在 ADF 中参数化 IR 名称、数据库类型和文件格式类型。 例如，如果你要从 Oracle Server 和 SQL Server 引入数据，则需要两个不同的参数化管道。 但是，一个控制表可由两组管道共享。 
- OPENJSON 用于通过复制数据工具生成 SQL 脚本。 如果使用 SQL Server 托管控制表，则必须是 SQL Server 2016 (13.x) 及更高版本以便支持 OPENJSON 函数。


## <a name="next-steps"></a>后续步骤
请尝试以下使用“复制数据”工具的教程：

- [快速入门：使用“复制数据”工具创建数据工厂](quickstart-create-data-factory-copy-data-tool.md)
- [教程：使用“复制数据”工具在 Azure 中复制数据](tutorial-copy-data-tool.md) 
- [教程：使用“复制数据”工具将本地数据复制到 Azure](tutorial-hybrid-copy-data-tool.md)
