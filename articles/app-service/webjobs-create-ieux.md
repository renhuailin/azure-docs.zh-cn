---
title: 使用 WebJobs 运行后台任务
description: 了解如何使用 WebJobs 在 Azure 应用服务中运行后台任务。 从各种脚本格式中进行选择，并使用 CRON 表达式运行它们。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4737f0f19acf199190df02386ecb2ece65fa571e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744465"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>在 Azure 应用服务中使用 WebJobs 运行后台任务

Azure 应用 service web 作业提供 Azure 上的 runn [后台任务](./webjobs-create-ieux-conceptual.md) 的概念。 了解如何部署 <abbr title="与 web 应用、API 应用或移动应用相同的实例中的程序或脚本。">Web 作业</abbr> 使用 [Azure 门户](https://portal.azure.com) 上载可执行文件或脚本。 

三个支持的 Web 作业包括：

* **连续**：立即启动，通常在无穷循环中运行。
* **计划**：从计划的触发器开始
* **手动**：从手动触发器开始

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> 后续步骤

* [详细了解作为 web 作业的后台任务](./webjobs-create-ieux-conceptual.md)
* [查看 Web 作业的日志历史记录](./webjobs-create-ieux-view-log.md)

* 使用 [Web 作业 SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 简化许多编程任务

* 了解如何 [通过 Visual Studio 开发和部署 Web 作业](webjobs-dotnet-deploy-vs.md)