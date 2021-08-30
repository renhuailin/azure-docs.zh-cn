---
title: 监视故障排除和常见问题解答 - Azure IoT Edge
description: 排查 Azure Monitor 集成的问题和常见问题解答
author: veyalla
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 9d3e89ee74dd1f0274ad742cae4a9706f54b7780
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015391"
---
# <a name="faq-and-troubleshooting"></a>常见问题解答和故障排除

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>收集器模块无法从内置终结点收集指标

### <a name="check-if-modules-are-on-the-same-docker-network"></a>检查模块是否位于同一 Docker 网络上

指标收集器模块依赖于 Docker 的嵌入式 DNS 解析程序来访问用户定义的网络。 DNS 解析程序为包含模块名称的指标终结点提供 IP 地址。 例如， http://edgeHub:9600/metrics。

当模块不在同一网络命名空间中运行时，此机制将失败。 例如，某些方案需要在主机网络上运行模块。 如果指标收集器模块位于其他网络上，在这种情况下收集会失败。

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>验证“httpSettings__enabled”环境变量是否未设置为“false”

由 IoT Edge 系统模块公开的内置指标使用 HTTP 协议。 如果通过 Edge 中心或 Edge 代理模块上的环境变量设置显式禁用 HTTP，那么即使在模块网络中，它们也不可用。

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>如果使用 HTTP 代理服务器，请设置“NO_PROXY”环境变量

有关详细信息，请参阅[代理注意事项](how-to-collect-and-transport-metrics.md#proxy-considerations)。

### <a name="update-moby-engine"></a>更新 Moby 引擎

在 Linux 主机上，确保使用的是最新版本的容器引擎。 建议按照[安装说明](how-to-install-iot-edge.md#install-a-container-engine)更新到最新版本。

## <a name="how-do-i-collect-logs-along-with-metrics"></a>如何收集日志和指标？

可以使用[内置日志拉取功能](how-to-retrieve-iot-edge-logs.md)。 有关使用内置日志检索功能的示例解决方案，请参阅 [https://aka.ms/iot-elms](https://aka.ms/iot-elms)。

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>为什么在 Azure 门户的“指标”页中看不到设备指标？

Azure Monitor 的[原生指标](../azure-monitor/essentials/data-platform-metrics.md)技术尚不直接支持 Prometheus 数据格式。 基于日志的指标目前更适合用于 IoT Edge 指标，因为它：

* 通过标准 InsightsMetrics 表对 Prometheus 指标格式提供本机支持。
* 通过 [KQL](/azure/data-explorer/kusto/query/) 进行高级数据处理，实现可视化效果和警报。

使用 Log Analytics 作为指标数据库是指标出现在 Azure 门户中的“日志”页而不是“指标”页中的原因。

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>如何在分层部署中配置指标收集器？

指标收集器没有任何服务发现功能。 建议在基本或较低部署层包含模块。 在模块的配置中包括可能部署模块的所有指标终结点。 如果一个模块没有出现在最终部署中，但其终结点显示在收集列表中，则收集器将尝试收集、失败并继续。

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>如何使用自定义指标来增强监视解决方案？

请参阅[自定义指标](how-to-add-custom-metrics.md)一文。

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>如何判断特定指标所属的设备？

在指标标签中对设备信息进行编码。 有关详细信息，请参阅[命名约定](how-to-add-custom-metrics.md#naming-conventions)。

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>如何创建跨多个 IoT 中心的设备的警报规则？

[创建警报规则](how-to-create-alerts.md#create-an-alert-rule)时，可以[更改范围](how-to-create-alerts.md#select-alert-rule-scope)，改为使用资源组或订阅。 然后，警报规则将应用于该范围内的所有 IoT 中心。

## <a name="alerts-arent-firing-when-they-should"></a>警报在应触发时没有触发

创建警报规则时，通过检查预览图来验证警报逻辑是否将触发。

如果无法定位问题，请为 Log Analytics 服务创建一个[技术支持事件](https://azure.microsoft.com/support/create-ticket/)。

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>我的设备未显示在监视工作簿中

工作簿依赖于使用 ResourceId 链接到正确的 IoT 中心或 IoT Central 应用程序的设备指标。 确认指标收集器[配置了](how-to-collect-and-transport-metrics.md#metrics-collector-configuration)正确的 ResourceId。

使用指标收集器模块日志，确认设备在所选时间范围内发送了指标。

请注意，在指标显示之前，可能会存在几分钟的引入延迟。

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>我发现了 bug，或者对工作簿中显示的指标有疑问

在 [Azure IoT Edge GitHub 存储库](https://github.com/azure/iotedge/issues)上提交问题，并在标题中包含“[monitor-workbook]”。

[GitHub 上公开提供](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub)了工作簿的模板。 欢迎使用改进或修复的拉取请求！

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>我在公共模板中看不到工作簿

确保你查看的是门户中“IoT 中心”或“IoT Central 应用程序”页中的“工作簿”页，而不是 Log Analytics 工作区中的“工作簿”页。

如果仍看不到工作簿，请尝试使用预生产 Azure 门户环境：[`https://ms.portal.azure.com`](https://ms.portal.azure.com)。 有时，工作簿更新需要更长的时间才能显示在生产环境中，但在预生产环境中可立即显示。

:::zone-end