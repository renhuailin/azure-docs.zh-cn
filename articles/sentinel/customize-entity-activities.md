---
title: 在 Azure Sentinel 实体时间线上自定义活动 | Microsoft Docs
description: 将自定义的活动添加到实体页时间线上的那些 Azure Sentinel 跟踪和显示画面
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: 737fce0dff06e8af5599158bb7b6e795bf43ba0c
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109811689"
---
# <a name="customize-activities-on-entity-page-timelines"></a>在实体页时间线上自定义活动

> [!IMPORTANT]
>
> - 活动自定义功能目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="introduction"></a>简介

除了 Azure Sentinel 本来就在时间线中跟踪和显示的活动以外，你还可以创建你想要在时间线中跟踪和显示的其他任何活动。 可以基于任何已连接的数据源中实体数据的查询创建自定义活动。 以下示例演示了如何使用此功能：

- 通过修改现成的活动模板将新活动添加到实体时间线。

- 从自定义日志添加新活动 - 例如，通过物理访问控制日志，可将某个用户在特定建筑物中的进门和出门活动添加到该用户的时间线。

## <a name="getting-started"></a>入门

1. 从 Azure Sentinel 导航菜单中选择“实体行为”。

1. 在“实体行为”边栏选项卡中，选择屏幕顶部的“自定义实体页” 。

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="“实体行为”页":::

1. 你将看到一个页面，其中列出了你在“我的活动”选项卡中创建的所有活动。在“活动模板”选项卡中，将会看到 Microsoft 安全研究人员现成提供的活动集合 。 这些活动已在实体页中的时间线上跟踪和显示。

## <a name="create-an-activity-from-a-template"></a>从模板创建活动

1. 单击“活动模板”选项卡查看默认提供的各种活动。 可以按实体类型和数据源筛选该列表。 从列表中选择一个活动会在预览窗格中显示以下详细信息：

    -  该活动的说明

    - 数据源，它提供了构成该活动的事件

    - 用于在原始数据中标识实体的标识符

    - 导致检测此活动的查询

1. 单击预览窗格底部的“创建活动”按钮以启动活动创建向导。 

1. 此时会打开“活动向导 - 从模板创建新活动”，其中的字段内已填充了来自模板的值。 可以在“常规”和“活动配置”选项卡中进行所需的更改 。

1. 如果对设置感到满意，请选择“查看并创建”选项卡。看到“已通过验证”消息后，单击底部的“创建”按钮  。

## <a name="create-an-activity-from-scratch"></a>从头开始创建活动

在“活动”页的顶部，单击“添加活动”以启动活动创建向导。

此时会打开“活动向导 - 创建新活动”，其中的字段是空的。

### <a name="general-tab"></a>“常规”选项卡
1. 输入活动的名称（例如：“添加到组中的用户”）。

1. 输入活动的说明（例如：“基于 Windows 事件 ID 4728 进行的用户组成员身份更改”）。

1. 选择此查询将要跟踪的实体类型（用户或主机）。

1. 可以按附加参数进行筛选，以帮助细化查询并优化其性能。 例如，可以选择“IsDomainJoined”参数并将值设置为“True”来筛选 Active Directory 用户 。

1. 可以选择“已启用”或“已禁用”作为活动的初始状态 。

### <a name="activity-configuration-tab"></a>活动配置选项卡

#### <a name="writing-the-activity-query"></a>编写活动查询

在这里，你将编写或粘贴 KQL 查询，用于检测所选实体的活动并确定该活动在时间线中的表示方式。

若要关联事件并检测自定义活动，需要在 KQL 查询中根据实体类型输入多个参数。 这些参数是相关实体的各种标识符。

最好是选择强标识符，这样可以在查询结果与实体之间实现一对一的映射。 选择弱标识符可能会产生不准确的结果。 [详细了解实体以及强标识符与弱标识符](entities-in-azure-sentinel.md)。

下表提供了有关实体标识符的信息。

帐户和主机实体的强标识符

查询中至少需要一个标识符。

| 实体 | 标识符 | 说明 |
| - | - | - |
| **帐户** | Account_Sid | Active Directory 中帐户的本地 SID |
| | Account_AadUserId | Azure Active Directory 中用户的 Azure AD 对象 ID |
| | Account_Name + Account_NTDomain | 类似于 SamAccountName（例如：Contoso\Joe） |
| | Account_Name + Account_UPNSuffix | 类似于 UserPrincipalName（例如：Joe@Contoso.com） |
| **主机** | Host_HostName + Host_NTDomain | 类似于完全限定的域名 (FQDN) |
| | Host_HostName + Host_DnsDomain | 类似于完全限定的域名 (FQDN) |
| | Host_NetBiosName + Host_NTDomain | 类似于完全限定的域名 (FQDN) |
| | Host_NetBiosName + Host_DnsDomain | 类似于完全限定的域名 (FQDN) |
| | Host_AzureID | Azure Active Directory 中主机的 Azure AD 对象 ID（如果已加入 AAD 域） |
| | Host_OMSAgentID | 安装在特定主机上的代理的 OMS 代理 ID（在每个主机中唯一） |
|

系统将根据所选的实体显示可用的标识符。 单击相关标识符会将标识符粘贴到查询中光标所在的位置。

> [!NOTE]
> - 查询必须包含“TimeGenerated”字段才能将检测到的活动放入实体的时间线中。
>
> - 在查询中最多可以投影 10 个字段。

#### <a name="presenting-the-activity-in-the-timeline"></a>在时间线中显示活动

你可以确定如何出于自己的便利在时间线中显示活动。

可以使用以下格式将动态参数添加到活动输出：`{{ParameterName}}`

可以添加以下参数： 

- 在查询中投影的任何字段  
- Count – 使用此参数可以汇总 KQL 查询输出的计数 
- StartTimeUTC – 活动开始时间，采用 UTC 格式 
- EndTimeUTC – 活动结束时间，采用 UTC 格式 

示例：“`{{SubjectUsername}}` 已将用户 `{{TargetUsername}}` 添加到组 `{{GroupName}}`”

### <a name="review-and-create-tab"></a>“审核并创建”选项卡 

1. 验证自定义活动的所有配置信息。

1. 出现“已通过验证”消息后，单击“创建”以创建活动 。 以后可以在“我的活动”选项卡中编辑或更改该活动。

## <a name="manage-your-activities"></a>管理活动 

在“我的活动”选项卡中管理自定义活动。单击活动所在行末尾的省略号图标 (...) 可执行以下操作：

- 编辑活动。
- 复制活动以创建一个略有不同的新活动。
- 删除活动。
- 禁用活动（但不删除它）。

## <a name="view-activities-in-an-entity-page"></a>在实体页中查看活动

每当进入实体页时，针对该实体启用的所有活动查询就会运行，并在实体时间线中提供最新的信息。 你将在时间线中看到活动以及警报和书签。 

可以使用“时间线内容”筛选器来仅显示活动（或者活动、警报和书签的任意组合）。  

还可以使用“活动”筛选器来显示或隐藏特定的活动。 

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何为实体页时间线创建自定义活动。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解有关[实体页](identify-threats-with-entity-behavior-analytics.md)的全貌。
- 查看[实体和标识符](entities-reference.md)的完整列表。
