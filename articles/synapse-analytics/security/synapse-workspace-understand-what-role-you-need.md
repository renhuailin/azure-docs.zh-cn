---
title: 了解在 Synapse 中执行常见任务所需的角色
description: 本文介绍完成特定任务所需的内置 Synapse RBAC 角色
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 56337e349cf2cfad792cecbec11503a4fd866095
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253623"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>了解在 Synapse 中执行常见任务所需的角色

本文将帮助你了解在 Synapse Studio 中完成工作所需的 Synapse RBAC（基于角色的访问控制）或 Azure RBAC 角色。  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio 访问控制和工作流摘要 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>访问 Synapse Studio 并查看其内容

- 如果已向你分配任何 Synapse RBAC 角色或者你在工作区上具有 Azure 所有者、参与者或读取者角色，则可以打开 Synapse Studio 并查看工作区的详细信息，并列出其任何 Azure 资源（SQL 池、Spark 池或集成运行时）。

### <a name="resource-management"></a>资源管理

- 如果你是工作区的 Azure 所有者或参与者，则可以创建 SQL 池、Apache Spark 池和集成运行时。
- 如果你是工作区或该资源的 Azure 所有者或参与者，则可以暂停或缩放专用 SQL 池、配置 Spark 池或集成运行时。

### <a name="viewing-and-editing-code-artifacts"></a>查看和编辑代码项目

- 如果具有对 Synapse Studio 的访问权限，则可以创建新的代码项目，例如 SQL 脚本、笔记本、spark 作业、链接服务、管道、数据流、触发器和凭据。  （可通过其他权限发布或保存这些项目。）  
- 如果你是 Synapse 项目用户、Synapse 项目发布者、Synapse 参与者或 Synapse 管理员，则可以列出、打开和编辑已发布的代码项目。

### <a name="executing-your-code"></a>执行代码

- 如果你具有 SQL 池中定义的必需的 SQL 权限，则可以在 SQL 池上执行 SQL 脚本。  
- 如果你具有对工作区或特定 Apache Spark 池的 Synapse 计算操作员权限，则可以运行笔记本和 Spark 作业。  
- 具有对工作区或特定集成运行时的计算操作员权限以及适当的凭据权限时，可以执行管道。

### <a name="monitoring-and-managing-execution"></a>监视和管理执行
- 如果你是 Synapse 用户，则可以查看 Apache Spark 池中正在运行的笔记本和作业的状态。
- 如果你是工作区或特定 Spark 池或管道的 Synapse 计算操作员，则可以查看日志和取消正在运行的作业和管道。  

### <a name="publishing-and-saving-your-code"></a>发布和保存代码

- 如果你是 Synapse 项目发布者、Synapse 参与者或 Synapse 管理员，则可以将新的或更新的代码项目发布到服务。 
- 如果工作区启用了 Git 并且你具有 Git 权限，则可以将代码项目提交到 Git 存储库的工作分支。 启用 Git 后，只能从协作分支进行发布。
- 如果关闭 Synapse Studio 时未发布或提交对代码项目的更改，则这些更改将丢失。


## <a name="tasks-and-required-roles"></a>任务和所需角色

下表列出了常见任务以及每项任务所需的 Synapse RBAC 或 Azure RBAC 角色。  

>[!Note]
>- 不会针对每项任务列出 Synapse 管理员，除非它是提供必要权限的唯一角色。  Synapse 管理员可以执行其他 Synapse RBAC 角色启用的所有任务。</br>
>- 显示了所需的最小 Synapse RBAC 角色。
>- 任何范围内的所有 Synapse RBAC 角色都在工作区提供 Synapse 用户权限
>- 表中显示的所有 Synapse RBAC 权限/操作都带有前缀 Microsoft/Synapse/workspaces/… </br>


