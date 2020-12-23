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
ms.date: 10/28/2020
ms.author: yelevin
ms.openlocfilehash: 140228a65be166bc172e81267c4449b49621e02c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425773"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中 (UEBA) 启用用户和实体行为分析 

> [!IMPORTANT]
>
> - UEBA 和实体页功能现已在以下 Azure Sentinel 地理位置和区域 **公开上市** ：
>    - 美国地域
>    - 西欧地区
>    - 澳大利亚地区
>
> - 在所有其他地理位置和区域，这些功能在 **预览** 阶段仍保留下来。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

## <a name="prerequisites"></a>必备知识

若要启用或禁用此功能 (使用此功能) 不需要这些先决条件：

- 你的用户必须是你组织的 Azure Active Directory 的成员，而不是来宾用户。

- 你的用户必须被分配到 Azure AD 中的 " **全局管理员** " 或 " **安全管理员** " 角色。

- 你的用户必须至少分配以下 **azure 角色** 之一 ( [详细了解 Azure RBAC](roles.md)) ：
    - 工作区或资源组级别的 **Azure Sentinel 参与者** 。
    - 在资源组或订阅级别 **Log Analytics 参与者** 。

- 你的工作区不得应用任何 Azure 资源锁。 [了解有关 Azure 资源锁定的详细信息](../azure-resource-manager/management/lock-resources.md)。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>如何启用用户和实体行为分析

1. 在 Azure Sentinel 导航菜单中，选择 " **实体行为** "。

1. 在标题下，将 **其** 切换到 **"打开** "。

1. 单击 " **选择数据源** " 按钮。

1. 在 " **数据源选择** " 窗格中，标记要启用 UEBA 的数据源旁边的复选框，然后选择 " **应用** "。

    > [!NOTE]
    >
    > 在 " **数据源选择** " 窗格的下半部分中，你将看到尚未启用的 UEBA 支持的数据源的列表。 
    >
    > 启用 UEBA 后，在连接新的数据源时，可以选择在 "数据连接器" 窗格中直接从 "数据连接器" 窗格中为 UEBA 启用它们。

1. 选择 " **前往实体搜索** "。 这会将你转到实体搜索窗格，从现在开始，你可以从 Azure 的主菜单中选择 **实体行为** 时看到这一点。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何启用和配置 Azure Sentinel (UEBA) 中的用户和实体行为分析。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
