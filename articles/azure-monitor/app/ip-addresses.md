---
title: Azure Monitor 使用的 IP 地址
description: Application Insights 所需的服务器防火墙例外
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 7516cdc5fcb605415ef009e7b5f1d5da195ba1c9
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858360"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Azure Monitor 使用的 IP 地址

[Azure Monitor](../overview.md) 使用多个 IP 地址。 除了 Log Analytics 和 Application Insights，Azure Monitor 还包含核心平台指标和日志。 如果要监视的应用或基础结构托管在防火墙后面，可能需要知道这些 IP 地址。

> [!NOTE]
> 尽管这些地址是静态的，但我们可能随时需要更改它们。 除了需要入站防火墙规则的可用性监视和 Webhook 之外，所有 Application Insights 流量都表示出站流量。

> [!TIP]
> 如果使用 Azure 网络安全组，则可以使用 Azure [网络服务标记](../../virtual-network/service-tags-overview.md)管理访问。 如果在为混合/本地资源管理访问，则可以下载 [JSON 文件](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)形式的等效 IP 地址列表（每周更新一次）。 若要涵盖本文中的所有例外情况，需要使用服务标记 `ActionGroup`、`ApplicationInsightsAvailability` 和 `AzureMonitor`。

或者，可以通过将 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom 添加到喜欢的 RSS/ATOM 阅读器来订阅此页面作为 RSS 源，以获取有关最新更改的通知。


## <a name="outgoing-ports"></a>传出端口

需要在服务器防火墙中打开某些传出端口，允许 Application Insights SDK 和/或状态监视器将数据发送到门户：

| 目的 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 遥测 |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com<br/>*.in.applicationinsights.azure.com | | 443 |
| 实时指标流 | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>状态监视器

状态监视器配置 - 仅在进行更改时需要。

| 目的 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 配置 |`management.core.windows.net` | |`443` |
| 配置 |`management.azure.com` | |`443` |
| 配置 |`login.windows.net` | |`443` |
| 配置 |`login.microsoftonline.com` | |`443` |
| 配置 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 配置 |`auth.gfx.ms` | |`443` |
| 配置 |`login.live.com` | |`443` |
| 安装 | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>可用性测试

这是用于运行[可用性 Web 测试](./monitor-web-app-availability.md)的地址列表。 如果想要对应用运行 Web 测试，但 Web 服务器局限于为特定的客户端提供服务，则必须允许来自可用性测试服务器的传入流量。


> [!NOTE]
> 对于专用虚拟网络中无法与公共 Azure 中的可用性测试代理直接进行入站通信的资源，只能[创建并托管你自己的自定义可用性测试](availability-azure-functions.md)。

### <a name="service-tag"></a>服务标记

如果使用 Azure 网络安全组，则只需添加一个“入站端口规则”以允许来自 Application Insights 可用性测试的流量，具体方法是选择“服务标记”作为“源”，并选择“ApplicationInsightsAvailability”作为“源服务标记”。

>[!div class="mx-imgBorder"]
>![在“设置”下，选择“入站安全规则”，然后选择选项卡顶部的“添加”](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![添加入站安全规则选项卡](./media/ip-addresses/add-inbound-security-rule2.png)

为来自这些地址的传入流量打开端口 80 (http) 和 443 (https)（IP 地址按位置进行分组）：

### <a name="ip-addresses"></a>IP 地址

若要查找实际 IP 地址，以便将其添加到防火墙中允许的 IP 的列表，请下载描述 Azure IP 范围的 JSON 文件。 这些文件包含最新信息。 对于 Azure 公有云，还可以使用下表按位置查找 IP 地址范围。

下载相应的文件后，请使用你喜爱的文本编辑器打开该文件，然后搜索“ApplicationInsightsAvailability”，直接转到该文件描述可用性测试服务标记的部分。

> [!NOTE]
> 这些地址使用无类别域际路由选择 (CIDR) 表示法来列出。 这意味着，`51.144.56.112/28` 这样的条目等效于从 `51.144.56.112` 开始，并在 `51.144.56.127` 结束的 16 个 IP。

#### <a name="azure-public-cloud"></a>Azure 公有云
下载[公有云 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。

#### <a name="azure-us-government-cloud"></a>Azure 美国政府云
下载[政府云 IP 地址](https://www.microsoft.com/download/details.aspx?id=57063)。

#### <a name="azure-china-cloud"></a>Azure 中国云
下载[中国云 IP 地址](https://www.microsoft.com/download/details.aspx?id=57062)。

#### <a name="addresses-grouped-by-location-azure-public-cloud"></a>按位置分组的地址（Azure 公有云）

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```

### <a name="discovery-api"></a>发现 API
你可能还需要[以编程方式检索](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)包含服务标记及 IP 地址范围详细信息的最新列表。

## <a name="application-insights--log-analytics-apis"></a>Application Insights 和日志分析 API

| 目的 | URI |  IP | 端口 |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Azure 管道注释扩展 | aigs1.aisvc.visualstudio.com |动态|443 | 

## <a name="application-insights-analytics"></a>Application Insights Analytics

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 分析门户 | analytics.applicationinsights.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |
| 媒体 CDN | applicationanalyticsmedia.azureedge.net | 动态 | 80,443 |

注意：*.applicationinsights.io 域为 Application Insights 团队所有。

## <a name="log-analytics-portal"></a>Log Analytics 门户

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 门户 | portal.loganalytics.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |

注意：*.loganalytics.io 域由 Log Analytics 团队拥有。

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure 门户扩展

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights 扩展 | stamp2.app.insightsportal.visualstudio.com | 动态 | 80,443 |
| Application Insights 扩展 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 动态 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net<br/>js.monitor.azure.com | 动态 | 80,443 |

## <a name="action-group-webhooks"></a>操作组 Webhook

可使用 [PowerShell 命令 Get-AzNetworkServiceTag](/powershell/module/az.network/Get-AzNetworkServiceTag) 查询操作组使用的 IP 地址列表。

### <a name="action-groups-service-tag"></a>操作组服务标记
管理对源 IP 地址的更改可能会非常耗时。 使用服务标记就无需更新配置。 服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理 IP 地址，并在地址更改时自动更新服务标记，无需更新操作组的网络安全规则。

1. 在 Azure 门户中的“Azure 服务”下，搜索“网络安全组”。
2. 单击“添加”，创建一个网络安全组。

   1. 添加资源组名称，然后输入“实例详细信息”。
   1. 依次单击“查看 + 创建”、“创建”。
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="有关如何创建网络安全组的示例。"border="true":::

3. 转到“资源组”，然后单击你创建的网络安全组。

    1. 选择“入站安全规则”。
    1. 单击“添加”。
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="有关如何添加服务标记的示例。" border="true":::

4. 此时将在右侧窗格中打开一个新窗口。
    1.  选择“源”：**服务标记**
    1.  源服务标记：**ActionGroup**
    1.  单击 **添加**。
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="有关如何添加服务标记的示例。" border="true":::


## <a name="profiler"></a>探查器

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 代理 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 门户 | gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.windows.net | 动态 | 443

## <a name="snapshot-debugger"></a>快照调试器

> [!NOTE]
> 探查器和快照调试器共享同一组 IP 地址。

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 代理 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 门户 | gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.windows.net | 动态 | 443
