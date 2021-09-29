---
title: 监视 Azure Web 应用程序防火墙的日志
description: 了解如何启用和管理 Azure Web 应用程序防火墙的日志
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20ea450789c30556a6a92091affb6631658d5c97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817955"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Azure Web 应用程序防火墙的资源日志

可以使用日志监视 Web 应用程序防火墙资源。 可从资源保存或使用性能、访问及其他数据，以便进行监视。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>诊断日志

可在 Azure 中使用不同类型的日志来对应用程序网关进行管理和故障排除。 可通过门户访问其中部分日志。 可从 Azure Blob 存储提取所有日志并在 [Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)、Excel 和 Power BI 等各种工具中查看。 可从以下列表了解有关不同类型日志的详细信息：

* **活动日志**：可使用 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)查看提交到 Azure 订阅的所有操作及其状态。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。
* **访问资源日志**：可以使用此日志查看应用程序网关访问模式并分析重要信息。 此日志包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。访问日志每 300 秒收集一次。 此日志包含每个应用程序网关实例的一条记录。 应用程序网关实例由 instanceId 属性标识。
* **性能资源日志**：可使用此日志查看应用程序网关实例的执行情况。 此日志会捕获每个实例的性能信息，包括服务的总请求数、吞吐量（以字节为单位）、失败请求计数、正常和不正常的后端实例计数。 每隔 60 秒会收集一次性能日志。 性能日志仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](../../application-gateway/application-gateway-metrics.md)。
* **防火墙资源日志**：可使用此日志查看通过应用程序网关（配置有 Web 应用程序防火墙）的检测模式或阻止模式记录的请求。

> [!NOTE]
> 日志仅适用于在 Azure 资源管理器部署模型中部署的 Azure 资源。 不能将日志用于经典部署模型中的资源。 若要更好地了解两种模型，请参阅[了解 Resource Manager 部署和经典部署](../../azure-resource-manager/management/deployment-models.md)一文。

可通过三种方式存储日志：

* **存储帐户**：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* **事件中心**：若要集成其他安全信息和事件管理 (SIEM) 工具以获取资源警报，事件中心是很好的选择。
* **Azure Monitor 日志**：Azure Monitor 日志最适合用于应用程序常规实时监视或查看趋势。

### <a name="enable-logging-through-powershell"></a>通过 PowerShell 启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 必须启用访问和性能日志记录才能开始收集通过这些日志提供的数据。 若要启用日志记录，请执行以下步骤：

1. 记下存储帐户的资源 ID，其中存储日志数据。 此值采用以下格式：/subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>。 可以使用订阅中的任何存储帐户。 可以使用 Azure 门户查找以下信息。

    ![存储帐户的门户：资源 ID](../media/web-application-firewall-logs/diagnostics1.png)

2. 记下为其启用日志记录的应用程序网关的资源 ID。 此值采用以下格式：/subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>。 可以使用门户查找以下信息。

    ![应用程序网关的门户：资源 ID](../media/web-application-firewall-logs/diagnostics2.png)

3. 使用以下 PowerShell cmdlet 启用资源日志记录：

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>活动日志不需要单独的存储帐户。 使用存储来记录访问和性能需支付服务费用。

### <a name="enable-logging-through-the-azure-portal"></a>通过 Azure 门户启用日志记录

1. 在 Azure 门户中找到资源，然后选择“诊断设置”。

   对于应用程序网关，提供 3 种日志：

   * 访问日志
   * 性能日志
   * 防火墙日志

2. 若要开始收集数据，请选择“启用诊断”。

   ![启用诊断][1]

3. “诊断设置”页提供用于资源日志的设置。 本示例使用 Log Analytics 存储日志。 也可使用事件中心和存储帐户保存资源日志。

   ![启动配置过程][2]

5. 键入设置的名称，确认设置，然后选择“保存”。

### <a name="activity-log"></a>活动日志

默认情况下，Azure 生成活动日志。 日志可在 Azure 事件日志存储中保留 90 天。 了解有关这些日志的详细信息，请参阅[查看事件和活动日志](../../azure-monitor/essentials/activity-log.md)一文。

