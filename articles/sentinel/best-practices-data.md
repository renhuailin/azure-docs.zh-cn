---
title: Azure Sentinel 中数据收集的最佳做法
description: 了解将数据源连接到 Azure Sentinel 时要采用的最佳做法。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 35b29ae6116e2e040f2383606fa20c5ab6316315
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253480"
---
#  <a name="data-collection-best-practices"></a>数据收集最佳做法

本节将回顾使用 Azure Sentinel 数据连接器收集数据的最佳做法。 有关详细信息，请参阅[连接数据源](connect-data-sources.md)、[Azure Sentinel 数据连接器引用](data-connectors-reference.md)和 [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)。

## <a name="prioritize-your-data-connectors"></a>设置数据连接器的优先级

如果不清楚哪种数据连接器最适合你的环境，可先启用所有[免费数据连接器](azure-sentinel-billing.md#free-data-sources)。

免费数据连接器会尽快开始显示来自 Azure Sentinel 的值，而这时你可继续规划其他数据连接器和预算。

对于[合作伙伴](data-connectors-reference.md)和[自定义](create-custom-connector.md)数据连接器，先设置 [Syslog](connect-syslog.md) 和 [CEF](connect-common-event-format.md) 连接器（优先级最高的优先），以及任何基于 Linux 的设备。

如果数据引入太昂贵、太迅速，可使用 [Azure Monitor 代理](../azure-monitor/agents/azure-monitor-agent-overview.md)停止或筛选转发的日志。

> [!TIP]
> 使用自定义数据连接器，可从当前内置功能不支持的数据源（如通过代理、Logstash 或 API）将数据引入 Azure Sentinel。 有关详细信息，请参阅[用于创建 Azure Sentinel 自定义连接器的资源](create-custom-connector.md)。
>

## <a name="filter-your-logs-before-ingestion"></a>在引入前筛选日志

建议在将数据引入 Azure Sentinel 前，筛选收集的日志，甚至是日志内容。 例如，建议筛选出与安全操作无关或对其不重要的日志，或者从日志消息中删除不需要的细节。 在处理具有许多无关的细节的 Syslog 日志、CEF 日志或基于 Windows 的日志时，筛选消息内容可能有助于降低成本。

使用以下方法之一筛选日志：

- Azure Monitor 代理。 在 Windows 和 Linux 上受支持，用于引入 [Windows 安全事件](connect-windows-security-events.md)。 通过将代理配置为只收集指定事件，筛选收集的日志。

- Logstash。 支持筛选消息内容，包括对日志消息进行更改。 有关详细信息，请参阅[使用 Logstash 进行连接](create-custom-connector.md#connect-with-logstash)。

> [!IMPORTANT]
> 使用 Logstash 筛选消息内容将导致日志被引入为自定义日志，使得任何[免费层日志](azure-sentinel-billing.md#free-data-sources)成为付费层日志。
>
> 自定义日志还需要处理成[分析规则](automate-incident-handling-with-automation-rules.md)、[威胁搜寻](hunting.md)以及[工作簿](get-visibility.md)，因为它们不会自动添加。 对于[机器学习](bring-your-own-ml.md)功能，自定义日志目前也不受支持。
>

## <a name="alternative-data-ingestion-requirements"></a>其他数据引入要求

由于各种挑战，用于数据收集的标准配置可能不适用于你的组织。 下表描述了常见的挑战或要求，以及可能的解决方案和注意事项。

> [!NOTE]
> 下面列出的许多解决方案都需要自定义数据连接器。 有关详细信息，请参阅[用于创建 Azure Sentinel 自定义连接器的资源](create-custom-connector.md)。
>

### <a name="on-premises-windows-log-collection"></a>本地 Windows 日志收集


|挑战/要求  |可能的解决方案  |注意事项  |
|---------|---------|---------|
|需要日志筛选     | 使用 Logstash <br><br>使用 Azure Functions <br><br> 使用 LogicApps <br><br> 使用自定义代码（.NET、Python）  |  尽管筛选可节省成本，并只引入所需数据，但不支持某些 Azure Sentinel 功能，如 [UEBA](identify-threats-with-entity-behavior-analytics.md)、[实体页](identify-threats-with-entity-behavior-analytics.md#entity-pages)、[机器学习](bring-your-own-ml.md)以及[融合](fusion.md)。 <br><br>配置日志筛选时，需要在资源（例如威胁搜寻查询和分析规则）中进行更新     |
|无法安装代理     |使用 [Azure Monitor 代理](connect-windows-security-events.md#connector-options)支持的 Windows 事件转发       |   使用 Windows 事件转发减少了来自 Windows 事件收集器的每秒负载均衡事件数，从 10,000 个事件降低到 500-1000 个事件。|
|服务器无法连接到 Internet     | 使用 [Log Analytics 网关](../azure-monitor/agents/gateway.md)        | 为你的代理配置代理，这需要额外的防火墙规则才能使网关正常工作。        |
|引入时需要标记和扩充     |使用 Logstash 注入 ResourceID <br><br>使用 ARM 模板将 ResourceID 注入到本地计算机中 <br><br>将资源 ID 引入到不同工作区        | Log Analytics 不支持用于自定义表的 RBAC <br><br>Azure Sentinel 不支持行级 RBAC <br><br>提示：建议对 Azure Sentinel 采用跨工作区设计和功能。        |
|需要拆分操作和安全日志     | 使用 [Microsoft Monitor 代理或 Azure Monitor 代理](connect-windows-security-events.md)多宿主功能        |  多宿主功能需要更多代理的部署开销。       |
|需要自定义日志     |   从特定文件夹路径收集文件 <br><br>使用 API 引入 <br><br>使用 PowerShell <br><br>使用 Logstash     |   筛选日志时可能会遇到问题。 <br><br>不支持自定义方法。 <br><br>自定义连接器可能需要开发人员技能。       |
| | | |

### <a name="on-premises-linux-log-collection"></a>本地 Linux 日志收集

|挑战/要求  |可能的解决方案  |注意事项  |
|---------|---------|---------|
|需要日志筛选     | 使用 Syslog-NG <br><br>使用 Rsyslog <br><br>使用代理的 FluentD 配置 <br><br> 使用 Azure Monitor 代理/Microsoft Monitoring Agent <br><br> 使用 Logstash  |  代理可能不支持某些 Linux 发行版。 <br> <br>使用 Syslog 或 FluentD 需要开发人员知识。 <br><br>有关详细信息，请参阅[连接到 Windows 服务器以收集安全事件](connect-windows-security-events.md)和[用于创建 Azure Sentinel 自定义连接器的资源](create-custom-connector.md)。      |
|无法安装代理     |  使用 Syslog 转发器，例如 (syslog-ng 或 rsyslog。       |         |
|服务器无法连接到 Internet       | 使用 [Log Analytics 网关](../azure-monitor/agents/gateway.md)        | 为你的代理配置代理，这需要额外的防火墙规则才能使网关正常工作。          |
|引入时需要标记和扩充      | 将 Logstash 用于扩充，或使用自定义方法，如 API 或 EventHubs。         | 筛选可能还需额外的工作量。    |
|需要拆分操作和安全日志     |  使用具有多宿主配置的 [Azure Monitor 代理](connect-windows-security-events.md)。       |         |
|需要自定义日志     |    使用 Microsoft Monitoring (Log Analytics) Agent 创建自定义收集器。      |      |
| | | |

### <a name="endpoint-solutions"></a>Endpoint 解决方案

如果需要从 Endpoint 解决方案（例如 EDR、其他安全事件、Sysmon 等）收集日志，请使用以下方法之一：

- MTP 连接器：用于从 Microsoft 365 Defender for Endpoint 收集日志。 此选项会产生额外的数据引入成本。
- Windows 事件转发。

> [!NOTE]
> 负载均衡减少了每秒可处理到工作区的事件。
>

### <a name="office-data"></a>Office 数据

如果需要收集 Microsoft Office 数据（不包括标准连接器数据），请使用以下解决方案之一：

|挑战/要求  |可能的解决方案  |注意事项  |
|---------|---------|---------|
|从 Teams、消息跟踪、钓鱼数据等收集原始数据     |    使用内置 [Office 365 连接器](connect-office-365.md)功能，然后为其他原始数据创建自定义连接器。  |  将事件映射到相应的 recordID 可能有难度。  |
|需要 RBAC 来拆分国家/地区、部门等     | 通过将标记添加到数据，并针对所需的每个拆分部分创建专用工作区，来自定义数据收集。|   自定义数据收集具有额外的引入成本。     |
|单个工作区内需要多个租户     |  使用 Azure LightHouse 和统一的事件视图自定义数据收集。|  自定义数据收集具有额外的引入成本。  <br><br>有关详细信息，请参阅[跨工作区和租户扩展 Azure Sentinel](extend-sentinel-across-workspaces-tenants.md)。      |
| | | |

### <a name="cloud-platform-data"></a>云平台数据

|挑战/要求  |可能的解决方案  |注意事项  |
|---------|---------|---------|
|筛选来自其他平台的日志     | 使用 Logstash <br><br>使用 Azure Monitor 代理/Microsoft Monitoring (Log Analytics) Agent       |    自定义收集具有额外的引入成本。 <br><br>与只收集安全事件相比，你可能会遇到收集所有 Windows 事件的挑战。     |
|无法使用代理     |   使用 Windows 事件转发      | 可能需要跨资源对工作进行负载均衡。        |
|服务器位于气隙网络中     | 使用 [Log Analytics 网关](../azure-monitor/agents/gateway.md)        | 为你的代理配置代理，这需要防火墙规则才能使网关正常工作。        |
|引入时的 RBAC、标记和扩充     |    通过 Logstash 或 Log Analytics API 创建自定义集合。     |  自定义表不支持 RBAC <br><br>任何表都不支持行级 RBAC。       |
|     |         |         |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [部署 Azure Sentinel 的预先部署活动和先决条件](prerequisites.md)
- [Azure Sentinel 的最佳做法](best-practices.md)
- [连接数据源](connect-data-sources.md)