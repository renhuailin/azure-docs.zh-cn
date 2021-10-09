---
title: 使用 Azure Monitor 监视数据工厂
description: 了解如何在 Azure Monitor 中通过使用来自数据工厂的信息启用诊断日志来监视 Azure 数据工厂管道。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 66f6c40ce77c50c18dfe3fca48e8f9367f29390b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093205"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>使用 Azure Monitor 监视数据工厂和发警报

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

云应用程序比较复杂，包含很多移动部件。 监视功能可以提供数据来确保应用程序始终处于正常运行状态。 监视功能还有助于避免潜在问题，或者排查以往的问题。 可以使用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性。 此外，还有助于实现本来需要手动干预的操作的自动化。

Azure Monitor 针对大多数 Azure 服务提供基本级别的基础结构指标和日志。 Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 Azure 数据工厂 (ADF) 可以在 Azure Monitor 中写入诊断日志。 有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

有关详细信息，请参阅 [Azure Monitor 概述](../azure-monitor/overview.md)。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>保留 Azure 数据工厂指标和管道运行数据

数据工厂仅将管道运行数据存储 45 天。 若要将这些数据保留更长时间，请使用 Azure Monitor。 使用 Monitor，可以将诊断日志路由到多个不同目标进行分析。

* **存储帐户**：将诊断日志保存到存储帐户进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* **事件中心**：将日志流式传输到 Azure 事件中心。 日志可用作合作伙伴服务/自定义分析解决方案（例如 Power BI）的输入。
* **Log Analytics**：使用 Log Analytics 分析日志。 在以下情况下，将数据工厂与 Azure Monitor 集成非常有用：
  * 需要针对由数据工厂发布到 Monitor 的丰富指标集编写复杂查询。 可以通过 Monitor 创建针对这些查询的自定义警报。
  * 你希望跨数据工厂进行监视。 可将来自多个数据工厂的数据路由到单个 Monitor 工作区。

还可使用与发出日志的资源不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 访问权限。

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂指标和警报](monitor-metrics-alerts.md)
- [以编程方式监视和管理管道](monitor-programmatically.md)