### <a name="access-log"></a>访问日志

只有按照上述步骤在每个应用程序网关实例上启用了访问日志，才会生成该日志。 数据存储在启用日志记录时指定的存储帐户中。 应用程序网关的每次访问均以 JSON 格式记录下来，如下面 v1 示例所示：

|值  |说明  |
|---------|---------|
|instanceId     | 处理请求的应用程序网关实例。        |
|clientIP     | 请求的起始 IP。        |
|clientPort     | 请求的起始端口。       |
|httpMethod     | 请求所用的 HTTP 方法。       |
|requestUri     | 所收到请求的 URI。        |
|RequestQuery     | **Server-Routed**：请求已发送至后端池实例。</br>**X-AzureApplicationGateway-LOG-ID**：用于请求的相关 ID。 可用于排查后端服务器上的流量问题。 </br>**SERVER-STATUS**：应用程序网关接收从后端的 HTTP 响应代码。       |
|UserAgent     | HTTP 请求标头中的用户代理。        |
|httpStatus     | 从应用程序网关返回到客户端的 HTTP 状态代码。       |
|httpVersion     | 请求的 HTTP 版本。        |
|receivedBytes     | 接收的数据包的大小（以字节为单位）。        |
|sentBytes| 发送的数据包的大小（以字节为单位）。|
|timeTaken| 处理请求并发送响应所需的时长（以毫秒为单位）。 此时长按特定的时间间隔（从应用程序网关接收第一个 HTTP 请求字节到完成响应发送操作所需的时间）来计算。 必须注意，“所用时间”字段通常包括请求和响应数据包在网络上传输的时间。 |
|sslEnabled| 与后端池的通信是否使用了 TLS/SSL。 有效值为 on 和 off。|
|host| 向后端服务器发送请求时所用的主机名。 如果正在重写后端主机名，则此名称将反映该主机名。|
|originalHost| 应用程序网关从客户端接收请求时所用的主机名。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
对于应用程序网关和 WAF v2，日志显示了一些详细信息：

|值  |说明  |
|---------|---------|
|instanceId     | 处理请求的应用程序网关实例。        |
|clientIP     | 请求的起始 IP。        |
|clientPort     | 请求的起始端口。       |
|httpMethod     | 请求所用的 HTTP 方法。       |
|requestUri     | 所收到请求的 URI。        |
|UserAgent     | HTTP 请求标头中的用户代理。        |
|httpStatus     | 从应用程序网关返回到客户端的 HTTP 状态代码。       |
|httpVersion     | 请求的 HTTP 版本。        |
|receivedBytes     | 接收的数据包的大小（以字节为单位）。        |
|sentBytes| 发送的数据包的大小（以字节为单位）。|
|timeTaken| 处理请求并发送响应所需的时长（以毫秒为单位）。 此时长按特定的时间间隔（从应用程序网关接收第一个 HTTP 请求字节到完成响应发送操作所需的时间）来计算。 必须注意，“所用时间”字段通常包括请求和响应数据包在网络上传输的时间。 |
|sslEnabled| 与后端池的通信是否使用了 TLS。 有效值为 on 和 off。|
|sslCipher| 用于 TLS 通信的密码套件（如果已启用 TLS）。|
|sslProtocol| 使用的 TLS 协议（如果已启用 TLS）。|
|serverRouted| 应用程序网关将请求路由到的后端服务器。|
|serverStatus| 后端服务器的 HTTP 状态代码。|
|serverResponseLatency| 后端服务器的响应延迟。|
|host| 请求的主机标头中列出的地址。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>性能日志

只有在每个应用程序网关实例上启用了性能日志，才会生成此日志，如上述步骤所示。 数据存储在启用日志记录时指定的存储帐户中。 每隔 1 分钟生成性能日志数据。 性能日志数据仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](../../application-gateway/application-gateway-metrics.md)。 将记录以下数据：