任务（我想要…） |角色（我需要是…）|Synapse RBAC 权限/操作
--|--|--
|在工作区上打开 Synapse Studio|Synapse 用户，或者|读取
| |工作区的 Azure 所有者、参与者或读取者|无
|列出 SQL 池、Apache Spark 池、集成运行时并访问其配置详细信息|Synapse 用户，或者|读取|
||工作区的 Azure 所有者、参与者或读取者|无
|列出链接服务、凭据、托管的专用终结点|Synapse 用户|读取
SQL 池|
创建专用 SQL 池或无服务器 SQL 池|工作区的 Azure 所有者或参与者|无
管理（暂停、缩放或删除）专用 SQL 池|SQL 池或工作区的 Azure 所有者或参与者|无
创建 SQL 脚本</br>|Synapse 用户，或者 </br>工作区的 Azure 所有者或参与者， </br>运行 SQL 脚本、发布或提交更改时需要其他 SQL 权限。|
列出并打开任何已发布的 SQL 脚本| Synapse 项目用户、项目发布者、Synapse 参与者|项目/读取
在无服务器 SQL 池上运行 SQL 脚本|池的 SQL 权限（自动授予 Synapse 管理员）|无
在专用 SQL 池上运行 SQL 脚本|池的 SQL 权限|无
发布新的、已更新的或已删除的 SQL 脚本|Synapse 项目发布者、Synapse 参与者|sqlScripts/写入、删除
将对 SQL 脚本的更改提交到 Git 存储库|需要存储库上的 Git 权限|
在工作区上分配 Active Directory 管理员（通过 Azure 门户中的工作区属性）|工作区的 Azure 所有者或参与者 |
APACHE SPARK 池|
创建 Apache Spark 池|工作区的 Azure 所有者或参与者|
监视 Apache Spark 应用程序| Synapse 用户|读取
查看笔记本和作业执行的日志 |Synapse 计算操作员|
取消 Apache Spark 池上运行的任何笔记本或 Spark 作业|Apache Spark 池上的 Synapse 计算操作员。|bigDataPools/useCompute
创建笔记本或作业定义|Synapse 用户，或者 </br>工作区的 Azure 所有者、参与者或读取者</br> 运行、发布或提交更改时需要其他权限|读取</br></br></br></br></br> 
列出并打开已发布的笔记本或作业定义，包括查看保存的输出|工作区的 Synapse 项目用户、Synapse 项目发布者、Synapse 参与者|项目/读取
运行笔记本并查看其输出，或提交 Spark 作业|所选 Apache Spark 池上的 Synapse Apache Spark 管理员、Synapse 计算操作员|bigDataPools/useCompute 
将笔记本或作业定义（包括输出）发布到服务或将其删除|工作区的项目发布者、Synapse Apache Spark 管理员|笔记本/写入、删除
将对笔记本或作业定义的更改提交到 Git 存储库|Git 权限|无
管道、集成运行时、数据流、数据集和触发器|
创建、更新或删除集成运行时|工作区的 Azure 所有者或参与者|
监视集成运行时状态|Synapse 计算操作员|read, integrationRuntimes/viewLogs
查看管道运行|Synapse 项目发布者/Synapse 参与者|读取、管道/viewOutputs 
创建管道 |Synapse 用户</br>调试、添加触发器、发布或提交更改时需要其他 Synapse 权限|读取
创建数据流或数据集 |Synapse 用户</br>发布或提交更改时需要其他 Synapse 权限|读取
列出并打开已发布的管道 |Synapse 项目用户 | 项目/读取
预览数据集数据|WorkspaceSystemIdentity 上的 Synapse 用户 + Synapse 凭据用户| 
使用默认集成运行时调试管道|WorkspaceSystemIdentity 凭据上的 Synapse 用户 + Synapse 凭据用户|读取， </br>凭据/useSecret
创建触发器，包括“立即触发”（需要权限才能执行管道）|WorkspaceSystemIdentity 上的 Synapse 用户 + Synapse 凭据用户|读取、凭据/useSecret/操作
执行/运行管道|WorkspaceSystemIdentity 上的 Synapse 用户 + Synapse 凭据用户|读取、凭据/useSecret/操作
使用“复制数据”工具复制数据|工作区系统标识上的 Synapse 用户 + Synapse 凭据用户|读取、凭据/useSecret/操作
引入数据（使用计划）|工作区系统标识上的 Synapse 作者 + Synapse 凭据用户|读取、凭据/useSecret/操作
向服务发布新的、已更新的或已删除的管道、数据流或触发器|工作区的 Synapse 项目发布者|管道/写入、删除</br>数据流/写入、删除</br>触发器/写入、删除
将对管道、数据流、数据集或触发器的更改提交到 Git 存储库 |Git 权限|无 
链接服务|
创建链接服务（包括分配凭据）|Synapse 用户</br>使用带有凭据的链接服务或者发布或提交更改时需要其他权限|读取
列出并打开已发布的链接服务|Synapse 项目用户|linkedServices/写入、删除  
对受凭据保护的链接服务进行连接测试|Synapse 用户 + Synapse 凭据用户|凭据/useSecret/操作|
发布链接服务|Synapse 项目发布者、Synapse 链接数据管理员|linkedServices/写入、删除
将链接服务定义提交到 Git 存储库|Git 权限|无
访问管理|
查看任何范围内的 Synapse RBAC 角色分配|Synapse 用户|读取
为用户、组和服务主体分配和删除 Synapse RBAC 角色分配| 工作区或特定工作区项范围内的 Synapse 管理员|roleAssignments/写入、删除 

>[!Note]
>来自其他租户的来宾用户在被分配为 Synapse 管理员后，也可以查看、添加或更改角色分配。 

## <a name="next-steps"></a>后续步骤

了解[如何查看 Synapse RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md)

了解[如何管理 Synapse RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md)。 
