---
title: Synapse RBAC 角色
description: 本文介绍内置的 Synapse RBAC 角色
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: a978113265e5e61c0fc09ef0daeb1da9826f294d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572790"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC 角色

本文介绍内置的 Synapse RBAC 角色、其授予的权限，以及可以使用这些角色的范围。  

## <a name="whats-changed-since-the-preview"></a>预览后，会发生什么变化？
对于熟悉在预览期间提供的 Synapse RBAC 角色的用户，以下更改适用：
- 工作区管理员已重命名 **Synapse 管理员**
- Apache Spark 管理员将被重命名为 **Synapse Apache Spark 管理员** ，并具有查看所有已发布代码项目（包括 SQL 脚本）的权限。  此角色不再给予权限使用工作区 MSI，这需要 Synapse Credential 用户角色。  运行管道需要此权限。 
- SQL 管理员已重命名为 **SYNAPSE Sql 管理员** ，并具有查看所有已发布代码项目（包括 Spark 笔记本和作业）的权限。  此角色不再给予权限使用工作区 MSI，这需要 Synapse Credential 用户角色。 运行管道需要此权限。
- 引入了 **新的细化 SYNAPSE RBAC 角色**，重点介绍如何支持开发和操作角色，而不是特定的分析运行时。  
- 为多个角色引入了 **新的较低级别的作用域**。  这些作用域允许角色限制为特定的资源或对象。

>[!Note]
>**新的 SYNAPSE RBAC 角色和更低级别的作用域目前处于预览阶段**。  建议你使用这些新的角色和作用域（完全受支持），并提供有关使用的反馈。

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>内置 Synapse RBAC 角色和范围

下表描述了内置角色和可用于这些角色的范围。

>[!Note]
> 任何范围内具有任意 Synapse RBAC 角色的用户在工作区范围内自动具有 Synapse 用户角色。 

|角色 |权限|作用域|
|---|---|-----|
|Synapse 管理员  |完全 Synapse 访问无服务器 SQL 池、Apache Spark 池和集成运行时。  包括对所有已发布代码项目的创建、读取、更新和删除访问权限。  包括对工作区系统标识凭据的计算运算符、链接数据管理器和凭据用户权限。  包括分配 Synapse RBAC 角色。  需要 Azure 权限才能创建、删除和管理计算资源。 </br></br>_可以读取和写入项目， </br> 对 Spark 活动执行所有操作。 </br> 可以查看 Spark 池日志 </br> 可以查看已保存的笔记本，管道输出 </br> 可以使用链接服务存储的机密，还可以使用 </br> sql、、和权限连接到 sql 无服务器终结点，并且 `db_datareader` `db_datawriter` `connect` `grant` </br> 可以在当前范围内分配和撤消 Synapse RBAC 角色_|工作区 </br> Spark 池<br/>集成运行时 </br>链接服务</br>凭据 |
|Synapse Apache Spark 管理员</br>|Apache Spark 池的完全 Synapse 访问权限。  创建、读取、更新和删除对发布的 Spark 作业定义、笔记本及其输出以及库、链接服务和凭据的访问权限。  包括对所有其他已发布代码项目的读取访问权限。 不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>_可对 spark 项目执行所有操作 </br> 可以对 spark 活动执行所有操作_|工作区</br>Spark 池|
|Synapse SQL 管理员|对无服务器 SQL 池的完全 Synapse 访问。  创建、读取、更新和删除已发布的 SQL 脚本、凭据和链接服务的访问权限。  包括对所有其他已发布代码项目的读取访问权限。  不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>*可以对 sql 脚本执行的所有操作都 <br/> 可以通过 sql `db_datareader` 、 `db_datawriter` 、 `connect` 和 `grant` 权限连接到 sql 无服务器终结点*|工作区|
|Synapse 参与者|完全 Synapse 访问无服务器 SQL 池、Apache Spark 池、集成运行时。  包括对所有已发布代码项目及其输出的创建、读取、更新和删除访问权限，包括凭据和链接服务。  包含计算操作员权限。 不包括使用凭据和运行管道的权限。 不包括授予访问权限。 </br></br>_可以读取和写入项目 </br> 可查看已保存的笔记本和管道输出 </br> 可以对 spark 活动执行所有操作 </br> 可以查看 spark 池日志_|工作区 </br> Spark 池<br/> 集成运行时|
|Synapse 项目发布服务器|创建、读取、更新和删除已发布代码项目及其输出的访问权限。 不包括运行代码或管道的权限，或授予访问权限。 </br></br>_可以读取已发布的项目并发布项目 </br> 可查看已保存的笔记本、Spark 作业和管道输出_|工作区
|Synapse 项目用户|对已发布代码项目及其输出的读取访问权限。 可以创建新项目，但不能发布更改，也不能运行代码而无需其他权限。|工作区
|Synapse 计算运算符 |提交 Spark 作业和笔记本，并查看日志。  包括取消任何用户提交的 Spark 作业。 需要对工作区系统标识使用额外的凭据权限来运行管道、查看管道运行和输出。 </br></br>_可以提交和取消作业，包括其他人提交的作业 </br> 可以查看 Spark 池日志_|工作区</br>Spark 池</br>集成运行时|
|Synapse 凭据用户|运行时和配置时间在凭据中使用机密，在管道运行等活动中使用链接服务。 若要运行管道，则需要此角色，其作用域为工作区系统标识。 </br></br>_作用域限定为凭据，允许通过凭据保护的链接服务访问数据 (也需要计算使用权限) </br> 允许使用额外的计算使用权限 (执行工作区系统标识凭据所保护的管道)_|工作区 </br>链接服务</br>凭据
|Synapse 链接数据管理器|创建和管理托管的专用终结点、链接服务和凭据。 可以创建使用受凭据保护的链接服务的托管专用终结点|工作区|
|Synapse 用户|列出并查看 SQL 池、Apache Spark 池、集成运行时和已发布的链接服务和凭据的详细信息。 不包含其他已发布的代码项目。  可以创建新项目，但不能在没有其他权限的情况下运行或发布。</br></br>_可以列出和读取 Spark 池、集成运行时。_|工作区，Spark 池</br>链接服务 </br>凭据|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC 角色及其允许的操作

