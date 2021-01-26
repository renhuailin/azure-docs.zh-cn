---
title: 使用实体行为分析来检测高级威胁 |Microsoft Docs
description: 在 Azure Sentinel 中启用用户和实体行为分析，并配置数据源
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
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 0dccd8ac4f852688bf7e59e7ed96c4458c08d18b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784722"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中 (UEBA) 启用用户和实体行为分析 

> [!IMPORTANT]
>
> - "UEBA" 和 "实体" 页功能现已 **公开上市** ，其中包括：*_所有_* _ Azure Sentinel 地域和区域。

## <a name="prerequisites"></a>先决条件

若要启用或禁用此功能 (使用此功能) 不需要这些先决条件：

- 你的用户必须是你组织的 Azure Active Directory 的成员，而不是来宾用户。

- 必须为用户分配 Azure AD 中的 "*全局管理员*" 或 " **安全管理员** " 角色。

- 你的用户必须至少分配以下 **azure 角色** 之一 ([详细了解 Azure RBAC](roles.md)) ：
    - 工作区或资源组级别的 **Azure Sentinel 参与者**。
    - 在资源组或订阅级别 **Log Analytics 参与者**。

- 你的工作区不得应用任何 Azure 资源锁。 [了解有关 Azure 资源锁定的详细信息](../azure-resource-manager/management/lock-resources.md)。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>如何启用用户和实体行为分析

1. 在 Azure Sentinel 导航菜单中，选择 " **实体行为**"。

1. 在标题下，将 **其** 切换到 **"打开**"。

1. 单击 " **选择数据源** " 按钮。

1. 在 " **数据源选择** " 窗格中，标记要启用 UEBA 的数据源旁边的复选框，然后选择 " **应用**"。

    > [!NOTE]
    >
    > 在 " **数据源选择** " 窗格的下半部分中，你将看到尚未启用的 UEBA 支持的数据源的列表。 
    >
    > 启用 UEBA 后，在连接新的数据源时，可以选择在 "数据连接器" 窗格中直接从 "数据连接器" 窗格中为 UEBA 启用它们。

1. 选择 " **前往实体搜索**"。 这会将你转到实体搜索窗格，从现在开始，你可以从 Azure 的主菜单中选择 **实体行为** 时看到这一点。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何启用和配置 Azure Sentinel (UEBA) 中的用户和实体行为分析。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
