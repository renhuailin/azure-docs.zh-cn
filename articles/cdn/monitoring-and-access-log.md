---
title: Azure CDN 的监视、指标和原始日志
description: 本文介绍如何设置和使用 Azure CDN 监视、指标和原始日志。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 8a4b48586c564ee5d14a0768156b0477e2935ccf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575446"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Azure CDN 的实时监视、指标和访问日志
利用 Microsoft Azure CDN，可以通过以下方式监视资源，以帮助排除故障、跟踪和调试问题。 

* 原始日志提供有关 CDN 接收的每个请求的丰富信息。 原始日志不同于活动日志。 活动日志让你能够了解对 Azure 资源执行的操作。
* 指标 - CDN 上显示四个关键指标，包括字节命中率、请求计数、响应大小和总延迟时间。 此外，还提供不同的维度来细分指标。
* 警报 - 客户可以针对关键指标设置警报
* 其他指标 - 客户可以使用 Azure Log Analytics 来启用其他指标值。 另外，我们还提供了 Azure Log Analytics 下几个其他指标的查询示例。

> [!IMPORTANT]
> HTTP 原始日志功能适用于 Microsoft Azure CDN。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="configuration---azure-portal"></a>配置 - Azure 门户

为 Microsoft 配置文件中的 Azure CDN 配置原始日志： 

1. 从 Azure 门户菜单中，选择“所有资源” >>  **\<your-CDN-profile>** 。

2. 在“监视”下，选择“诊断设置” 。

3. 选择“+ 添加诊断设置”。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::
    
    > [!IMPORTANT]
    > 原始日志仅在配置文件级别提供，而聚合的 http 状态代码日志在终结点级别提供。

4. 在“诊断设置”下，在“诊断设置名称”下输入诊断设置的名称 。

5. 选择 AzureCdnAccessLog，并设置保留天数。

6. 选择“目标详细信息”。 目标选项有：
    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。
    * 流式传输到事件中心
        * 选择“订阅”、“事件中心命名空间”、“事件中心名称（可选）”和“事件中心策略名称”   。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="配置日志设置的目标。" border="true":::

7. 选择“保存”。

## <a name="configuration---azure-powershell"></a>配置 - Azure PowerShell

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) 配置原始日志的诊断设置。

保留数据使用命令的 -RetentionInDays 选项定义。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 若要在存储帐户中启用诊断日志，请输入以下命令。 将变量替换成你自己的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>为 Log Analytics 工作区启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将变量替换成你自己的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>为事件中心命名空间启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为事件中心命名空间启用诊断日志，请输入以下命令。 将变量替换成你自己的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>原始日志属性

Microsoft 服务中的 Azure CDN 当前提供原始日志。 原始日志提供单个 API 请求，其中每个条目具有以下架构： 

| properties  | 说明 |
| ------------- | ------------- |
| BackendHostname | 如果请求将被转发到后端，则此字段表示后端的主机名。 如果在为传递规则启用缓存时，请求将被重定向或转发到区域缓存，则此字段将为空。 |
| CacheStatus | 对于缓存方案，此字段定义在 POP 中的缓存命中/未命中情况 |
| ClientIp | 发出请求的客户端的 IP 地址。 如果请求中包含 X-Forwarded-For 头，则从同一个头中获取客户端 IP。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatusDetails | 请求的结果状态。 此字符串值的含义可以在状态引用表中找到。 |
| HttpVersion | 请求或连接的类型。 |
| POP | 请求着陆的边缘的短名称。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 |
| RequestUri | 已收到请求的 URI。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。  |
| RoutingRuleName | 请求匹配的传递规则的名称。 |
| RulesEngineMatchNames | 请求匹配的规则的名称。 |
| SecurityProtocol | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 |
| SentToOriginShield </br> （已弃用）* 请参阅以下部分中的弃用说明。| 如果为 true，则表示请求是从源防护缓存（而不是边缘 pop）响应的。 源防护是用于提高缓存命中率的父缓存。 |
| isReceivedFromClient | 如果为 true，则表示请求来自客户端。 如果为 false，则请求在边缘（子 POP）未命中，并从源盾牌（父 POP）进行响应。 |
| TimeTaken | 从请求的第一个字节传入 Front Door 到响应的最后一个字节传出 Front Door 的时间长度（以秒为单位）。 |
| TrackingReference | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| UserAgent | 客户端使用的浏览器类型。 |
| ErrorInfo | 此字段包含特定类型的错误，以缩小故障排除区域。 </br> 可能的值包括： </br> NoError：指示未发现任何错误。 </br> CertificateError：常规 SSL 证书错误。</br> CertificateNameCheckFailed：SSL 证书中的主机名无效或不匹配。 </br> ClientDisconnected：客户端网络连接问题导致请求失败。 </br> UnspecifiedClientError：常规客户端错误。 </br> InvalidRequest：请求无效。 此错误的原因可能是头、正文和 URL 格式不正确。 </br> DNSFailure：DNS 错误。 </br> DNSNameNotResolved：无法解析服务器名称或地址。 </br> OriginConnectionAborted：与源的连接突然断开。 </br> OriginConnectionError：常规源连接错误。 </br> OriginConnectionRefused：无法与源建立连接。 </br> OriginError：常规源错误。 </br> OriginInvalidResponse：源返回的响应无效或无法识别。 </br> OriginTimeout：超过了源请求的超时期限。 </br> ResponseHeaderTooBig：源返回的响应头过大。 </br> RestrictedIP：请求由于 IP 受限而被阻止。 </br> SSLHandshakeError：由于 SSL 握手失败，无法与源建立连接。 </br> UnspecifiedError：发生的错误与表中任何错误都不相符。 |
| TimeToFirstByte | 从 Microsoft CDN 接收到请求到第一个字节发送到客户端的时间长度（以毫秒为单位）。 仅从 Microsoft 端测量该时间。 不测量客户端数据。 |
> [!NOTE]
> 可以通过运行查询来查看 Log Analytics 配置文件下的日志。 查询示例如下所示：
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>发送到源盾牌弃用
原始日志属性 isSentToOriginShield 已弃用，并已替换为新的字段 isReceivedFromClient。 如果正在使用弃用的字段，请改为使用新字段。 

