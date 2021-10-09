---
title: Azure Monitor 中的代理运行状况解决方案 | Microsoft Docs
description: 了解如何使用此解决方案来监视代理的运行状况，这些代理直接向 Log Analytics 或 System Center Operations Manager 报告。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663433"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Azure Monitor 中的代理运行状况解决方案
Azure 中的代理运行状况解决方案可帮助你了解哪些代理无响应且正在提交操作数据。 这包括所有直接向 Azure Monitor 中的 Log Analytics 工作区报告或者向连接到 Azure Monitor 的 System Center Operations Manager 管理组报告的代理。

使用代理运行状况解决方案还可以：

* 跟踪所部署代理的数目及其地理分布。
* 执行其他查询，以便持续了解部署在 Azure、其他云环境或本地的代理的分布。    

## <a name="prerequisites"></a>先决条件
在部署此解决方案之前，请确认已安装受支持的 [Windows 代理](../agents/agent-windows.md)，此类代理向 Log Analytics 工作区报告或向与工作区集成的 [Operations Manager 管理组](../agents/om-agents.md)报告。

## <a name="management-packs"></a>管理包
如果 Operations Manager 管理组已连接到 Log Analytics 工作区，则会在 Operations Manager 中安装以下管理包。 在添加此解决方案后，还会在直接连接的 Windows 计算机上安装这些管理包。

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer)。  

这些管理包不需进行配置或管理。 有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](../agents/om-agents.md)。

## <a name="configuration"></a>配置
执行[添加解决方案](solutions.md)中所述的过程，将代理运行状况解决方案添加到 Log Analytics 工作区。 无需进一步配置。

## <a name="supported-agents"></a>支持的代理
下表介绍了此解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 | 是 | 检测信号事件从直接的 Windows 代理收集。|
| System Center Operations Manager 管理组 | 是 | 每 60 秒从向管理组报告的代理收集一次检测信号事件，然后将其转发到 Azure Monitor。 从 Operations Manager 代理到 Azure Monitor 的直接连接不是必需的。 检测信号事件数据从管理组转发到 Log Analytics 工作区。|

## <a name="using-the-solution"></a>使用解决方案
向 Log Analytics 工作区添加解决方案时，“代理运行状况”磁贴将添加到仪表板。 此磁贴显示过去 24 小时内的总代理数以及不响应的代理数。

![显示仪表板上的“代理运行状况”磁贴的屏幕截图。](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

选择“代理运行状况”磁贴打开“代理运行状况”仪表板 。  仪表板包含下表中的列。 每个列按照指定时间范围内符合该列条件的计数列出了前 10 个事件。 可以通过选择每一列下方的“全部查看”或选择列标题来运行提供整个列表的日志搜索。

| 列 | 说明 |
|--------|-------------|
| 某个时段的代理计数 | 在七天时段内 Linux 和 Windows 代理的代理计数趋势|
| 无响应代理的计数 | 在过去 24 小时内未发送检测信号的代理的列表|
| 按 OS 类型列出的分布情况 | 对环境中存在的 Windows 代理和 Linux 代理进行数目上的细分|
| 按代理版本列出的分布情况 | 对安装在环境中的代理版本进行细分，并对每个版本计数|
| 按代理类别列出的分布情况 | 对发送检测信号事件的类别的代理进行细分：直接代理、Operations Manager 代理或 Operations Manager 管理服务器|
| 按管理组列出的分布情况 | 对环境中的 Operations Manager 管理组进行细分|
| 代理的地理位置 | 对代理所在的国家/地区进行细分，并对安装在每个国家/地区的代理进行总计|
| 已安装的网关计数 | 已安装 Log Analytics 网关的服务器数，以及这些服务器的列表|

![显示代理运行状况解决方案仪表板示例的屏幕截图。](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor 日志记录
该解决方案在 Log Analytics 工作区中创建一种类型的记录：检测信号。 检测信号记录具有下表中列出的属性。  

| 属性 | 说明 |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`、`SCOM Agent` 或 `SCOM Management Server`|
| `Computer` | 计算机名称|
| `OSType` | Windows 或 Linux 操作系统|
| `OSMajorVersion` | 操作系统主版本|
| `OSMinorVersion` | 操作系统次版本|
| `Version` | Log Analytics 代理或 Operations Manager 代理版本|
| `SCAgentChannel` | `Direct` 和/或 `SCManagementServer`|
| `IsGatewayInstalled` | 如果 Log Analytics 网关已安装，则为 `true`，否则为 `false`|
| `ComputerIP` | Azure 虚拟机的公共 IP 地址（如果有）；使用专用 IP 的虚拟机的 Azure SNAT 地址（而不是专用 IP 地址） |
| `ComputerPrivateIPs` | 计算机的专用 IP 列表 |
| `RemoteIPCountry` | 计算机部署到的地理位置|
| `ManagementGroupName` | Operations Manager 管理组的名称|
| `SourceComputerId` | 计算机的唯一 ID|
| `RemoteIPLongitude` | 计算机所在地理位置的经度|
| `RemoteIPLatitude` | 计算机所在地理位置的纬度|

向 Operations Manager 管理服务器报告的每个代理将发送两个检测信号。 `SCAgentChannel` 属性的值将包含 `Direct` 和 `SCManagementServer`，具体取决于你在订阅中启用了哪些数据源和监视解决方案。 

如前所述，来自解决方案的数据将按以下方式之一发送：

* 直接从 Operations Manager 管理服务器发送到 Azure Monitor
* 直接从代理发送到 Azure Monitor，原因与代理上收集的数据量有关

对于具有 `SCManagementServer` 值的检测信号事件，`ComputerIP` 值为管理服务器的 IP 地址，因为数据实际上是由管理服务器上传的。 对于 `SCAgentChannel` 设置为 `Direct` 的检测信号，该值为代理的公共 IP 地址。  

## <a name="sample-log-searches"></a>示例日志搜索
下表提供了解决方案收集的记录的示例日志搜索。

| 查询 | 说明 |
|:---|:---|
| Heartbeat &#124; distinct Computer |代理总数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |过去 24 小时内无响应代理的计数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |过去 15 分钟内无响应代理的计数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |过去 24 小时内处于联机状态的计算机数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |在过去 24 小时内的最近 30 分钟处于脱机状态的代理总数 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |在某个时段内按 OS 类型显示的代理数趋势|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |按 OS 类型列出的分布情况 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |按代理版本列出的分布情况 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |按代理类别列出的分布情况 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 按管理组列出的分布情况 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |代理的地理位置 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |已安装的 Log Analytics 网关数 |

## <a name="next-steps"></a>后续步骤

* 了解如何[从 Azure Monitor 中的日志查询生成警报](../alerts/alerts-overview.md)。 

