---
title: 使用实体行为分析来检测高级威胁 | Microsoft Docs
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
ms.openlocfilehash: 985f1d4edacd81b7567124845836c3d93f976bb2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779730"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中启用用户和实体行为分析 (UEBA) 

> [!IMPORTANT]
>
> UEBA 和实体页功能现已在所有 Azure Sentinel 地理位置和区域正式发布 。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

启用或禁用此功能（使用此功能不需要这些先决条件）：

- 用户必须是你组织的 Azure Active Directory 的成员，而不是来宾用户。

- 必须为用户分配 Azure AD 中的“全局管理员”或“安全管理员”角色。

- 必须至少为用户分配以下“Azure 角色”之一（[了解有关 Azure RBAC 的详细信息](roles.md)）：
    - 工作区或资源组级别的“Azure Sentinel 参与者”。
    - 资源组或订阅级别的“Log Analytics 参与者”。

- 工作区不得应用任何 Azure 资源锁。 [了解有关 Azure 资源锁的详细信息](../azure-resource-manager/management/lock-resources.md)。

> [!NOTE]
> 向 Azure Sentinel 添加 UEBA 功能不需要任何特殊许可证，但可能会收取“额外费用”。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>如何启用用户和实体行为分析

1. 从 Azure Sentinel 导航菜单中选择“实体行为”。

1. 在标题“启用它”下，切换到“打开” 。

1. 单击“选择数据源”按钮。

1. 在“数据源选择”窗格中，选中要启用 UEBA 的数据源旁边的复选框，然后选择“应用”。

    > [!NOTE]
    >
    > 在“数据源选择”窗格的下半部分，将显示尚未启用的 UEBA 支持的数据源的列表。 
    >
    > 启用 UEBA 后，在连接新的数据源时，可以选择直接从“数据连接器”窗格中为 UEBA 启用它们（如果支持 UEBA）。

1. 选择“转到实体搜索”。 这会将你转到实体搜索窗格，从现在开始，当你从 Azure Sentinel 的主菜单中选择“实体行为”时，就会看到此窗格。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure Sentinel 中启用和配置用户和实体行为分析 (UEBA)。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
