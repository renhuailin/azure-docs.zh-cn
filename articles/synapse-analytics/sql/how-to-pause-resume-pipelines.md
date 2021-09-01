---
title: 如何使用 Synapse Pipelines 暂停和恢复专用 SQL 池
description: 了解如何使用 Azure Synapse Analytics 中的 Synapse Pipelines 自动暂停和恢复专用 SQL 池。
author: julieMSFT
ms.author: jrasnick
ms.service: synapse-analytics
ms.reviewer: wiassaf
ms.subservice: sql
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01fd517be7e60a5ab16e7844d8c149ddac2dcb3e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862443"
---
# <a name="pause-and-resume-dedicated-sql-pools-with-synapse-pipelines"></a>使用 Synapse Pipelines 暂停和恢复专用 SQL 池

可以使用 Azure Synapse Analytics 中的 Synapse Pipelines 自动暂停和恢复专用 SQL 池。 暂停和恢复操作可用于节省专用 SQL 池的成本。 可以轻松将此解决方案包含在现有的数据业务流程中。 

以下步骤引导你设置自动暂停和恢复。

1. 创建管道。
1. 在管道中设置参数。
1. 确定 Azure Synapse 工作区中的专用 SQL 池列表。
1. 从列表中筛选出你不想要暂停或恢复的任何专用 SQL 池。 
1. 循环访问每个专用 SQL 池，然后执行以下操作：
    1. 检查专用 SQL 池的状态。
    1. 评估专用 SQL 池的状态。
    1. 暂停或恢复专用 SQL 池。

这些步骤将在 Azure Synapse 中的某个简单管道内执行：

![简单的 Synapse 管道](./media/how-to-pause-resume-pipelines/simple-pipeline.png)


根据环境的性质，此处所述的整个过程不一定适用，你只需选择适当的步骤即可。 此处所述的过程可用于暂停或恢复开发、测试或 PoC 环境中的所有实例。 对于生产环境，你很有可能会根据各个实例计划暂停或恢复，因此只需执行步骤 5a 至 5c。

上述步骤使用适用于 Azure Synapse 和 Azure SQL 的 REST API：

- [专用 SQL 池操作](/rest/api/synapse/sqlpools)
 
- [Azure SQL 数据库 REST API](/rest/api/sql)

Synapse Pipelines 允许自动暂停和恢复，但你可以通过所选的工具或应用程序按需执行这些命令。

## <a name="prerequisites"></a>先决条件

