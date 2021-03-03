---
title: Azure 上的 WebJob、后台任务
description: 了解 Web 作业。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cb5e99558d6bf1a5baa21d05d45415855c61cb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744468"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Web 作业在 Azure App Service 中运行后台任务

本文介绍如何使用 [Azure 门户](https://portal.azure.com)部署 WebJobs，以便上传可执行文件或脚本。 有关如何使用 Visual Studio 开发和部署 WebJobs 的信息，请参阅[使用 Visual Studio 部署 WebJobs](webjobs-dotnet-deploy-vs.md)。

## <a name="overview"></a>概述
WebJobs 是 [Azure 应用服务](index.yml)的一项功能，它允许你在与 Web 应用、API 应用或移动应用相同的实例中运行程序或脚本。 使用 Web 作业无需支付额外的费用。

> [!IMPORTANT]
> Linux 上的应用服务尚不支持 WebJobs。

可以结合 WebJobs 使用 Azure WebJobs SDK 来简化许多编程任务。 有关详细信息，请参阅[什么是 WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)。

Azure Functions 提供了运行程序和脚本的另一种方式。 有关 WebJobs 与 Functions 之间的比较，请参阅[在 Flow、逻辑应用、Functions 与 WebJobs 之间选择](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="webjob-types"></a>Web 作业类型

下表描述了 *连续* 和 *触发* Web 作业之间的差别。


|连续  |触发  |
|---------|---------|
| 创建 Web 作业后立即启动。 若要防止作业终止，程序或脚本通常在无限循环中执行其工作。 如果作业确实终止，可将其重启。 | 仅当手动触发或按计划触发时启动。 |
| 在运行 Web 应用的所有实例上运行。 可以选择性地将 Web 作业限制为单个实例。 |在 Azure 选择用于负载均衡的单个实例上运行。|
| 支持远程调试。 | 不支持远程调试。|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>向源代码管理添加 Web 作业

如果你使用应用程序配置了源代码管理，则 Web 作业应作为源代码管理集成的一部分进行部署。 在应用程序中配置源代码管理后，将无法从 Azure 门户添加 WebJob。

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>支持的脚本或程序文件类型

支持以下文件类型：

* .cmd、.bat、.exe（使用 Windows cmd）
* .ps1（使用 PowerShell）
* .sh（使用 Bash）
* .php（使用 PHP）
* .py（使用 Python）
* .js（使用 Node.js）
* .jar（使用 Java）

## <a name="next-steps"></a>后续步骤

* 了解如何 [创建 Web 作业](./webjobs-create-ieux.md)
* 查看 Web 作业的日志历史记录] (./webjobs-create-ieux-view-log.md) 