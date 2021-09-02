---
title: 有关监视应用服务数据的参考
description: 监视应用服务时所需的重要参考资料
author: msangapu-msft
ms.topic: reference
ms.author: msangapu
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 330629c958e036db382bed63b9ae0b9642e14bad
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114227871"
---
# <a name="monitoring-app-service-data-reference"></a>有关监视应用服务数据的参考

使用 Azure Monitor 监视应用服务时适合参考本文。 有关收集和分析应用服务监视数据的详细信息，请参阅[监视应用服务](monitor-app-service.md)。

## <a name="metrics"></a>指标

本部分列出了为应用服务自动收集的所有平台指标。  

|指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 应用服务计划 | [Microsoft.Web/serverfarms](../azure-monitor/essentials/metrics-supported.md#microsoftwebserverfarms)
| Web 应用 | [Microsoft.Web/sites](../azure-monitor/essentials/metrics-supported.md#microsoftwebsites) |
| 过渡槽 | [Microsoft.Web/sites/slots](../azure-monitor/essentials/metrics-supported.md#microsoftwebsitesslots) 
| 应用服务环境 | [Microsoft.Web/hostingEnvironments](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironments)
| 应用服务环境前端 | [Microsoft.Web/hostingEnvironments/multiRolePools](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)


有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。


## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

应用服务没有任何包含维度的指标。

## <a name="resource-logs"></a>资源日志

> [!NOTE]
> Azure Monitor 与应用服务的集成目前以[预览版](https://aka.ms/appsvcblog-azmon)提供。
>

本部分列出了可为应用服务收集的资源日志类型。 

| 日志类型 | Windows | Windows 容器 | Linux | Linux 容器 | 说明 |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE 和 Tomcat | 是 | 是 | 是 | 标准输出和标准错误 |
| AppServiceHTTPLogs | 是 | 是 | 是 | 是 | Web 服务器日志 |
| AppServiceEnvironmentPlatformLogs | 是 | 不适用 | 是 | 是 | 应用服务环境：缩放、配置更改和状态日志|
| AppServiceAuditLogs | 是 | 是 | 是 | 是 | 通过 FTP 和 Kudu 进行的登录活动 |
| AppServiceFileAuditLogs | 是 | 是 | TBA | TBA | 对站点内容所做的文件更改；仅适用于高级层和更高层级 |
| AppServiceAppLogs | ASP.NET | ASP.NET | Java SE 和 Tomcat 映像 <sup>1</sup> | Java SE 和 Tomcat Blessed 图像 <sup>1</sup> | 应用程序日志 |
| AppServiceIPSecAuditLogs  | 是 | 是 | 是 | 是 | 来自 IP 规则的请求 |
| AppServicePlatformLogs  | TBA | 是 | 是 | 是 | 容器操作日志 |
| AppServiceAntivirusScanAuditLogs | 是 | 是 | 是 | 是 | 使用 Microsoft Defender 的[防病毒扫描日志](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html)；仅适用于高级层 | 

<sup>1</sup> 对于 Java SE 应用，请将“$WEBSITE_AZMON_PREVIEW_ENABLED”添加到应用设置，并将其设置为“1”或“true”。

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

Azure 应用服务使用 Azure Monitor 日志中的 Kusto 表。 可以使用 Log Analytics 查询这些表。 有关 Kusto 使用的应用服务表的列表，请参阅 [Azure Monitor 日志表引用 - 应用服务表](/azure/azure-monitor/reference/tables/tables-resourcetype#app-services)。 

## <a name="activity-log"></a>活动日志

下表列出了可能在活动日志中创建的与应用服务相关的常见操作。 此列表并不详尽。

| 操作 | 说明 |
|:---|:---|
|创建或更新 Web 应用| 已创建或更新应用|
|删除 Web 应用| 已删除应用 |
|创建 Web 应用备份| 应用备份|
|获取 Web 应用发布配置文件| 下载发布配置文件 |
|发布 Web 应用| 已部署应用 |
|重新启动 Web 应用| 已重启应用|
|启动 Web 应用| 已启动应用 |
|停止 Web 应用| 已停止应用|
|交换 Web 应用槽| 已交换槽|
|获取 Web 应用槽差异| 槽差异|
|应用 Web 应用配置| 已应用配置更改|
|重置 Web 应用配置| 已重置配置更改|
|审批专用终结点连接| 已审批专用终结点连接|
|网络跟踪 Web 应用| 已启动网络跟踪|
|Newpassword Web 应用| 已创建新密码 |
|获取 Web 应用的压缩容器日志| 获取容器日志 |
|从备份 Blob 还原 Web 应用| 已从备份还原应用|

有关活动日志条目架构的详细信息，请参阅[活动日志架构](../azure-monitor/essentials/activity-log-schema.md)。 

## <a name="see-also"></a>另请参阅

- 有关如何监视 Azure 应用服务的说明，请参阅[监视 Azure 应用服务](monitor-app-service.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。