---
title: 排查“启动/停止 VM”（预览）的问题
description: 本文介绍如何排查 Azure VM 的“启动/停止 VM”（预览）功能出现的问题。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2908f9a8383897ae0bda162e6f980e02d6c81bc1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966982"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>排查“启动/停止 VM”（预览）的常见问题

本文提供了有关排查和解决尝试安装和配置“启动/停止 VM”（预览）时可能出现的问题的信息。 有关一般信息，请参阅[启动/停止 VM 概述](overview.md)。

## <a name="general-validation-and-troubleshooting"></a>一般验证和故障排除

本部分介绍如何排查计划方案的一般问题，并帮助确定根本原因。

### <a name="azure-dashboard"></a>Azure 仪表板

首先可以查看 Azure 共享仪表板。 作为“启动/停止 VM”v2（预览）的一部分部署的 Azure 共享仪表板是一种验证 VM 上执行的每个操作状态的快速而简单的方法。 请参阅“VM 上最近尝试的操作”磁贴，查看 VM 上近期执行的所有操作。 当报表从 Application Insights 资源拉取数据时，数据在报表中的显示会出现一些延迟（大约 5 分钟）。

### <a name="logic-apps"></a>逻辑应用

根据为支持启动/停止方案而启用的逻辑应用，你可以查看其运行历史记录，帮助确定为一个或多个目标 VM 计划的启动/停止方案未成功完成的原因。 若要了解如何查看详细信息，请参阅[逻辑应用运行历史记录](../../logic-apps/monitor-logic-apps.md#review-runs-history)。

### <a name="azure-storage"></a>Azure 存储

你可以在用于“启动/停止 VM”v2（预览）的 Azure 存储帐户中查看对 VM 执行的写入 requestsstoretable 表中的操作的详细信息。 请执行以下步骤来查看这些记录。

1. 导航到 Azure 门户中的存储帐户，并在帐户的左侧窗格中选择“存储资源管理器(预览版)”。
1. 选择“表”，然后选择“requeststoretable” 。
1. 表中的每条记录均表示基于逻辑应用方案中定义的目标范围针对 Azure VM 执行的启动/停止操作。 可按任意一个记录属性（例如，TIMESTAMP、ACTION 或 TARGETTOPLEVELRESOURCENAME）来筛选结果。

### <a name="azure-functions"></a>Azure Functions

可以查看负责执行 VM 启动和停止操作的所有 Azure Functions 的最新调用详细信息。 首先，让我们查看执行流。

“计划”方案和“序列”方案的执行流都由相同的函数控制 。 有效负载架构决定执行的方案。 对于“计划”方案，执行流为 Scheduled HTTP > VirtualMachineRequestOrchestrator 队列 > VirtualMachineRequestExecutor 队列   。

将通过有效负载架构从逻辑应用中调用 Scheduled HTTP 函数。 Scheduled HTTP 函数收到请求后，会将该信息发送到 Orchestrator 队列函数，后者又为每个 VM 创建了多个队列来执行该操作 。

请执行以下步骤查看调用详细信息。

1. 在 Azure 门户中，导航到 Azure Functions。
1. 从列表中选择用于“启动/停止 VM”v2（预览）的函数应用。
1. 在左侧窗格中选择“函数”。
1. 在列表中，你将看到与每个方案关联的多个函数。 选择 Scheduled HTTP 函数。
1. 在左侧窗格中选择“监视”。
1. 选择最新的执行跟踪，查看调用详细信息和消息部分以获取详细的日志记录信息。
1. 为之前查看执行流时所述的每个函数重复相同的步骤。

若要了解有关监视 Azure Functions 的详细信息，请参阅[分析 Application Insights 中的 Azure Functions 遥测数据](../../azure-functions/analyze-telemetry-data.md)。

## <a name="next-steps"></a>后续步骤

了解有关监视 Azure Functions 和逻辑应用的详细信息：

* [监视 Azure Functions](../../azure-functions/functions-monitoring.md)。

* [如何为 Azure Functions 配置监视](../../azure-functions/configure-monitoring.md)。

* [监视逻辑应用](../../logic-apps/monitor-logic-apps.md)。

* 如果你在部署过程中遇到问题、在使用“启动/停止 VM v2 (预览版)”时遇到问题，或者有相关的问题，则可在 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) 上提交问题。 从 [Azure 支持站点](https://azure.microsoft.com/support/options/)提交 Azure 支持事件的功能不适用于此预览版本。 