|值  |说明  |
|---------|---------|
|instanceId     |  正在为其生成性能数据的应用程序网关实例。 对于多实例应用程序网关，一个实例对应于一行。        |
|healthyHostCount     | 后端池中运行正常的主机数。        |
|unHealthyHostCount     | 后端池中运行不正常的主机数。        |
|requestCount     | 服务的请求数。        |
|latency | 从实例到请求服务后端的请求的平均延迟（以毫秒为单位）。 |
|failedRequestCount| 失败的请求数。|
|throughput| 自最后一个日志后的平均吞吐量，以每秒字节数为单位。|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 延迟时间的计算是从接收到 HTTP 请求的第一个字节开始，到发出 HTTP 响应的最后一个字节为止。 它是应用程序网关处理时间加上后端的网络耗时，再加上后端处理请求所花费的时间之和。

### <a name="firewall-log"></a>防火墙日志

只有按照上述步骤为每个应用程序网关启用了防火墙日志，才会生成该日志。 此日志还需要在应用程序网关上配置 Web 应用程序防火墙。 数据存储在启用日志记录时指定的存储帐户中。 将记录以下数据：


|值  |说明  |
|---------|---------|
|instanceId     | 为其生成了防火墙数据的应用程序网关实例。 对于多实例应用程序网关，一个实例对应于一行。         |
|clientIp     |   请求的起始 IP。      |
|clientPort     |  请求的起始端口。       |
|requestUri     | 所收到请求的 URI。       |
|ruleSetType     | 规则集类型。 可用值为 OWASP。        |
|ruleSetVersion     | 所使用的规则集版本。 可用值为 2.2.9 和 3.0。     |
|ruleId     | 触发事件的规则 ID。        |
|message     | 触发事件的用户友好消息。 详细信息部分提供了更多详细信息。        |
|action     |  针对请求执行的操作。 可用值为“已阻止”或“已允许”（对于自定义规则），“匹配”（当规则匹配请求的一部分时）和“已检测”和“已阻止”（两者均用于强制规则，具体取决于 WAF 是处于检测模式还是防护模式）。      |
|site     | 为其生成日志的站点。 目前仅列出 Global，因为规则是全局性的。|
|详细信息     | 触发事件的详细信息。        |
|details.message     | 规则的说明。        |
|details.data     | 在请求中找到的与规则匹配的特定数据。         |
|details.file     | 包含规则的配置文件。        |
|details.line     | 配置文件中触发了事件的行号。       |
|hostname   | 应用程序网关的主机名或 IP 地址。    |
|transactionId  | 给定事务的唯一 ID，它有助于对同一请求中发生的多个违反规则的情况进行分组。   |
|policyId   | 与应用程序网关、侦听器或路径关联的防火墙策略的唯一 ID。   |
|policyScope    | 策略的位置 - 值可以是“Global”、“Listener”或“Location”。   |
|policyScopeName   | 应用策略的对象的名称。    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可使用以下任意方法查看和分析活动日志数据：

* **Azure 工具**：通过 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 门户检索活动日志中的信息。 [使用 Resource Manager 的活动操作](../../azure-monitor/essentials/activity-log.md)一文中详细介绍了每种方法的分步说明。
* **Power BI**：如果还没有 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可以免费试用。 使用 [Power BI 模板应用](/power-bi/service-template-apps-overview)，可以分析数据。

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>查看并分析访问、性能和防火墙日志

[Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)可从 Blob 存储帐户收集计数器和事件日志文件。 它含有可视化和强大的搜索功能，可用于分析日志。

还可以连接到存储帐户并检索访问和性能日志的 JSON 日志条目。 下载 JSON 文件后，可以将其转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>通过 GoAccess 分析访问日志

我们发布了一个资源管理器模板，用于安装和运行应用程序网关访问日志的常用 [GoAccess](https://goaccess.io/) 日志分析器。 GoAccess 提供了宝贵的 HTTP 流量统计信息，例如唯一访问者、请求的文件、主机、操作系统、浏览器和 HTTP 状态代码等。 有关更多详细信息，请参阅 [GitHub 的资源管理器模板文件夹中的自述文件](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/application-gateway-logviewer-goaccess)。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)可视化计数器和事件日志。
* [Visualize your Azure Activity Log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)（使用 Power BI 直观显示 Azure 活动日志）博客文章。
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)（在 Power BI 和其他组件中查看和分析 Azure 活动日志）博客文章。

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