- 一个现有的 [Azure Synapse 工作区](../get-started-create-workspace.md)
- 至少一个[专用 SQL 池](../get-started-analyze-sql-pool.md)
- 必须为此工作区分配 Azure 参与者角色。 请参阅[向 Synapse 管理员授予工作区中的“Azure 参与者”角色](../security/how-to-set-up-access-control.md#step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace)。

## <a name="step-1-create-a-pipeline-in-synapse-studio"></a>步骤 1：在 Synapse Studio 中创建管道。
1. 导航到你的工作区并打开 Synapse Studio。 
1. 选择“集成”图标，然后选择“+”符号以创建新管道 。 
1. 将管道命名为 PauseResume。

    ![在 Synapse Studio 中创建管道](./media/how-to-pause-resume-pipelines/create-pipeline.png) 

## <a name="step-2-create-pipeline-parameters"></a>步骤 2：创建管道参数 

要创建的管道将由参数驱动。 通过参数可以创建一个可跨多个订阅、资源组或专用 SQL 池使用的通用管道。 选择靠近管道屏幕底部的“参数”选项卡。 选择“+新建”以创建以下每个参数：

    
|名称  |类型  |默认值  |说明|
|---------|---------|---------|-----------|
|ResourceGroup    |字符串        |Synapse          |专用 SQL 池的资源组名称|
|订阅 ID   |字符串        |`<SubscriptionID>` |资源组的订阅 ID|
|WorkspaceName    |字符串        |Synapse          |工作区的名称|
|SQLPoolName      |字符串        |SQLPool1         |专用 SQL 池的名称|
|PauseorResume    |字符串        |暂停            |管道运行结束时所需的状态|

![Synapse Studio 中的管道参数。](./media/how-to-pause-resume-pipelines/pipeline-parameters.png)

## <a name="step-3-create-list-of-dedicated-sql-pools"></a>步骤 3：创建专用 SQL 池列表
 
 设置一个 Web 活动，你将使用此活动通过调用专用 SQL 池的“按服务器列出”REST API 请求来创建专用 SQL 池列表。 输出是一个 JSON 字符串，其中包含工作区中专用 SQL 池的列表。 该 JSON 字符串将传递到下一个活动。
1. 在“活动” > “常规”下，将某个 Web 活动作为管道的第一阶段拖放到管道画布中  。  
1. 在“常规”选项卡中，将此阶段命名为“GET 列表”。 
1. 选择“设置”选项卡，在“URL”输入空间中单击，然后选择“添加动态内容”  。 将以下使用 @concat 字符串函数参数化的 GET 请求复制并粘贴到动态内容框中。 选择“完成”  。
以下代码是一个简单的 Get 请求：

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools?api-version=2019-06-01-preview
    ```
    
    已使用 @concat 字符串函数参数化的 GET 请求：
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools?api-version=2019-06-01-preview')
    ``` 
1. 选择“方法”对应的下拉列表，然后选择“Get” 。  
1. 选择“高级”展开内容。 选择“MSI”作为身份验证类型。 对于“资源”，请输入 `https://management.azure.com/` 
    > [!IMPORTANT]
    > 对于所有“Web 活动”/“REST API Web 调用”，需确保 Synapse Pipeline 针对专用 SQL 池进行身份验证。 需要使用[托管标识](../../data-factory/control-flow-web-activity.md#managed-identity)来运行这些 REST API 调用。 
        
        
    ![专用 SQL 池的 Web 活动列表](./media/how-to-pause-resume-pipelines/web-activity-list-sql-pools.png)



## <a name="step-4-filter-dedicated-sql-pools"></a>步骤 4：筛选专用 SQL 池
删除你不想要暂停或恢复的专用 SQL 池。 使用筛选器活动来筛选从 Get 列表活动传递的值。  在此示例中，我们将从数组中提取其名称中不包含“prod”的记录。 根据需要应用其他条件。 例如，根据 Synapse 工作区的 SKU/名称进行筛选，以确保仅识别有效的专用 SQL 池。
1. 选择“迭代和条件”下的“Filter”活动，并将其拖放到管道画布中 。    
![筛选专用 SQL 池](./media/how-to-pause-resume-pipelines/filter-sql-pools.png)    
1. 将 Get 列表 Web 活动连接到 Filter 活动。 选择 Web 活动中的绿色标签并将其拖放到“Filter”框。
1. 为“项”输入 `@activity('Get list').output.value`，其中的“GET 列表”是前一 Web 活动的名称
1. 为“条件”输入 `@not(endswith(item().name,'prod'))`。 然后，数组中的其余记录将传递到下一活动。

## <a name="step-5-create-a-foreach-loop"></a>步骤 5：创建 ForEach 循环
创建一个 ForEach 活动以循环访问每个专用 SQL 池。 
1. 选择“迭代和条件”下的“ForEach”活动，并将其拖放到管道画布中 。
1. 在“常规”选项卡上为该活动命名，我们使用了“ForEach_pool”。 
1. 在“设置”选项卡上选择“项”输入，然后选择“添加动态内容” 。 滚动到“活动输出”，然后选择筛选器活动的输出。 将 `.value` 添加到该活动。 值应类似于 `@activity('Filter_PROD').output.value`。 选择“完成”。    
    ![循环访问专用 SQL 池](./media/how-to-pause-resume-pipelines/loop-through-sql-pools.png)
1. 选择“活动”选项卡，然后选择编辑（铅笔）图标打开 ForEach 循环画布。 

## <a name="step-5a-check-the-state-of-the-dedicated-sql-pools"></a>步骤 5a：检查专用 SQL 池的状态    
检查专用 SQL 池的状态需要用到一个 Web 活动，如同在步骤 1 中那样。 此活动将调用 [Azure Synapse 的“检查专用 SQL 池状态”REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#check-database-state)。 
1. 在“常规”下选择一个 Web 活动并将其拖放到管道画布中 。    
2. 在“常规”选项卡中，将此阶段命名为 CheckState。 
3. 选择“设置”选项卡。     
4. 在“URL”输入空间中单击，然后选择“添加动态内容” 。 将以下使用 @concat 字符串函数参数化的 GET 请求复制并粘贴到动态内容框中。 选择“完成”  。 再次检查状态时，会使用一个通过以下调用发出的 Get 请求：

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    使用 @concat 字符串函数参数化的 GET 请求：
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',item().name,'?api-version=2019-06-01-preview')
    ```
    
    在本例中，我们将使用 item().name，这是步骤 1 中已通过 ForEach 循环传递到此活动的专用 SQL 池名称。 如果使用管道来控制单个专用 SQL 池，则可以在此处嵌入该专用 SQL 池的名称，或者在管道中使用参数。 例如，可以使用 pipeline().parameters.SQLPoolName。

    输出是一个 JSON 字符串，其中包含专用 SQL 池的详细信息，其中包括其状态（在 properties.status 中提供）。 该 JSON 字符串将传递到下一个活动。 
1. 选择“方法”对应的下拉列表，然后选择“Get”。选择“高级”展开内容  。 选择“MSI”作为身份验证类型。 对于“资源”，请输入 `https://management.azure.com/` 

![检查专用 SQL 池的状态](./media/how-to-pause-resume-pipelines/check-sql-pool-state.png)
    

    
## <a name="step-5b-evaluate-the-state-of-the-dedicated-sql-pools"></a>步骤 5b：评估专用 SQL 池的状态
评估所需状态“暂停”或“恢复”以及当前状态“联机”或“已暂停”，然后根据需要启动暂停或恢复。

1. 选择“迭代和条件”下的“Switch”活动，并将其拖放到管道画布中 。    
1. 将“Switch”活动连接到“CheckState”活动 。 选择 Web 活动中的绿色标签并将其拖放到“Switch”框。  
1. 在“常规”选项卡中，将此阶段命名为 State-PauseOrResume。 

    根据所需状态和当前状态，只有以下两种组合才需要更改状态：“已暂停”->“恢复或联机”->“暂停”。 

1. 在 “活动”选项卡上，将以下代码复制到“表达式”中 。

    ```HTTP
    @concat(activity('CheckState').output.properties.status,'-',pipeline().parameters.PauseOrResume)
    ```
    
    其中，CheckState 是前面的 Web 活动的名称，output.properties.status 定义了当前状态，pipeline().parameters.PauseOrResume 指示所需状态。
    
    检查条件会检查所需状态和当前状态。 如果所需状态为“恢复”且当前状态为“已暂停”，则会在“已暂停-恢复”案例中调用 Resume 活动。 如果所需状态为“暂停”且当前状态为“联机”，则会在“联机-暂停”案例中调用 Pause 活动。 对于任何其他案例，例如所需状态为“暂停”且当前状态为“已暂停”，或者所需状态为“恢复”且当前状态为“联机”，不需要执行任何操作，这些案例将作为默认案例（不使用任何活动）进行处理。
1. 在“活动”选项卡上，选择“+ 添加案例”。  添加案例 `Paused-Resume` 和 `Online-Pause`。 
    ![检查专用 SQL 池的状态条件](./media/how-to-pause-resume-pipelines/check-condition.png)

### <a name="step-5c-pause-or-resume-dedicated-sql-pools"></a>步骤 5c：暂停或恢复专用 SQL 池     

对于某些要求而言，最终且唯一相关的步骤是启动专用 SQL 池的暂停或恢复。 此步骤同样使用 Web 活动，并调用 [Azure Synapse 的“暂停或恢复计算”REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#pause-compute)。 
1. 选择活动编辑（铅笔）图标，将某个 Web 活动添加到 State-PauseorResume 画布中。 
1. 选择“设置”选项卡，在“URL”输入空间中单击，然后选择“添加动态内容”  。 将以下使用 @concat 字符串函数参数化的 POST 请求复制并粘贴到动态内容框中。 选择“完成”  。 
1. 选择“方法”对应的下拉列表，然后选择“POST” 。
1. 在“正文”部分中键入“暂停和恢复”
1. 选择“高级”展开内容。 选择“MSI”作为身份验证类型。 对于“资源”，请输入 `https://management.azure.com/` 
1. 使用以下参数化代码为恢复功能添加另一个活动。

    ![恢复专用 SQL 池](./media/how-to-pause-resume-pipelines/true-condition-resume.png)
    
    
    此处的示例演示如何通过以下调用来调用 POST 请求，以恢复专用 SQL 池：
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/resume?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    可以使用 @concat 字符串函数来参数化上述 POST 语句：
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/resume?api-version=2019-06-01-preview')
    ```
    
    在本例中，我们将结合使用活动 'Check State'.output.name，以及在步骤 3a 中已通过 Switch 条件传递到此活动的专用 SQL 池名称。 如果对单一数据库使用单个活动，则可以在此处嵌入专用 SQL 池的名称，或者在管道中使用参数。 例如，可以使用 pipeline().parameters.DatabaseName。
    
    暂停专用 SQL 池的 POST 请求为：
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/pause?api-version=2019-06-01-preview HTTP/1.1    
    ```    
    
    可以使用 @concat 字符串函数来参数化 POST 请求，如下所示：
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/pause?api-version=2019-06-01-preview')
    ```

## <a name="pipeline-run-output"></a>管道运行输出

运行整个管道时，你将看到下面列出的输出。 可以通过选择“调试”模式或选择“添加触发器”来运行管道 。 根据下面的管道结果，名为“ResourceGroup”的管道参数已设置为包含两个 Synapse 工作区的单个资源组。 其中一个工作区名为 testprod 并已筛选掉，另一个工作区名为 test1。 test1 专用 SQL 池已暂停，因此作业启动了恢复。

![管道运行输出](./media/how-to-pause-resume-pipelines/pipeline-run-output.png)

## <a name="save-your-pipeline"></a>保存管道

若要保存管道，请选择该管道上方的“全部发布”。

## <a name="schedule-your-pause-or-resume-pipeline-to-run"></a>计划暂停或恢复管道的运行

若要计划管道，请选择该管道顶部的“添加触发器”。 按照屏幕中的说明，将管道计划为在指定的时间运行。

![选择触发器以设置管道运行时间](./media/how-to-pause-resume-pipelines/trigger.png)

## <a name="next-steps"></a>后续步骤

有关 Azure Synapse 的托管标识以及如何将托管标识添加到专用 SQL 池的更多详细信息，请参阅：

[Azure Synapse 工作区托管标识](../security/synapse-workspace-managed-identity.md)

[向工作区托管标识授予权限](../security/how-to-grant-workspace-managed-identity-permissions.md)

[Synapse 管道运行的 SQL 访问控制](../security/how-to-set-up-access-control.md#step-73-sql-access-control-for-azure-synapse-pipeline-runs)

