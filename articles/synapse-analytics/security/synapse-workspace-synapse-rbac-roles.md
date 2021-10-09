---
title: Synapse RBAC 角色
description: 本文介绍内置的 Synapse RBAC 角色
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 6b6c2cbf04b0e68f86bd11b8295c54b8c65d0360
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352779"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC 角色

本文介绍内置的 Synapse RBAC 角色、它们授予的权限，以及可以使用这些角色的范围。  

## <a name="whats-changed-since-the-preview"></a>从预览以来发生了哪些变化？
对于熟悉预览期间提供的 Synapse RBAC 角色的用户，以下更改适用：
- 工作区管理员已重命名为 Synapse 管理员
- Apache Spark 管理员已重命名为 Synapse Apache Spark 管理员，并具有查看所有已发布代码项目（包括 SQL 脚本）的权限。  此角色不再授予使用工作区 MSI 的权限，这项授权需使用 Synapse 凭据用户角色。  运行管道需要此权限。 
- SQL 管理员已重命名为 Synapse SQL 管理员，并具有查看所有已发布代码项目（包括 Spark 笔记本和作业）的权限。  此角色不再授予使用工作区 MSI 的权限，这项授权需使用 Synapse 凭据用户角色。 运行管道需要此权限。
- 引入了 **新的更加精细的 Synapse RBAC 角色**，专注于支持开发和操作角色，而不是特定的分析运行时。  
- 为多个角色引入了新的较低级别范围。  这些范围允许将角色限制为使用特定的资源或对象。

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>内置 Synapse RBAC 角色和范围

下表描述了内置角色和可以使用这些角色的范围。

>[!Note]
> 在任何范围内具有任意 Synapse RBAC 角色的用户在工作区范围内自动具有 Synapse 用户角色。 

> [!IMPORTANT]
> Synapse RBAC 角色不会授予在 Synapse 工作区中创建和管理 SQL 池、Apache Spark 池和集成运行时的权限。 这些操作需要资源组上的 Azure 所有者或 Azure 参与者角色。

|角色 |权限|作用域|
|---|---|-----|
|Synapse 管理员  |对 SQL 池、Apache Spark 池和集成运行时的完全 Synapse 访问。  包括对所有已发布代码项目的创建、读取、更新和删除访问权限。  包括对工作区系统标识凭据的计算操作员、链接数据管理员和凭据用户权限。  包括分配 Synapse RBAC 角色。 除了 Synapse 管理员以外，Azure 所有者也可以分配 Synapse RBAC 角色。 创建、删除和管理计算资源需要 Azure 权限。 </br></br>可以读写工件 </br>可以对 Spark 活动执行所有操作</br>。可以查看 Spark 池日志</br> 可以查看保存的笔记本和管道输出</br> 可以使用链接服务或凭据存储的机密</br> 可以在当前范围内分配和撤销 Synapse RBAC 角色|工作区 </br> Spark 池<br/>集成运行时 </br>链接服务</br>凭据 |
|Synapse Apache Spark 管理员</br>|对 Apache Spark 池的完全 Synapse 访问权限。  对已发布的 Spark 作业定义、笔记本及其输出以及对库、链接服务和凭据的创建、读取、更新和删除访问权限。  包括对所有其他已发布代码项目的读取权限。 不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>_可以对 Spark 项目执行所有操作</br>可以对 Spark 活动执行所有操作_|工作区</br>Spark 池|
|Synapse SQL 管理员|对无服务器 SQL 池的完全 Synapse 访问权限。  对已发布的 SQL 脚本、凭据和链接服务的创建、读取、更新和删除访问权限。  包括对所有其他已发布代码项目的读取权限。  不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>*可以对 SQL 脚本执行所有操作<br/>可以通过 SQL `db_datareader``db_datawriter``connect` 和 `grant` 权限连接到 SQL 无服务器终结点*|工作区|
|Synapse 参与者|对 Apache Spark 池和集成运行时的完全 Synapse 访问权限。 包括对所有已发布项目及其输出（包括凭据和链接的服务）的创建、读取、更新和删除访问权限。  包括计算操作员权限。 不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>_可以读取和写入项目</br>可以查看已保存的笔记本和管道输出</br>可以对 Spark 活动执行所有操作</br>可以查看 Spark 池日志_|工作区 </br> Spark 池<br/> 集成运行时|
|Synapse 项目发布者|对已发布代码项目及其输出的创建、读取、更新和删除访问权限。 不包括运行代码或管道（或授予访问权限）的权限。 </br></br>_可以读取已发布的项目并发布项 </br>可以查看已保存的笔记本、Spark 作业和管道输出_|工作区
|Synapse 项目用户|对已发布代码项目及其输出的读取访问权限。 可以创建新项目，但不能发布更改，也不能在没有其他权限的情况下运行代码。|工作区
|Synapse 计算操作员 |提交 Spark 作业和笔记本以及查看日志。  包括取消任何用户提交的 Spark 作业。 需要对工作区系统标识使用额外的凭据权限来运行管道、查看管道运行和输出。 </br></br>_可以提交和取消作业，包括其他人提交的作业</br>可以查看 Spark 池日志_|工作区</br>Spark 池</br>集成运行时|
|Synapse 凭据用户|在管道运行等活动中，对凭据和链接服务中的机密的运行时和配置时使用。 若要运行管道，则需要此角色，其范围限定为工作区系统标识。 </br></br>_范围限定为凭据，允许通过受凭据保护的链接服务访问数据（还需要计算使用权限）</br>允许执行受工作区系统标识凭据保护的管道（具有额外的计算使用权限）_|工作区 </br>链接服务</br>凭据
|Synapse 链接数据管理员|创建和管理托管专用终结点、链接服务和凭据。 可以创建使用受凭据保护的链接服务的托管专用终结点|工作区|
|Synapse 用户|列出并查看 SQL 池、Apache Spark 池、集成运行时和已发布的链接服务和凭据的详细信息。 不包括其他已发布的代码项目。  可以创建新项目，但不能在没有其他权限的情况下运行或发布项目。</br></br>_可以列出和读取 Spark 池、集成运行时。_|工作区、Spark 池</br>链接服务 </br>凭据|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC 角色及其允许的操作

