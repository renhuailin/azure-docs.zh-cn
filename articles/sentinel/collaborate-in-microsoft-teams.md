---
title: 在 Microsoft Teams 中与 Azure Sentinel 事件团队协作 | Microsoft Docs
description: 了解如何从 Azure Sentinel 连接到 Microsoft Teams，以使用 Azure Sentinel 数据来与其他团队成员协作。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 3aefed370606b80249eabeac35123223b2ad5255
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729753"
---
# <a name="collaborate-in-microsoft-teams-public-preview"></a>在 Microsoft Teams（公共预览版）中协作

Azure Sentinel 支持直接与 [Microsoft Teams](/microsoftteams/) 集成，使你可以就特定的事件直接展开团队合作。


> [!IMPORTANT]
> 与 Microsoft Teams 的集成目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="overview"></a>概述

从 Azure Sentinel 直接与 Microsoft Teams 集成可以在整个组织中以及与外部利益干系人无缝协作。

在 Azure Sentinel 事件团队中使用 Microsoft Teams 可以专注于与相关人员进行沟通和协调。 对于高严重性、持续性的事件，将事件团队用作专用会议桥特别有用。

已使用 Microsoft Teams 进行沟通和协作的组织可以使用 Azure Sentinel 集成将安全数据直接引入其对话和日常工作中。 

Azure Sentinel 事件团队始终具有 Azure Sentinel 中的最新数据，确保团队随手可以获得最相关的数据。

## <a name="required-permissions"></a>所需的权限

若要从 Azure Sentinel 创建多个团队：

- 创建团队的用户必须在 Azure Sentinel 中享有事件写入权限。 例如，[Azure Sentinel 响应方](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)角色是此特权的理想最小角色。

- 创建团队的用户还必须有权在 Microsoft Teams 中创建团队。

- 任何 Azure Sentinel 用户（包括具有[读者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)、[响应方](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)或[参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)角色的用户）都可以通过请求访问权限来访问创建的团队。

## <a name="use-an-incident-team-to-investigate"></a>使用事件团队进行调查

通过直接从事件集成 Microsoft Teams，与事件团队一起进行调查。

若要创建事件团队，请执行以下操作：

1. 在 Azure Sentinel 的“威胁管理” > “事件”网格中，选择当前正在调查的事件 。

1. 在右侧显示的事件窗格底部，选择“操作” > “创建团队” 。

    [ ![创建一个团队以在事件团队中协作。](media/collaborate-in-microsoft-teams/create-team.png) ](media/collaborate-in-microsoft-teams/create-team.png#lightbox)

    右侧将打开“新建团队”窗格。 定义事件团队的以下设置：

    - 团队名称：自动定义为事件的名称。 根据需要修改名称，以便可以轻松识别该事件。
    - 说明：为事件团队输入有意义的说明。
    - 添加组：选择一个或多个要添加到事件团队的 Azure AD 组。 此页不支持个人用户。 如需添加单个用户，请在创建团队后[前往 Microsoft Teams 执行相应操作](#more-users)。

        > [!TIP]
        > 如果你经常与相同的团队合作，可以选择星形图标 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: 将其保存到收藏夹中。
        >
        > 下一次创建团队时，会自动选择收藏夹中的项。 如果要将其从创建的下一个团队中删除，请选择“删除”:::image type="icon" source="media/collaborate-in-microsoft-teams/delete-user-group.png" border="false":::，或者再次选择星形图标 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: 将该团队完全从收藏夹中删除。
        >

1. 添加完组后，选择“创建”以创建事件团队。

    事件窗格将会刷新，“团队名称”标题下提供了新事件团队的链接。

    [ ![单击添加到事件的 Teams 集成链接。](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg) ](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg#lightbox)


1. 选择“Teams 集成”链接切换到 Microsoft Teams，其中有关事件的所有数据都列在“事件页”选项卡上 。

    [ ![Microsoft Teams 中的事件页。](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

根据需要，在 Teams 中继续展开有关调查的对话。 可以直接在团队中获得完整的事件详细信息。

> [!TIP]
> - <a name="more-users"></a>如需将单个用户添加到团队，可以在 Microsoft Teams 中使用“帖子”选项卡上的“添加更多用户”按钮完成相应操作 。
>
> - [关闭事件](investigate-cases.md#closing-an-incident)时，在 Microsoft Teams 中创建的相关事件团队将会存档。 如果重新打开事件，则相关事件团队也会在 Microsoft Teams 中重新打开，使你可以在上次退出的位置继续对话。
>

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [教程：使用 Azure Sentinel 调查事件](investigate-cases.md)
- [Microsoft Teams 中的团队和频道概述](/microsoftteams/teams-channels-overview/)
