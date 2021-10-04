---
title: Azure Migrate 发现和评估中的依赖项分析
description: 介绍如何使用通过 Azure Migrate 发现和评估进行评估的依赖项分析。
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 55e8efc7b49f14f6eb4cfe399866e53df29e3e36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767055"
---
# <a name="dependency-analysis"></a>依赖项分析

本文介绍“Azure Migrate：发现和评估”中的依赖项分析。

依赖项分析可识别已发现的本地服务器之间的依赖项。 它提供以下优势：

- 更准确地将服务器收集到组中进行评估，以提升评估的置信度。
- 可标识必须一起迁移的服务器。 如果不确定哪些服务器属于要迁移到 Azure 的应用部署，这会特别有用。
- 确定服务器是否正在使用中，以及哪些服务器可解除授权而不迁移。
- 分析依赖项有助于确保不会漏掉任何内容，从而避免在迁移后出现意外的中断。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)有关依赖项分析的常见问题。

## <a name="analysis-types"></a>分析类型

有两个选项可用于部署依赖项分析

**选项** | **详细信息** | **公有云** | **Azure Government**
----  |---- | ----
**无代理** | 使用 vSphere API 轮询 VMware 上的服务器中的数据。<br/><br/> 不需要在服务器上安装代理。<br/><br/> 此选项目前仅适用于 VMware 上的服务器。 | 支持。 | 支持。
**基于代理的分析** | 使用 Azure Monitor 中的[“服务映射”解决方案](../azure-monitor/vm/service-map.md)来实现依赖项可视化和分析。<br/><br/> 需要在要分析的每个本地服务器上安装代理。 | 支持 | 不支持。

## <a name="agentless-analysis"></a>无代理分析

通过从为其启用的服务器捕获 TCP 连接数据，可进行无代理依赖项分析。 服务器上未安装任何代理。 具有相同源服务器和进程以及目标服务器、进程和端口的连接将按逻辑方式分组到依赖项中。 可以在映射视图中可视化捕获的依赖项数据，或将其导出为 CSV。 要分析的服务器上未安装任何代理。

### <a name="dependency-data"></a>依赖项数据

依赖项数据发现开始后，轮询开始：

- Azure Migrate 设备每五分钟从服务器轮询 TCP 连接数据，以收集数据。
- 使用 vSphere API 通过 vCenter Server 从来宾服务器收集数据。
- 轮询会收集以下数据：

    - 具有活动连接的进程的名称。
    - 运行具有活动连接的进程的应用程序的名称。
    - 活动连接上的目标端口。

- 收集的数据在 Azure Migrate 设备上进行处理，以推断出标识信息，并每隔 6 小时发送到 Azure Migrate


## <a name="agent-based-analysis"></a>基于代理的分析

对于基于代理的分析，“Azure Migrate：发现和评估”在 Azure Monitor 中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案。 在要分析的每个服务器上安装 [Microsoft Monitoring Agent/Log Analytics 代理](../azure-monitor/agents/agents-overview.md#log-analytics-agent)和[依赖项代理](../azure-monitor/agents/agents-overview.md#dependency-agent)。

### <a name="dependency-data"></a>依赖项数据

基于代理的分析提供以下数据：

- 源服务器名称、进程、应用程序名称。
- 目标服务器名称、进程、应用程序名称和端口。
- 收集连接数、延迟和数据传输信息并用于 Log Analytics 查询。

## <a name="compare-agentless-and-agent-based"></a>比较无代理和基于代理

表中汇总了无代理可视化和基于代理的可视化之间的差异。

**要求** | **无代理** | **基于代理**
--- | --- | ---
**支持** | 仅适用于 VMware 上的服务器。 [查看](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)支持的操作系统。 | 正式发布版 (GA) 中。
**代理** | 要分析的服务器上无需代理。 | 要分析的每个本地服务器上需要代理。
**Log Analytics** | 不需要。 | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案进行依赖项分析。<br/><br/> 将 Log Analytics 工作区与项目相关联。 工作区必须位于美国东部、东南亚或欧洲西部区域。 工作区必须位于[支持服务映射](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)的区域中。
**处理** | 捕获 TCP 连接数据。 发现后，每隔五分钟收集一次数据。 | 服务器上安装的服务映射代理收集有关 TCP 进程的数据以及每个进程的入站/出站连接。
**数据** | 源服务器名称、进程、应用程序名称。<br/><br/> 目标服务器名称、进程、应用程序名称和端口。 | 源服务器名称、进程、应用程序名称。<br/><br/> 目标服务器名称、进程、应用程序名称和端口。<br/><br/> 收集连接数、延迟和数据传输信息并用于 Log Analytics 查询。 
**可视化** | 可在一小时到 30 天内查看单个服务器的依赖项映射。 | 单个服务器的依赖项映射。<br/><br/> 一组服务器的依赖项映射。<br/><br/>  仅可在一小时内查看映射。<br/><br/> 从映射视图中添加和删除组中的服务器。
数据导出 | 过去 30 天的数据可以 CSV 格式下载。 | 数据可以通过 Log Analytics 查询。



## <a name="next-steps"></a>后续步骤

- [设置](how-to-create-group-machine-dependencies.md)基于代理的依赖项可视化。
- [试用](how-to-create-group-machine-dependencies-agentless.md) VMware 上的服务器的无代理依赖项可视化。
- 查看有关依赖项可视化的[常见问题](common-questions-discovery-assessment.md#what-is-dependency-visualization)。
