---
title: 将 Google Workspace (G Suite) 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Google 工作区 (G Suite) 数据连接器将 Google Workspace 活动事件引入 Azure Sentinel。 在工作簿中查看 Google 工作区数据，创建警报，并改进调查。
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744376"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>将 Google 工作区连接到 Azure Sentinel

> [!IMPORTANT]
> Google Workspace 连接器当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

[Google 工作区 (以前的 G Suite) ](https://workspace.google.com/)数据连接器提供通过 REST API 将 Google Workspace 活动事件引入 Azure Sentinel 的功能。 连接器在 SOC 中提供这些 [事件](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) 的可见性，这有助于你检查潜在的安全风险，分析你的团队的协作使用情况、诊断配置问题、跟踪何人登录时间、分析管理员活动、了解用户创建和共享内容的方式，以及查看组织中的更多事件。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

若要收集 Google 工作区数据，你必须具有以下权限和凭据：

- Azure Sentinel 工作区的读取和写入权限。

- 对工作区的共享密钥的读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

- Azure Functions 的 "读取" 和 "写入" 权限，以创建 Function App。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- REST API 需要 **GooglePickleString** 凭据。 [详细了解 Google REST API](https://developers.google.com/admin-sdk/reports/v1/reference/activities)。 [了解如何获取凭据](https://developers.google.com/admin-sdk/reports/v1/quickstart/python)。

## <a name="configure-and-connect-google-workspace"></a>配置并连接 Google 工作区

Google 工作区可以使用 Azure Function App 将日志直接集成到 Azure Sentinel。

> [!NOTE]
> 此连接器使用 Azure Functions 连接到 Google 报表 API，以将活动事件拉入 Azure Sentinel。 这可能会导致额外的数据引入成本。 有关详细信息，请查看 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/) 页。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器**"。 

1. 从 "连接器库" 中选择 " **Google Workspace (G Suite)  (预览)** ，然后选择" **打开连接器 "页面**。

1. 按照 "连接器" 页的 " **配置** " 部分中所述的步骤进行操作。

## <a name="validate-connectivity-and-find-your-data"></a>验证连接和查找数据

可能需要长达20分钟的时间，才能在 Azure Sentinel 中看到引入数据。

成功建立连接后，数据将显示在 " **日志**" 中的 " **自定义日志** " 部分下，如下表所示：
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 [按照以下步骤](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) 创建 **GWorkspaceActivityReports** Kusto 函数别名。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Google Workspace 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