原始日志包括从 CDN 边缘（子 POP）和源盾牌生成的日志。 源盾牌是指位于全球各地的战略性父节点。 这些节点与源服务器通信，可减少源上的流量负载。 

对于每个进入源盾牌的请求，都有 2 个日志项：

* 一个传至边缘节点
* 一个传至源盾牌。 

要区分边缘节点与源盾牌的流出或响应，可以使用字段 isReceivedFromClient 来获取正确数据。 

如果值为 false，则表示该请求是从源盾牌到边缘节点的响应。 这种方法用来比较原始日志与账单数据非常有效。 从源盾牌向边缘节点流出不会产生费用。 从边缘节点向客户端流出却会产生费用。 

**Kusto 查询示例，不包括在 Log Analytics 的源盾牌上生成的日志。**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> 2020 年 2 月 25 日之后创建或更新的任何配置文件可自动使用 HTTP 原始日志功能。 对于之前创建的 CDN 配置文件，应在设置日志记录后更新 CDN 终结点。 例如，可以导航到 CDN 终结点下的地区筛选，并阻止任何与工作负荷无关的国家/地区，并按保存。


## <a name="metrics"></a>指标
Microsoft Azure CDN 与 Azure Monitor 集成在一起，并发布四个 CDN 指标来帮助跟踪、排查和调试问题。 

图表中显示了这些指标，可通过 PowerShell、CLI 和 API 访问它们。 CDN 指标不收取费用。

Microsoft Azure CDN 每隔 60 秒测量和发送一次指标。 可能需要 3 分钟，这些指标才会显示在门户中。 

有关详细信息，请参阅 [Azure Monitor 指标](../azure-monitor/essentials/data-platform-metrics.md)。

**Microsoft Azure CDN 支持的指标**

| 指标  | 说明 | 维度 |
| ------------- | ------------- | ------------- |
| 字节命中率* | 从 CDN 缓存的流出量占总流出量的百分比。 | 端点 |
| RequestCount | 由 CDN 为之服务的客户端请求数。 | 端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。 |
| ResponseSize | 以响应形式从 CDN 边缘发送到客户端的字节数。 |端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。 |
| TotalLatency | 从 CDN 接收到客户端请求到最后一个响应字节从 CDN 发送到客户端的总时间。 |端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。 |

*字节命中率 =（从边缘的流出量 - 从源点的流出量）/从边缘的流出量

字节命中率计算中不包含的方案：

* 通过规则引擎或查询字符串缓存行为，显式配置无缓存。
* 显式配置了包含无存储或专用缓存的缓存控制指令。

### <a name="metrics-configuration"></a>指标配置

1. 从 Azure 门户菜单中，选择“所有资源” >>  **\<your-CDN-profile>** 。

2. 在“监视”下，选择“指标”： 

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="CDN 配置文件的指标。" border="true":::

3. 选择“添加指标”，然后选择要添加的指标：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="添加并选择 CDN 配置文件的指标。" border="true":::

4. 选择“添加筛选器”以添加筛选器：
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="对指标应用筛选器。" border="true":::

5. 选择“应用拆分”可按不同维度查看趋势：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="对指标应用拆分。" border="true":::

6. 选择“新建图表”以添加新图表：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="将新图表添加到指标视图。" border="true":::

### <a name="alerts"></a>警报

通过选择“监视” >> “警报”，可在 Microsoft CDN 上设置警报。

对“指标”部分列出的指标选择“新建警报规则”：

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="为 CDN 终结点配置警报。" border="true":::

警报将根据 Azure Monitor 进行收费。 有关警报的详细信息，请参阅 [Azure Monitor 警报](../azure-monitor/alerts/alerts-overview.md)。

### <a name="additional-metrics"></a>其他指标
用户可以使用 Azure Log Analytics 和原始日志启用其他指标，但会产生额外的费用。

1. 请按照上述步骤启用诊断，以将原始日志发送到 log analytics。

2. 选择创建的 Log Analytics 工作区：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="选择 Log Analytics 工作区" border="true":::   

3. 在 log analytics 工作区中，选择“常规” 下的“日志”。  然后，选择“开始使用”：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log analytics 资源工作区。" border="true":::   
 
4. 选择“CDN 配置文件”。  选择一个示例查询运行或关闭示例屏幕来输入自定义查询：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="示例查询屏幕。" border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="执行查询。" border="true":::   

4. 要按图表查看数据，请选择“图表”。  选择“固定到仪表板”，可将图表固定到 Azure 仪表板：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="将图表固定到仪表板。" border="true"::: 

## <a name="next-steps"></a>后续步骤
本文介绍了如何为 Microsoft CDN 服务启用 HTTP 原始日志。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [分析](cdn-log-analysis.md) Azure CDN 使用模式。

* 详细了解 [Azure Monitor](../azure-monitor/overview.md)。

* 配置 [Azure Monitor 中的 Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)。