>[!Note]
>- 下表中列出的所有操作都带有前缀 "Synapse/..."</br>
>- 所有项目中的 "读取"、"写入" 和 "删除" 操作都与 live service 中的已发布项目有关。  这些权限不会影响对连接的 Git 存储库中的项目的访问。  

下表列出了内置角色以及每个角色支持的操作/权限。

角色|操作
--|--
Synapse 管理员|工作区/读取</br>工作区/roleAssignments/写入、删除</br>工作区/managedPrivateEndpoint/写入、删除</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>工作区/项目/读取</br>工作区/笔记本/写入、删除</br>工作区/sparkJobDefinitions/写入、删除</br>工作区/sqlScripts/写入、删除</br>工作区/数据流/写入、删除</br>工作区/管道/写入、删除</br>工作区/触发器/写入，删除</br>工作区/数据集/写入、删除</br>工作区/库/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除</br>工作区/笔记本/viewOutputs/操作</br>工作区/管道/viewOutputs/操作</br>workspace/Linkedservices.json/useSecret/action</br>工作区/凭据/useSecret/操作|
|Synapse Apache Spark 管理员|工作区/读取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>工作区/笔记本/viewOutputs/操作</br>工作区/项目/读取</br>工作区/笔记本/写入、删除</br>工作区/sparkJobDefinitions/写入、删除</br>工作区/库/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除|
|Synapse SQL 管理员|工作区/读取</br>工作区/项目/读取</br>工作区/sqlScripts/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除|
|Synapse 参与者|工作区/读取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>workspace/integrationRuntimes/viewLogs/action</br>工作区/项目/读取</br>工作区/笔记本/写入、删除</br>工作区/sparkJobDefinitions/写入、删除</br>工作区/sqlScripts/写入、删除</br>工作区/数据流/写入、删除</br>工作区/管道/写入、删除</br>工作区/触发器/写入，删除</br>工作区/数据集/写入、删除</br>工作区/库/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除</br>工作区/笔记本/viewOutputs/操作</br>工作区/管道/viewOutputs/操作|
|Synapse 项目发布服务器|工作区/读取</br>工作区/项目/读取</br>工作区/笔记本/写入、删除</br>工作区/sparkJobDefinitions/写入、删除</br>工作区/sqlScripts/写入、删除</br>工作区/数据流/写入、删除</br>工作区/管道/写入、删除</br>工作区/触发器/写入，删除</br>工作区/数据集/写入、删除</br>工作区/库/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除</br>工作区/笔记本/viewOutputs/操作</br>工作区/管道/viewOutputs/操作|
|Synapse 项目用户|工作区/读取</br>工作区/项目/读取</br>工作区/笔记本/viewOutputs/操作</br>工作区/管道/viewOutputs/操作|
|Synapse 计算运算符 |工作区/读取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>workspace/integrationRuntimes/viewLogs/action|
|Synapse 凭据用户|工作区/读取</br>workspace/Linkedservices.json/useSecret/action</br>工作区/凭据/useSecret/操作|
|Synapse 链接数据管理器|工作区/读取</br>工作区/managedPrivateEndpoint/写入、删除</br>工作区/Linkedservices.json/写入、删除</br>工作区/凭据/写入、删除|
|Synapse 用户|工作区/读取|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC 操作及其允许的角色

