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
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452792"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>在 Azure 应用服务中使用 WebJobs 运行后台任务

Azure 应用服务 Web 作业提供了在 Azure 上运行[后台任务](./webjobs-create-ieux-conceptual.md)的概念。 了解部署方式 <abbr title="（与 Web 应用、API 应用或移动应用位于同一实例中的程序或脚本。）">Web 作业</abbr> ：使用 [Azure 门户](https://portal.azure.com)上传可执行文件或脚本。 

三种支持的 WebJobs 包括：

* **持续**：立即启动，通常以无限循环方式运行。
* **计划**：从计划触发器启动
* **手动**：从手动触发器启动

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

* [详细了解 WebJobs 形式的后台任务](./webjobs-create-ieux-conceptual.md)
* [查看 WebJobs 的日志历史记录](./webjobs-create-ieux-view-log.md)

* 使用 [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 简化许多编程任务

* 了解如何[使用 Visual Studio 开发和部署 WebJobs](webjobs-dotnet-deploy-vs.md)
