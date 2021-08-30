---
title: 收集和传输指标 - Azure IoT Edge
description: 使用 Azure Monitor 远程监视 IoT Edge 的内置指标
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 60a01c9e3a3e8643ad7d993db34d299fdc5ef145
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014589"
---
# <a name="collect-and-transport-metrics-preview"></a>收集和传输指标（预览）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

你可以使用 Azure Monitor 和内置指标集成远程监视你的 IoT Edge。 若要在设备上启用此功能，请将指标收集器模块添加到部署中，并将其配置为收集模块指标并传输到 Azure Monitor。

## <a name="architecture"></a>体系结构

# <a name="iot-hub"></a>[IoT 中心](#tab/iothub)

[![IoT 中心的指标监视体系结构](./media/how-to-collect-and-transport-metrics/arch.png)](./media/how-to-collect-and-transport-metrics/arch.png#lightbox)

| 注意 | 说明 |
|-|-|
|  1 | 所有模块都必须使用 [Prometheus 数据模型](https://prometheus.io/docs/concepts/data_model/)发出指标。 默认情况下，[内置指标](how-to-access-built-in-metrics.md)可显示各种工作负荷，但你还可以使用自定义模块发出特定于方案的指标，以增强监视解决方案的成效。 了解如何使用[添加自定义指标](how-to-add-custom-metrics.md) 一文中的开源库来检测自定义模块。 |
|  2️ | [指标收集器模块](https://aka.ms/edgemon-metrics-collector)是 Microsoft 提供的 IoT Edge 模块，用于收集工作负荷模块指标并将其传输到设备外。 指标收集采用拉取模型。 你可以通过配置收集频率、终结点和筛选器来控制从模块传出的数据。 有关详细信息，请参阅本文后面的[指标收集器配置部分](#metrics-collector-configuration)。 |
|  3️ | 你可以通过两种方法将指标收集器模块中的指标发送到云。 方法 1：将指标发送到 Log Analytics。<sup>1</sup>你可以使用固定本机表（名为 `InsightsMetrics`）将收集到的指标引入指定的 Log Analytics 工作区。 此表的架构可与 Prometheus 指标数据模型兼容。<br><br> 此方法需要访问出站端口 443 上的工作区。 而且必须在模块配置过程中指定 Log Analytics 工作区 ID 及密钥。 若要在受限制的网络中启用，请参阅本文后面的[适用于受限网络访问方案的启用程序](#enable-in-restricted-network-access-scenarios)。
|  4️ | 每个指标条目都包含先前已在[模块配置](#metrics-collector-configuration)过程中指定的 `ResourceId`。 此关联会自动将指标链接到指定资源（例如，IoT 中心）。 因此，[特选 IoT Edge 工作簿模板](how-to-explore-curated-visualizations.md)可以通过对资源发出查询来检索指标。 <br><br> 此方法还允许多个 IoT 中心将单个 Log Analytics 工作区作为指标数据库进行安全共享。 |
|  5️ | 方法 2：将指标发送到 IoT 中心。<sup>1</sup>你可以将收集器模块配置为通过 `edgeHub` 模块将收集到的指标作为 UTF-8 编码的 JSON [设备到云消息](../iot-hub/iot-hub-devguide-messages-d2c.md)进行发送。 此方法会解除对锁定 IoT Edge 设备（此类设备只能对 IoT 中心终结点进行外部访问）的监视。 其还允许监视嵌套配置中的 IoT Edge 子设备，其中子设备只能访问其父设备。 |
|  6️ | 若要通过 IoT 中心路由指标，则需要设置一个（一次性）云工作流。 该工作流将处理来自指标收集器模块的消息，并将这些消息发送到 Log Analytics 工作区。 即使是通过此可选路径收到的指标，该工作流也能使用[特选可视化](how-to-explore-curated-visualizations.md)和[警报](how-to-create-alerts.md)功能。 如需详细了解如何设置此云工作流，请参阅[通过 IoT 中心路由指标](#route-metrics)部分。 |

<sup>1</sup> 目前，使用 *方法 1* 直接将指标从 IoT Edge 设备传输到 Log Analytics 所需的设置步骤最少，因此操作也更简单。 建议首选方法 1；除非你的特定方案要求使用方法 2，以便让 IoT Edge 设备仅与 IoT 中心通信。

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

[![IoT Central 的指标监视体系结构](./media/how-to-collect-and-transport-metrics/arch-iot-central.png)](./media/how-to-collect-and-transport-metrics/arch-iot-central.png#lightbox)

| 注意 | 说明 |
|-|-|
|  1 | 所有模块都必须使用 [Prometheus 数据模型](https://prometheus.io/docs/concepts/data_model/)发出指标。 默认情况下，[内置指标](how-to-access-built-in-metrics.md)可显示各种工作负荷，但你还可以使用自定义模块发出特定于方案的指标，以增强监视解决方案的成效。 了解如何使用[添加自定义指标](how-to-add-custom-metrics.md) 一文中的开源库来检测自定义模块。 |
|  2️ | [指标收集器模块](https://aka.ms/edgemon-metrics-collector)是 Microsoft 提供的 IoT Edge 模块，用于收集工作负荷模块指标并将其传输到设备外。 指标收集采用拉取模型。 你可以通过配置收集频率、终结点和筛选器来控制从模块传出的数据。 有关详细信息，请参阅本文后面的[指标收集器配置部分](#metrics-collector-configuration)。 |
|  3️ | 你可以通过两种方法将指标收集器模块中的指标发送到云。 方法 1：将指标发送到 Log Analytics。 使用固定本机表（名为 `InsightsMetrics`）将收集到的指标引入指定的 Log Analytics 工作区。 此表的架构可与 Prometheus 指标数据模型兼容。<br><br> 此方法需要访问出站端口 443 上的工作区。 而且必须在模块配置过程中指定 Log Analytics 工作区 ID 及密钥。 若要在受限制的网络中启用，请参阅本文后面的[适用于受限网络访问方案的启用程序](#enable-in-restricted-network-access-scenarios)。
|  4️ | 每个指标条目都包含先前已在[模块配置](#metrics-collector-configuration)过程中指定的 `ResourceId`。 此关联会自动将指标链接到指定资源（例如 IoT Central）。 因此，[特选 IoT Edge 工作簿模板](how-to-explore-curated-visualizations.md)可以通过对资源发出查询来检索指标。 <br><br> 此方法还允许多个 IoT Central 应用程序将单个 Log Analytics 工作区作为指标数据库进行安全共享。 |
|  5️ | 方法 2：将指标发送到 IoT Central。 此方法可让操作员在一个位置查看指标和设备遥测。 可以将收集器模块配置为通过 `edgeHub` 模块将收集到的指标作为 UTF-8 编码的 JSON [设备到云消息](../iot-hub/iot-hub-devguide-messages-d2c.md)进行发送。 此方法会解除对锁定 IoT Edge 设备（此类设备只能对 IoT Central 终结点进行外部访问）的监视。 其还允许监视嵌套配置中的 IoT Edge 子设备，其中子设备只能访问其父设备。 |

---

## <a name="metrics-collector-module"></a>指标收集器模块

你可以将 Microsoft 提供的指标收集器模块添加到 IoT Edge 部署，以收集模块指标并将其发送到 Azure Monitor。 模块代码为开放源代码，可从 [IoT Edge GitHub 存储库](https://github.com/Azure/iotedge/tree/release/1.1/edge-modules/azure-monitor)获取。

指标收集器模块可在 Linux X64、ARM32、ARM64 和 Windows X64（1809 版）上作为多架构 Docker 容器映像提供。 你可以从 [`mcr.microsoft.com/azureiotedge-metrics-collector`](https://aka.ms/edgemon-metrics-collector) 公开获取此指标收集器。

你还可以在 [IoT Edge 模块市场](https://aka.ms/edgemon-module-marketplace)上获取此收集器。

## <a name="metrics-collector-configuration"></a>指标收集器配置

指标收集器的所有配置都是通过使用环境变量来完成。 并且，你至少需要将下表所述的变量指定为“必需”变量。

# <a name="iot-hub"></a>[IoT 中心](#tab/iothub)

| 环境变量名称 | 说明 |
|-|-|
| `ResourceId` | 与设备通信的 IoT 中心的资源 ID。 有关详细信息，请参阅[资源 ID](#resource-id) 部分。  <br><br>  **必需** <br><br> 默认值：none |
| `UploadTarget` |  控制是直接通过 HTTPS 将指标发送到 Azure Monitor，还是将指标作为 D2C 消息发送到 IoT 中心。 有关详细信息，请参阅[上传目标](#upload-target)。 <br><br>可以是 AzureMonitor 或 IoTMessage  <br><br>  **不需要** <br><br> 默认值：AzureMonitor |
| `LogAnalyticsWorkspaceId` | [Log Analytics 工作区 ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>仅当“UploadTarget”为“AzureMonitor”时，才“必需”  <br><br>默认值：none |
| `LogAnalyticsSharedKey` | [Log Analytics 工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>仅当“UploadTarget”为“AzureMonitor”时，才“必需”    <br><br> 默认值：none |
| `ScrapeFrequencyInSecs` | 收集和传输指标的重复时间间隔（秒）。<br><br>  示例：600 <br><br>  **不需要** <br><br> 默认值：300 |
| `MetricsEndpointsCSV` | 终结点列表（以英文逗号分隔），用于从中收集 Prometheus 指标。 要从中收集指标的所有模块终结点都必须在此列表中显示。<br><br>  示例： http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics <br><br>  **不需要** <br><br> 默认值： http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics |
| `AllowedMetrics` | 要收集的指标列表，而所有其他指标都将忽略。 设置为空字符串以禁用。 有关详细信息，请参阅[允许和禁止列表](#allow-and-disallow-lists)。 <br><br>示例：metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]<br><br>  **不需要** <br><br> 默认值：empty |
| `BlockedMetrics` | 要忽略的指标列表。 重写 AllowedMetrics，以避免系统报告包含在两个列表中的同一指标。 有关详细信息，请参阅[允许和禁止列表](#allow-and-disallow-lists)。 <br><br>   示例：metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes<br><br>  **不需要**  <br><br>默认值：empty |
| `CompressForUpload` | 控制是否应在上传指标时进行压缩。 适用于所有上传目标。<br><br>  示例：true <br><br>    **不需要** <br><br>  默认值：true |
| `AzureDomain` | 指定在将指标直接引入 Log Analytics 时要使用的顶级 Azure 域。 <br><br>  示例：azure.us <br><br>    **不需要** <br><br>  默认值：azure.com |

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

| 环境变量名称 | 说明 |
|-|-|
| `ResourceId` | 与设备通信的 IoT Central 应用程序的资源 ID。 有关详细信息，请参阅[资源 ID](#resource-id) 部分。  <br><br>  **必需** <br><br> 默认值：none |
| `UploadTarget` |  控制是直接通过 HTTPS 将指标发送到 Azure Monitor，还是将指标作为 D2C 消息发送到 IoT Central。 有关详细信息，请参阅[上传目标](#upload-target)。 <br><br>可以是 AzureMonitor 或 IoTMessage  <br><br>  **不需要** <br><br> 默认值：AzureMonitor |
| `LogAnalyticsWorkspaceId` | [Log Analytics 工作区 ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>仅当“UploadTarget”为“AzureMonitor”时，才“必需”  <br><br>默认值：none |
| `LogAnalyticsSharedKey` | [Log Analytics 工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>仅当“UploadTarget”为“AzureMonitor”时，才“必需”    <br><br> 默认值：none |
| `ScrapeFrequencyInSecs` | 收集和传输指标的重复时间间隔（秒）。<br><br>  示例：600 <br><br>  **不需要** <br><br> 默认值：300 |
| `MetricsEndpointsCSV` | 终结点列表（以英文逗号分隔），用于从中收集 Prometheus 指标。 要从中收集指标的所有模块终结点都必须在此列表中显示。<br><br>  示例： http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics <br><br>  **不需要** <br><br> 默认值： http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics |
| `AllowedMetrics` | 要收集的指标列表，而所有其他指标都将忽略。 设置为空字符串以禁用。 有关详细信息，请参阅[允许和禁止列表](#allow-and-disallow-lists)。 <br><br>示例：metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]<br><br>  **不需要** <br><br> 默认值：empty |
| `BlockedMetrics` | 要忽略的指标列表。 重写 AllowedMetrics，以避免系统报告包含在两个列表中的同一指标。 有关详细信息，请参阅[允许和禁止列表](#allow-and-disallow-lists)。 <br><br>   示例：metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes<br><br>  **不需要**  <br><br>默认值：empty |
| `CompressForUpload` | 控制是否应在上传指标时进行压缩。 适用于所有上传目标。<br><br>  示例：true <br><br>    **不需要** <br><br>  默认值：true |
| `AzureDomain` | 指定在将指标直接引入 Log Analytics 时要使用的顶级 Azure 域。 <br><br>  示例：azure.us <br><br>    **不需要** <br><br>  默认值：azure.com |

若要详细了解 IoT Edge 和 IoT Central，请参阅[将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序](../iot-central/core/concepts-iot-edge.md)。

---

### <a name="resource-id"></a>资源 ID

# <a name="iot-hub"></a>[IoT 中心](#tab/iothub)

指标收集器模块需要 IoT Edge 设备所属之 IoT 中心的 Azure 资源管理器 ID。 因此，请提供此 ID 作为 ResourceID 环境变量的值。

资源 ID 采用的格式如下：

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Devices/IoTHubs/<iot hub name>
```

你可以前往 Azure 门户，在 IoT 中心的“属性”页中找到资源 ID。

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id.png" alt-text="从 IoT 中心属性中检索资源 ID。":::

你也可以使用 [az resource show](/cli/azure/resource#az_resource_show) 命令来检索资源 ID：

```azurecli-interactive
az resource show -g <resource group> -n <hub name> --resource-type "Microsoft.Devices/IoTHubs"
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

指标收集器模块需要 IoT Edge 设备所属的 IoT Central 应用程序的 Azure 资源管理器 ID。 因此，请提供此 ID 作为 ResourceID 环境变量的值。

资源 ID 采用的格式如下：

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.IoTCentral/IoTApps/<iot central app name>
```

你可以前往 Azure 门户，在 IoT Central 应用程序的“属性”页中找到资源 ID。

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id-iot-central.png" alt-text="从 IoT Central 属性中检索资源 ID。":::

你也可以使用 [az resource show](/cli/azure/resource#az_resource_show) 命令来检索资源 ID：

```azurecli-interactive
az resource show -g <resource group> -n <application name> --resource-type "Microsoft.IoTCentral/IoTApps"
```

---

### <a name="upload-target"></a>上传目标

# <a name="iot-hub"></a>[IoT 中心](#tab/iothub)

“UploadTarget”配置选项用于控制是将指标直接发送到 Azure Monitor 还是 IoT 中心。

如果将“UploadTarget”设置为“IoTMessage”，则模块指标将作为 IoT 消息发布。 这些消息将作为 UTF8 编码的 json 从终结点 `/messages/modules/<module name>/outputs/metricOutput` 发出。 其格式如下所示：

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

“UploadTarget”配置选项用于控制是将指标直接发送到 Azure Monitor 还是 IoT Central。

如果将“UploadTarget”设置为“IoTMessage”，则模块指标将作为 IoT 消息发布。 这些消息将作为 UTF8 编码的 json 从终结点 `/messages/modules/<module name>/outputs/metricOutput` 发出。 其格式如下所示：

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

---

### <a name="allow-and-disallow-lists"></a>允许和禁止列表

`AllowedMetrics` 和 `BlockedMetrics` 配置选项用于使用以空格或逗号分隔的指标选择器列表。 如果指标与列表中的一个或多个指标匹配，则该指标将与相应列表匹配，并纳入列表或从列表中排除。

指标选择器使用的格式类似于 [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) 查询语言的子集。

```query
metricToSelect{quantile=0.5,otherLabel=~Re[ge]*|x}[http://VeryNoisyModule:9001/metrics]
```

指标选择器由三部分构成：

指标名称 (`metricToSelect`)。

* 指标名称中可以使用通配符 `*`（任何字符）和 `?`（任何单字符）。 例如，`*CPU` 将匹配 `maxCPU` 和 `minCPU`，但无法匹配 `CPUMaximum`。 `???CPU` 将匹配 `maxCPU` 和 `minCPU`，但无法匹配 `maximumCPU`。
* 指标选择器必需包含此组件。

基于标签的选择器 (`{quantile=0.5,otherLabel=~Re[ge]*|x}`)。

* 花括号内可以包含多个指标值， 但这些值应以逗号进行分隔。
* 若选择器中的所有标签都存在且匹配，则将匹配指标。
* 与 PromQL 一样，允许使用以下匹配运算符。
  * `=` 匹配与所提供字符串完全相等的标签（区分大小写）。
  * `!=` 匹配与所提供字符串不完全相等的标签。
  * `=~` 匹配与所提供正则表达式对应的标签。 例如，`label=~CPU|Mem|[0-9]*`
  * `!=` 匹配不适用于所提供正则表达式的标签。
  * 正则表达式包含完全定位标记（^ 和 $ 会自动添加到每个正则表达式的开头和末尾）
  * 此组件在指标选择器中是可选的。

终结点选择器 (`[http://VeryNoisyModule:9001/metrics]`)。

* 此 URL 应与 `MetricsEndpointsCSV` 中列出的 URL 完全匹配。
* 此组件在指标选择器中是可选的。

指标必须与给定待选选择器的所有部分匹配。 其必须匹配名称，且包含所有带匹配值的相同标记，而且来自给定终结点。 例如，`mem{quantile=0.5,otherLabel=foobar}[http://VeryNoisyModule:9001/metrics]` 就无法匹配选择器 `mem{quantile=0.5,otherLabel=~foo|bar}[http://VeryNoisyModule:9001/metrics]`。 而且，你应该使用多个选择器来创建 or-like 行为，而不是 and-like 行为。

例如，若要允许来自 `module1` 模块（任意标记皆可）的指标 `mem`，但仅允许来自 `module2`（带 `agg=p99` 标记）的相同指标，可以将以下选择器添加到 `AllowedMetrics`：

```query
mem{}[http://module1:9001/metrics] mem{agg="p99"}[http://module2:9001/metrics]
```

或者，若要允许 `mem` 和 `cpu` 指标（带任意标记或终结点皆可），可以将以下内容添加到 `AllowedMetrics`：

```query
mem cpu
```

## <a name="enable-in-restricted-network-access-scenarios"></a>适用于受限网络访问方案的启用程序

如果要将指标直接发送到 Log Analytics 工作区，则允许出站访问以下 URL：

* `https://<LOG_ANALYTICS_WORKSPACE_ID>.ods.opinsights.azure.com/*`
* `https://<LOG_ANALYTICS_WORKSPACE_ID>.oms.opinsights.azure.com/*`

### <a name="proxy-considerations"></a>代理注意事项

指标收集器模块的编写平台为 .NET Core。 因此，请使用适用系统模块的相同指导来 [允许通过代理服务器进行通信](how-to-configure-proxy-support.md#configure-deployment-manifests)。

收集来自本地模块的指标时，则使用 http 协议。 通过设置 `NO_PROXY` 环境变量，排除通过代理服务器进行的本地通信。

将 `NO_PROXY` 值设置为应排除的主机名列表（以英文逗号分隔）。 使用主机名的模块名称。 例如：edgeHub,edgeAgent,myCustomModule。

## <a name="route-metrics"></a>路由指标

# <a name="iot-hub"></a>[IoT 中心](#tab/iothub)

有时需要通过 IoT 中心引入指标，而不是将指标直接发送到 Log Analytics。 例如，监视[嵌套配置中的 IoT Edge 设备](tutorial-nested-iot-edge.md)时（其中子设备只能访问其父设备的 IoT Edge 中心）。 另一个示例是，将仅具有出站网络访问权限的 IoT Edge 设备部署到 IoT 中心。

若要在此方案中启用监视，可以将指标收集器模块配置为通过 edgeHub 模块将指标作为设备到云 (D2C) 消息进行发送。 通过在收集器[配置](#metrics-collector-configuration)中将环境变量 `UploadTarget` 设置为 `IoTMessage`，即可启用此功能。

>[!TIP]
>请记住添加 edgeHub 路由，以将指标消息从收集器模块传递到 IoT 中心。 应类似于 `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream`。

此方法需进行[额外设置](how-to-collect-and-transport-metrics.md#sample-cloud-workflow)，才能将 IoT 中心接收的指标消息传递到 Log Analytics 工作区。 如未完成该设置，集成的其他部分（如[特选可视化](how-to-explore-curated-visualizations.md)和[警报](how-to-create-alerts.md)）将无法正常工作。

>[!NOTE]
>请注意使用此方法将产生的额外成本。 指标消息将占用你的 IoT 中心消息配额。 此外，你还要为 Log Analytics 引入和云工作流资源付费。

### <a name="sample-cloud-workflow"></a>云工作流示例

将指标消息从 IoT 中心传递到 Log Analytics 的云工作流可作为 [IoT Edge 日志记录和监视示例](https://github.com/Azure-Samples/iotedge-logging-and-monitoring-solution#monitoring-architecture-reference)的一部分提供。 你可以将此示例部署到现有云资源上，也可以将其用作生产部署引用。

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

有时需要通过 IoT Central 引入指标，而不是将指标直接发送到 Log Analytics。 例如，监视[嵌套配置中的 IoT Edge 设备](tutorial-nested-iot-edge.md)时（其中子设备只能访问其父设备的 IoT Edge 中心）。 另一个示例是，将仅具有出站网络访问权限的 IoT Edge 设备部署到 IoT Central。

若要在此方案中启用监视，可以将指标收集器模块配置为通过 edgeHub 模块将指标作为设备到云 (D2C) 消息进行发送。 通过在收集器[配置](#metrics-collector-configuration)中将环境变量 `UploadTarget` 设置为 `IoTMessage`，即可启用此功能。

以下示例部署清单显示了配置：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            // ...
          },
          "edgeHub": {
            // ...
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            // ...
          },
          "AzureMonitorForIotEdgeModule": {
            "settings": {
                "image": "mcr.microsoft.com/azureiotedge-metrics-collector:1.0",
                "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"json-file\",\"Config\":{\"max-size\":\"4m\",\"max-file\":\"7\"}}}}"
            },
            "type": "docker",
            "env": {
              "UploadTarget": {
                "value": "IotMessage"
              },
              "ResourceId": {
                "value": "/subscriptions/{your subscription id}/IOTC/providers/Microsoft.IoTCentral/IoTApps/{your app name}"
              },
              "MetricsEndpointsCSV": {
                "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics"
              },
              "ScrapeFrequencyInSecs": {
                "value": "30"
              },
              "AllowedMetrics": {
                "value": ""
              },
              "BlockedMetrics": {
                "value": ""
              },
              "CompressForUpload": {
                "value": "false"
              },
              "TransformForIoTCentral": {
                "value": "true"
              }
            },
            "status": "running",
            "restartPolicy": "always",
            "version": "1.0"
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "temperatureupload": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO $upstream",
          "metricupload": "FROM /messages/modules/AzureMonitorForIotEdgeModule/outputs/metricOutput INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      // ...
    },
    "AzureMonitorForIotEdgeModule": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "scrapeFrequencySecs": 30,
        "metricsFormat": "Json",
        "syncTarget": "IoTHub"
      }
    }
  }
}
```

>[!TIP]
>请记住添加 edgeHub 路由，以将指标消息从收集器模块传递到 IoT Central。 应类似于 `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream`。

在 IoT Central 应用程序中查看 IoT Edge 设备中的指标：

* 将 IoT Edge 指标标准接口作为继承接口添加到[设备模板](../iot-central/core/concepts-device-templates.md)：

    :::image type="content" source="media/how-to-collect-and-transport-metrics/add-metrics-interface.png" alt-text="添加 IoT Edge 指标标准接口。":::

* 使用接口中定义的遥测值生成监视 IoT Edge 设备时所需的[仪表板](../iot-central/core/howto-manage-dashboards.md)：

    :::image type="content" source="media/how-to-collect-and-transport-metrics/iot-edge-metrics-telemetry.png" alt-text="IoT Edge 指标用作遥测。":::

>[!NOTE]
>请注意使用此方法将产生的额外成本。 指标消息将占用你的 IoT Central 消息配额。

---

## <a name="next-steps"></a>后续步骤

了解 Azure Monitor 将会启用的[特选可视化](how-to-explore-curated-visualizations.md)类型。