>[!Note]
>- 下表中列出的所有操作都带有前缀“Microsoft.Synapse/...”</br>
>- 所有项目读取、写入和删除操作都与实时服务中的已发布项目有关。  这些权限不会影响对连接的 Git 存储库中项目的访问。  

下表列出了内置角色以及每个角色支持的操作/权限。

角色|操作
--|--
Synapse 管理员|workspaces/read</br>workspaces/roleAssignments/write, delete</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse Apache Spark 管理员|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/notebooks/viewOutputs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse SQL 管理员|workspaces/read</br>workspaces/artifacts/read</br>workspaces/sqlScripts/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse 参与者|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 项目发布者|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 项目用户|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 计算操作员 |workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action|
|Synapse 凭据用户|workspaces/read</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse 链接数据管理员|workspaces/read</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse 用户|workspaces/read|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC 操作及允许这些操作的角色

下表列出了 Synapse 操作以及允许这些操作的内置角色：

操作|角色
--|--
workspaces/read|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 项目用户</br>Synapse 计算操作员 </br>Synapse 凭据用户</br>Synapse 链接数据管理员</br>Synapse 用户 
workspaces/roleAssignments/write, delete|Synapse 管理员
workspaces/managedPrivateEndpoint/write, delete|Synapse 管理员</br>Synapse 链接数据管理员
workspaces/bigDataPools/useCompute/action|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 计算操作员 
workspaces/bigDataPools/viewLogs/action|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 计算操作员 
workspaces/integrationRuntimes/useCompute/action|Synapse 管理员</br>Synapse 参与者</br>Synapse 计算操作员
workspaces/integrationRuntimes/viewLogs/action|Synapse 管理员</br>Synapse 参与者</br>Synapse 计算操作员
workspaces/artifacts/read|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 项目用户
workspaces/notebooks/write, delete|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/sparkJobDefinitions/write, delete|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/sqlScripts/write, delete|Synapse 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/dataFlows/write, delete|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/pipelines/write, delete|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/triggers/write, delete|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/datasets/write, delete|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/libraries/write, delete|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布者
workspaces/linkedServices/write, delete|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 链接数据管理员
workspaces/credentials/write, delete|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 链接数据管理员
workspaces/notebooks/viewOutputs/action|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 项目用户
workspaces/pipelines/viewOutputs/action|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 项目用户
workspaces/linkedServices/useSecret/action|Synapse 管理员</br>Synapse 凭据用户
workspaces/credentials/useSecret/action|Synapse 管理员</br>Synapse 凭据用户

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC 范围及其支持的角色

下表列出了可在每个范围分配的 Synapse RBAC 范围和角色。 

>[!note]
>若要创建或删除对象，必须具有更高级别范围的权限。

范围|角色
--|--
工作区 |Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布者</br>Synapse 项目用户</br>Synapse 计算操作员 </br>Synapse 凭据用户</br>Synapse 链接数据管理员</br>Synapse 用户
Apache Spark 池 | Synapse 管理员 </br>Synapse 参与者 </br> Synapse 计算操作员
集成运行时 | Synapse 管理员 </br>Synapse 参与者 </br> Synapse 计算操作员
链接服务 |Synapse 管理员 </br>Synapse 凭据用户
凭据 |Synapse 管理员 </br>Synapse 凭据用户
 
>[!note]
>所有项目角色和操作的范围都限制在工作区级别。 

## <a name="next-steps"></a>后续步骤

了解[如何查看工作区的 Synapse RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md)。

了解[如何分配 Synapse RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)
