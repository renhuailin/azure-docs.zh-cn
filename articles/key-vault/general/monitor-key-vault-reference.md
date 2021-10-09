---
title: 监视 Azure Key Vault 数据引用
description: 监视 Key Vault 时所需的重要参考资料
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: d3404dbd28c662734fe7319f83b69b4c45234332
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356187"
---
# <a name="monitoring-key-vault-data-reference"></a>监视 Key Vault 数据引用

有关收集和分析 Key Vault 监视数据的详细信息，请参阅[监视 Key Vault](monitor-key-vault.md)。

## <a name="metrics"></a>指标


本部分列出了为 Key Vault 自动收集的所有平台指标。  

|指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 密钥保管库 | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| 托管的 HSM | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Key Vault 指标

资源提供程序和类型：[Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults)

| 名称 | 指标 | 计价单位 | 类型 | 说明 |
|:-------|:-----|:------------|:------------------|
| 保管库整体可用性 | 可用性      | 百分比    | 平均值 | 保管库请求可用性            | 
| 保管库整体饱和度 | SaturationShoebox | 百分比 | 平均值| 已使用的保管库容量 | 
| 服务 API 命中总计 | ServiceApiHit | 计数 | 计数 | 服务 API 命中总数 |
| 总体服务 API 延迟 | ServiceApiLatency | 毫秒 | 平均值 | 服务 API 请求的总体延迟 |
| 服务 API 结果总计 | ServiceApiResult | 计数 | 计数 | 服务 API 结果总数 |

有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](/azure/azure-monitor/essentials/metrics-supported)。

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](/azure/azure-monitor/essentials/data-platform-metrics#multi-dimensional-metrics)。

Key Vault 具有以下与指标关联的维度：

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>资源日志

本部分列出了可为 Key Vault 收集的资源日志类型。

有关参考，请参阅 [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 的列表。  有关完整的详细信息，请参阅 [Azure Key Vault 日志记录](logging.md)。

|资源日志类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 密钥保管库 | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| 托管的 HSM | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与 Key Vault 相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。 

|资源类型 | 说明 |
|-------|-----|
| [密钥保管库](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

### <a name="diagnostics-tables"></a>诊断表

Key Vault 使用 [Azure 诊断](/azure/azure-monitor/reference/tables/azurediagnostics)、[Azure 活动](/azure/azure-monitor/reference/tables/azureactivity)表和 [Azure 指标](/azure/azure-monitor/reference/tables/azuremetrics)表来存储资源日志信息。 以下列与此相关。

**Azure 诊断**

| 属性 | 说明 |
|:--- |:---|
| _ResourceId | 与记录关联的资源的唯一标识符 |
| CallerIPAddress | 根据可用性执行了 UPN 声明或 SPN 声明操作的用户的 IP 地址。 |
| DurationMs | 操作持续时间，以毫秒为单位。 |
| httpStatusCode_d | 由请求返回的 HTTP 状态代码（例如，200） |
| 级别 | 事件的级别。 以下值之一：Critical、Error、Warning、Informational 和 Verbose。 |
| OperationName | 操作的名称，例如 Alert |
| properties_s |  |
| Region_s | |
| requestUri_s | 客户端请求的 URI。 |
| 资源 | |
| ResourceProvider | 报告指标的 Azure 资源的资源提供程序。 |
| ResultSignature | |
| TimeGenerated | 创建记录的日期和时间 |

## <a name="see-also"></a>另请参阅

- 有关监视 Azure Key Vault 的说明，请参阅[监视 Azure Key Vault](monitor-key-vault.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](/azure/azure-monitor/essentials/monitor-azure-resource)。