下表列出了允许以下操作的 Synapse 操作和内置角色：

操作|角色
--|--
工作区/读取|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 项目用户</br>Synapse 计算运算符 </br>Synapse 凭据用户</br>Synapse 链接数据管理器</br>Synapse 用户 
工作区/roleAssignments/写入、删除|Synapse 管理员
工作区/managedPrivateEndpoint/写入、删除|Synapse 管理员</br>Synapse 链接数据管理器
workspace/bigDataPools/useCompute/action|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 计算运算符 
workspace/bigDataPools/viewLogs/action|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 计算运算符 
workspace/integrationRuntimes/useCompute/action|Synapse 管理员</br>Synapse 参与者</br>Synapse 计算运算符 
工作区/项目/读取|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 项目用户
工作区/笔记本/写入、删除|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/sparkJobDefinitions/写入、删除|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/sqlScripts/写入、删除|Synapse 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/数据流/写入、删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/管道/写入、删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/触发器/写入，删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/数据集/写入、删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/库/写入、删除|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器
工作区/Linkedservices.json/写入、删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 链接数据管理器
工作区/凭据/写入、删除|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 链接数据管理器
工作区/笔记本/viewOutputs/操作|Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 项目用户
工作区/管道/viewOutputs/操作|Synapse 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 项目用户
workspace/Linkedservices.json/useSecret/action|Synapse 管理员</br>Synapse 凭据用户
工作区/凭据/useSecret/操作|Synapse 管理员</br>Synapse 凭据用户

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC 范围及其支持的角色

下表列出了可在每个范围分配的 Synapse RBAC 范围和角色。 

>[!note]
>若要创建或删除对象，必须在更高级别范围内具有权限。

范围|角色
--|--
工作区 |Synapse 管理员</br>Synapse Apache Spark 管理员</br>Synapse SQL 管理员</br>Synapse 参与者</br>Synapse 项目发布服务器</br>Synapse 项目用户</br>Synapse 计算运算符 </br>Synapse 凭据用户</br>Synapse 链接数据管理器</br>Synapse 用户
Apache Spark 池 | Synapse 管理员 </br>Synapse 参与者 </br> Synapse 计算运算符
集成运行时 | Synapse 管理员 </br>Synapse 参与者 </br> Synapse 计算运算符
链接服务 |Synapse 管理员 </br>Synapse 凭据用户
凭据 |Synapse 管理员 </br>Synapse 凭据用户
 
>[!note]
>所有项目角色和操作都在工作区级别范围内。 

## <a name="next-steps"></a>后续步骤

了解如何为工作区 [查看 SYNAPSE RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md) 。

了解 [如何分配 SYNAPSE RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)