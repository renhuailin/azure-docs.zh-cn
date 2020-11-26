---
title: 应用程序网关高流量支持
description: 本文提供了有关如何配置 Azure 应用程序网关以支持高网络流量方案的指导原则。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: a5f7569fc46d4678ca0c12299e33caa3c78df849
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182914"
---
# <a name="application-gateway-high-traffic-support"></a>应用程序网关高流量支持

>[!NOTE]
> 本文介绍了几条建议的指导原则，可帮助你设置应用程序网关，以处理可能发生的任何高流量量的额外流量。 警报阈值本质上是纯粹的建议和泛型。 用户可以根据其工作负荷和利用率预期确定警报阈值。

可以使用配置了 Web 应用程序防火墙 (WAF) 的应用程序网关，以可缩放且安全的方式管理流向 Web 应用程序的流量。

请务必根据流量和缓冲区扩展应用程序网关，以便为任何流量提供电涌或峰值，并使其在 QoS 中的影响降至最低。 以下建议可帮助你设置部署有 WAF 的应用程序网关来应对额外流量。

有关应用程序网关提供的度量值的完整列表，请查看 [指标文档](./application-gateway-metrics.md) 。 有关如何为指标设置警报的详细说明，请参阅在 Azure 门户中 [显示度量值](./application-gateway-metrics.md#metrics-visualization) 和 [Azure monitor 文档](../azure-monitor/platform/alerts-metric.md) 。

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>适用于应用程序网关 v1 SKU (Standard/WAF SKU) 

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>根据高峰 CPU 使用率设置实例计数
如果你使用的是 v1 SKU 网关，则可以将应用程序网关设置为最多32个实例进行缩放。 在过去一个月内查看应用程序网关的 CPU 利用率，查看是否有超过80% 的任何峰值，作为监视的指标。 建议你根据高峰使用情况设置实例计数，并使用10% 到20% 的额外缓冲区来计算流量峰值。

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="V1 CPU 利用率指标" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>请使用 v2 SKU 而不是 v1，因为前者具有自动缩放功能，且性能更有优势
v2 SKU 提供自动缩放功能，确保应用程序网关能够随着流量的增加而纵向扩展。 与 v1 相比，它还提供其他重要性能优势，例如，TLS 卸载性能要高出 5 倍、部署和更新时间更快、支持区域冗余等。 有关详细信息，请参阅我们的 [v2 文档](./application-gateway-autoscaling-zone-redundant.md) ，并参阅 v1 到 v2 的 [迁移文档](./migrate-v1-v2.md) ，了解如何将现有 v1 SKU 网关迁移到 v2 sku。 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>应用程序网关 v2 SKU (的自动缩放 Standard_v2/WAF_v2 SKU) 

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>将最大实例计数设置为最大可能值 (125)
 
对于应用程序网关 v2 SKU，将 "最大实例计数" 设置为 "最大可能值 125" 允许应用程序网关根据需要进行扩展。 这样，应用程序网关就能处理应用程序中可能出现的流量增大情形。 你只需为使用的容量单位 (CU) 付费。 

请确保在子网中检查子网大小和可用 IP 地址计数，并基于该数量设置最大实例数。 如果子网没有足够的空间来容纳空间，则必须在具有足够容量的相同或不同子网中重新创建网关。 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 自动缩放配置" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>基于平均计算单位使用情况设置最小实例计数

对于应用程序网关 v2 SKU，自动缩放需要6到7分钟的时间来横向扩展和预配可供接收流量的其他实例集。 在此之前，如果流量出现短暂的高峰，则现有的网关实例可能会受到严重影响，这可能会导致意外的延迟或流量损失。 

建议将最小实例计数设置为最佳级别。 例如，如果你需要50实例来处理高峰负载的流量，则最好将最小25设置为30，而不是 <10，这样，即使在出现短暂的突发流量时，应用程序网关也能处理此问题，并为自动缩放提供足够的时间来响应和生效。

检查过去一个月的计算单元指标。 计算单位指标是网关 CPU 使用率的表示形式，根据高峰用量除以10，可以设置所需的最小实例数。 请注意，1个应用程序网关实例最少可处理10个计算单元

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 计算单元指标" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>应用程序网关 v2 SKU 的手动缩放 (Standard_v2/WAF_v2) 

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>根据峰值计算单位使用情况设置实例计数 

与自动缩放不同，手动缩放时，必须根据流量要求手动设置应用程序网关的实例数。 建议你根据高峰使用情况设置实例计数，并使用10% 到20% 的额外缓冲区来计算流量峰值。 例如，如果流量在高峰需要50个实例，请将55设置为60实例，以处理可能出现的意外流量峰值。

检查过去一个月的计算单元指标。 计算单位指标是网关 CPU 使用率的表示形式，根据高峰用量除以10，你可以设置所需实例数，因为1个应用程序网关实例可以处理最少10个计算单元

## <a name="monitoring-and-alerting"></a>监视和警报

若要获得任何流量或利用率异常的通知，可以设置特定指标的警报。 有关应用程序网关提供的度量值的完整列表，请参阅 [度量值文档](./application-gateway-metrics.md) 。 有关如何为指标设置警报的详细说明，请参阅在 Azure 门户中 [显示度量值](./application-gateway-metrics.md#metrics-visualization) 和 [Azure monitor 文档](../azure-monitor/platform/alerts-metric.md) 。

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>应用程序网关 v1 SKU (Standard/WAF) 的警报

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>平均 CPU 使用率交叉80% 时发出警报

正常情况下，CPU 使用率不应经常超过 90%，因为这可能导致托管在应用程序网关后面的网站中出现延迟，并破坏客户端体验。 可以通过增加实例计数或通过移动到更大的 SKU 大小或同时执行这两种操作，来间接控制或改善 CPU 利用率。 如果 CPU 利用率指标超过80%，请设置警报。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>如果不正常的主机计数超出阈值，则发出警报

此指标表示应用程序网关无法成功探测的后端服务器数。 这将捕获应用程序网关实例无法连接到后端的问题。 如果此数字超过后端容量的20%，则发出警报。 例如 如果当前在其后端池中有30个后端服务器，则如果不正常的主机计数超过6，请设置警报。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>响应状态 (4xx，5xx) 交叉阈值时发出警报 

当应用程序网关响应状态为4xx 或5xx 时，创建警报。 由于暂时性问题，可能会出现偶然的4xx 或5xx 响应。 你应在生产中看到网关，以确定静态阈值或对警报使用动态阈值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>如果失败请求交叉阈值时发出警报 

当失败请求度量值交叉阈值时创建警报。 你应在生产中看到网关，以确定静态阈值或对警报使用动态阈值。

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>示例：在过去5分钟内为超过100个失败请求设置警报

此示例演示如何使用 Azure 门户在过去5分钟超过100的失败请求计数时设置警报。
1. 导航到应用程序网关。
2. 在左侧面板中，选择“监视”选项卡下的“指标” 。 
3. 添加 **失败请求** 的指标。
4. 单击 " **新建警报规则** " 并定义条件和操作
5. 单击 " **创建警报规则** " 以创建并启用警报

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 创建警报" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>适用于应用程序网关 v2 SKU 的警报 (Standard_v2/WAF_v2) 

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>计算单元利用率超过平均使用量的75% 时发出警报 

计算单元是应用程序网关计算使用量的度量。 检查最近1个月内的平均计算单位使用率，并在其超过75% 的情况时设置警报。 例如，如果平均使用量为10个计算单元，则在 7.5 Cu 上设置一个警报。 这会在用量不断增大时发出警报，并让你从容应对。 如果你认为这种流量将会持续，可以提高最小值，以提醒自己该流量可能会不断增大。 按照上述缩放建议进行扩展，根据需要进行扩展。

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>示例：设置在达到平均 CU 用量的 75% 时发出警报

此示例演示如何使用 Azure 门户设置在达到平均 CU 用量的 75% 时发出警报。 
1. 导航到应用程序网关。
2. 在左侧面板中，选择“监视”选项卡下的“指标” 。 
3. 为“平均当前计算单位数”添加一个指标。 
4. 如果已将最小实例计数设置为平均 CU 用量，请继续设置在使用了最小实例数的 75% 时发出警报。 例如，如果平均用量为 10 个 CU，则设置在使用了 7.5 个 CU 时发出警报。 这会在用量不断增大时发出警报，并让你从容应对。 如果你认为这种流量将会持续，可以提高最小值，以提醒自己该流量可能会不断增大。 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 计算单元警报" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> 你可以根据自己对潜在流量高峰的敏感程度，设置在 CU 用量百分比降低或提高时发出警报。

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>如果容量单位利用率超过75% 的高峰使用量，则发出警报 

容量单位根据吞吐量、计算和连接计数来表示总体网关利用率。 检查最近1个月内的最大容量单位使用率，并在其超过75% 的情况时设置警报。 例如，如果最大使用量为100容量单位，请在 75 Cu 上设置一个警报。 根据需要，按照上述两个建议进行扩展。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>如果不正常的主机计数超出阈值，则发出警报 

此指标表示应用程序网关无法成功探测的后端服务器数。 这将捕获应用程序网关实例无法连接到后端的问题。 如果此数字超过后端容量的20%，则发出警报。 例如 如果当前在其后端池中有30个后端服务器，则如果不正常的主机计数超过6，请设置警报。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>响应状态 (4xx，5xx) 交叉阈值时发出警报 

当应用程序网关响应状态为4xx 或5xx 时，创建警报。 由于暂时性问题，可能会出现偶然的4xx 或5xx 响应。 你应在生产中看到网关，以确定静态阈值或对警报使用动态阈值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>如果失败请求交叉阈值时发出警报 

当失败请求度量值交叉阈值时创建警报。 你应在生产中看到网关，以确定静态阈值或对警报使用动态阈值。

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>当后端最后一个字节响应时间跨越阈值时发出警报 

此指标表示开始建立与后端服务器的连接和接收响应正文的最后一个字节之间的时间间隔。 如果后端响应延迟比平时更长，则创建警报。 例如，设置此值，以便在后端响应延迟从常用值超过30% 时发出警报。

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>应用程序网关总时间跨越阈值时发出警报

此间隔时间是根据从应用程序网关收到 HTTP 请求的第一个字节的时间，到将最后一个响应字节发送到客户端的时间计算的。 如果后端响应延迟比平时更常见，则应该创建警报。 例如，他们可以将此设置为在整个时间延迟从常用值增加30% 时发出警报。

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>设置 WAF，通过地理筛选和机器人保护来停止攻击
如果需要在应用程序的前面使用额外的安全层，请为 WAF 功能使用应用程序网关 WAF_v2 SKU。 可将 v2 SKU 配置为仅允许从给定的国家/地区访问你的应用程序。 设置 WAF 自定义规则，以便根据地理位置明确地允许或阻止流量。 有关详细信息，请参阅 [geo 筛选自定义规则](../web-application-firewall/ag/geomatch-custom-rules.md) 以及 [如何通过 PowerShell 在应用程序网关上配置自定义规则 WAF_v2 SKU](../web-application-firewall/ag/configure-waf-custom-rules.md)。

启用机器人防护以阻止已知恶意的机器人。 这应该可以减少进入应用程序的流量。 有关详细信息，请参阅[机器人防护和设置说明](../web-application-firewall/ag/configure-waf-custom-rules.md)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在应用程序网关和 WAF 上启用诊断

通过诊断日志，你可以查看防火墙日志、性能日志和访问日志。 可在 Azure 中使用这些日志来对应用程序网关进行管理和故障排除。 有关详细信息，请参阅我们的[诊断文档](./application-gateway-diagnostics.md#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>设置 TLS 策略以进一步提高安全性
请确保使用最新的 TLS 策略版本 ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s))。 此版本强制实施 TLS 1.2 和更强的密码。 有关详细信息，请参阅[通过 PowerShell 配置 TLS 策略版本和加密套件](./application-gateway-configure-ssl-policy-powershell.md)。