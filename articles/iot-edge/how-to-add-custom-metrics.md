---
title: 如何添加自定义指标 - Azure IoT Edge
description: 使用自定义模块中特定于场景的指标来增强内置指标
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8e9c5b74b19af00228f03b26450b987d87283376
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015488"
---
# <a name="add-custom-metrics-preview"></a>添加自定义指标（预览）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

除了系统模块提供的内置指标之外，还从 IoT Edge 模块收集自定义指标。 通过[内置指标](how-to-access-built-in-metrics.md)，可以基本了解部署运行状况。 但若要全面了解运行状况，可能需要来自自定义模块的额外信息。 可以使用相应的 [Prometheus 客户端库](https://prometheus.io/docs/instrumenting/clientlibs/)将自定义模块集成到监视解决方案中，从而发出指标。 此额外信息可以实现专门针对你的要求的新视图或警报。

## <a name="sample-modules-repository"></a>示例模块存储库

请参阅 [azure-samples 存储库](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics)，了解用于发出指标的自定义模块示例。 即使你选择的语言的示例尚未提供，一般方法也可能会对你有所帮助。

## <a name="naming-conventions"></a>命名约定

如需一般性指导，请参阅 Prometheus 文档中的[最佳做法](https://prometheus.io/docs/practices/naming/)。 以下其他建议可能对 IoT Edge 场景有所帮助。

* 在指标名称的开头包含模块名称，以确定哪个模块发出了指标。

* 在每个指标中以标签（也称为标记/维度）形式添加 IoT 中心名称或 IoT Central 应用程序名称、IoT Edge 设备 ID 和模块 ID。/ 此信息可用作 IoT Edge 代理启动的每个模块的环境变量。 示例存储库中的示例[演示了](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49)该方法。 如果没有此上下文，则不可能将给定的指标值与特定设备相关联。

* 在标签中包含实例 ID。 实例 ID 可以是任何唯一 ID，例如在模块启动过程中生成的 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 在后端处理模块的指标时，实例 ID 信息可以帮助协调模块重启。

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>配置指标收集器以收集自定义指标

自定义模块发出指标后，下一步是配置 [metrics-collector 模块](how-to-collect-and-transport-metrics.md#metrics-collector-module)以收集和传输自定义指标。

必须更新环境变量 `MetricsEndpointsCSV` 以包含自定义模块的指标终结点的 URL。 更新环境变量时，请务必包含系统模块终结点，如[指标收集器配置](how-to-collect-and-transport-metrics.md#metrics-collector-configuration)示例中所示。

>[!NOTE]
>默认情况下，自定义模块的指标终结点如果要允许指标收集器访问它，不需要映射到主机端口。 除非明确替换，否则在 Linux 上，这两个模块都在名为 azure-iot-edg 的[用户定义的 Docker 桥接网络](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)上启动。
>
>用户定义的 Docker 网络包含一个默认的 DNS 解析程序，支持使用模块（容器）名称进行模块间通信。 例如，如果名为 module1 的自定义模块在路径 /metrics 下的 http 端口 9600 上发出指标，则收集器应配置为从终结点 http://module1:9600/metrics 收集   。

在 IoT Edge 设备上运行以下命令以测试路径 /metrics 下的 http 端口 9600 上的自定义模块发出的指标是否可访问 ：

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>添加自定义可视化效果

在 Log Analytics 中收到自定义指标后，可以创建自定义可视化效果和警报。 可以扩充监视工作簿以添加查询支持的可视化效果。

每个指标都与 IoT 中心或 IoT Central 应用程序的资源 ID 相关联。 因此，可以检查自定义指标是否从关联的 IoT 中心或 IoT Central 应用程序的“日志”页（而不是支持的 Log Analytics 工作区）正确引入。 使用这个基本的 KQL 查询来验证：

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

确认引入后，可以创建新工作簿或扩充现有工作簿。 使用[工作簿文档](../azure-monitor/visualize/workbooks-overview.md)和精选 [IoT Edge 工作簿](how-to-explore-curated-visualizations.md)中的查询作为指导。

如果对结果感到满意，可以与你的团队[共享工作簿](../azure-monitor/visualize/workbooks-access-control.md)或[以编程方式将工作簿部署](../azure-monitor/visualize/workbooks-automate.md)为组织资源部署的一部分。

## <a name="next-steps"></a>后续步骤

浏览[精选工作簿](how-to-explore-curated-visualizations.md)的其他指标可视化选项。
