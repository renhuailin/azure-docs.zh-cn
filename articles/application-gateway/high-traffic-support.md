---
title: 应用程序网关高流量支持
description: 本文提供了有关如何配置 Azure 应用程序网关以支持高网络流量方案的指导原则。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586110"
---
# <a name="application-gateway-high-traffic-support"></a>应用程序网关高流量支持

>[!NOTE]
> 本文介绍了一些建议的准则，以帮助你设置应用程序网关，从而为可能产生的任何高流量处理额外流量。 警报阈值纯粹是建议，本质上是一般性的。 用户可以根据其工作负荷和使用率预期来确定警报阈值。

可以使用配置了 Web 应用程序防火墙 (WAF) 的应用程序网关，以可缩放且安全的方式管理流向 Web 应用程序的流量。

请务必根据流量并提供少量缓冲来缩放应用程序网关，以便应对流量激增或峰值情况，最大程度地降低这些情况在 QoS 中的可能影响。 以下建议可帮助你设置部署有 WAF 的应用程序网关来应对额外流量。

有关应用程序网关提供的指标的完整列表，请查看[指标文档](./application-gateway-metrics.md)。 请查看 Azure 门户中的[将指标可视化](./application-gateway-metrics.md#metrics-visualization)和 [Azure Monitor 文档](../azure-monitor/alerts/alerts-metric.md)，了解如何为指标设置警报。

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>针对应用程序网关 v1 SKU (Standard/WAF SKU) 的缩放

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>根据高峰 CPU 使用情况设置实例计数
如果你使用的是 v1 SKU 网关，则可将应用程序网关设置为最多 32 个实例，以便进行缩放。 请检查过去一个月内应用程序网关的 CPU 使用率是否有超过 80% 的峰值情况，该使用率是作为监视指标提供的。 建议你根据高峰使用情况并提供 10% 到 20% 的用于应对任何流量峰值的额外缓冲来设置实例计数。

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="V1 CPU 使用率指标" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>请使用 v2 SKU 而不是 v1，因为前者具有自动缩放功能，且性能更有优势
v2 SKU 提供自动缩放功能，确保应用程序网关能够随着流量的增加而纵向扩展。 与 v1 相比，它还提供其他重要性能优势，例如，TLS 卸载性能要高出 5 倍、部署和更新时间更快、支持区域冗余等。 有关详细信息，请参阅我们的 [v2 文档](./application-gateway-autoscaling-zone-redundant.md)。另请参阅我们的 v1 到 v2 [迁移文档](./migrate-v1-v2.md)，了解如何将现有的 v1 SKU 网关迁移到 v2 SKU。 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>针对应用程序网关 v2 SKU (Standard_v2/WAF_v2 SKU) 的自动缩放

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>将最大实例计数设置为最大可能值 (125)
 
对于应用程序网关 v2 SKU，将最大实例计数设置为最大可能值 125 可使应用程序网关按需横向扩展。 这样，应用程序网关就能处理应用程序中可能出现的流量增大情形。 你只需为使用的容量单位 (CU) 付费。 

请确保检查子网大小和子网中可用的 IP 地址计数，并基于该检查结果设置最大实例计数。 如果子网没有足够的空间来容纳，则必须在具有足够容量的相同或不同子网中重新创建网关。 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 自动缩放配置" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>根据平均计算单位用量设置最小实例计数

对于应用程序网关 v2 SKU，自动缩放需要 6 到 7 分钟的时间来横向扩展并预配已做好接收流量准备的其他实例集。 在该时间之前，如果流量出现短暂高峰，则现有的网关实例可能会受到压力，这可能会导致意外的延迟或流量损失。 

建议将最小实例计数设置为最佳级别。 例如，如果你需要 50 个实例来处理负载高峰期的流量，则最好是将最小实例计数设置为 25 到 30，而不是设置为 <10，这样，即使出现流量短期突发性增加的情况，应用程序网关也能处理它，并提供足够的时间以便自动缩放功能响应和生效。

检查过去一个月的计算单位指标。 计算单位指标是网关的 CPU 使用率的表示形式。你可以根据高峰用量除以 10 得到的值来设置所需的最小实例数。 请注意，1 个应用程序网关实例最少可处理 10 个计算单位

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 计算单位指标" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>针对应用程序网关 v2 SKU (Standard_v2/WAF_v2) 的手动缩放

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>根据高峰计算单位用量设置实例计数 

与自动缩放不同，在手动缩放中，必须根据流量要求手动设置应用程序网关的实例数。 建议你根据高峰使用情况并提供 10% 到 20% 的用于应对任何流量峰值的额外缓冲来设置实例计数。 例如，如果流量在高峰时需要 50 个实例，请预配 55 到 60 个实例，以应对可能会意外出现的流量峰值。

检查过去一个月的计算单位指标。 计算单位指标是网关的 CPU 使用率的表示形式。你可以根据高峰用量除以 10 得到的值来设置所需的实例数，因为 1 个应用程序网关实例可以处理最少 10 个计算单位

## <a name="monitoring-and-alerting"></a>监视和警报

若要获得有关流量或使用率异常的通知，可以针对特定指标设置警报。 有关应用程序网关提供的指标的完整列表，请查看[指标文档](./application-gateway-metrics.md)。 请查看 Azure 门户中的[将指标可视化](./application-gateway-metrics.md#metrics-visualization)和 [Azure Monitor 文档](../azure-monitor/alerts/alerts-metric.md)，了解如何为指标设置警报。

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>针对应用程序网关 v1 SKU (Standard/WAF) 的警报

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>在平均 CPU 使用率超出 80% 时发出警报

正常情况下，CPU 使用率不应经常超过 90%，因为这可能导致托管在应用程序网关后面的网站中出现延迟，并破坏客户端体验。 可以通过修改应用程序网关的配置（具体方法是：增加实例计数和/或转换到更大的 SKU 大小）来间接控制或改进 CPU 使用率。 设置一个在平均 CPU 使用率指标超出 80% 时会发出的警报。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>在不正常的主机计数超出阈值时发出警报

此指标表示应用程序网关无法成功探测的后端服务器数。 这将捕获应用程序网关实例无法连接到后端的问题。 如果此数字超出后端容量的 20%，则会发出警报。 例如 如果你目前在后端池中有 30 个后端服务器，则设置一个在不正常主机计数超过 6 的情况下就会发出的警报。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>在响应状态（4xx、5xx）超出阈值时发出警报 

在应用程序网关响应状态为 4xx 或 5xx 时创建警报。 可能会由于暂时性问题而偶然出现 4xx 或 5xx 响应。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>在失败的请求数超出阈值时发出警报 

在“失败的请求数”指标超出阈值时创建警报。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>示例：设置在过去 5 分钟内失败的请求数超出 100 时会发出的警报

此示例演示如何使用 Azure 门户设置在过去 5 分钟内失败的请求计数超出 100 时会发出的警报。
1. 导航到应用程序网关。
2. 在左侧面板中，选择“监视”选项卡下的“指标” 。 
3. 添加一个针对“失败的请求数”的指标。
4. 单击“新建警报规则”并定义条件和操作
5. 单击“创建警报规则”以创建并启用警报

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 - 创建警报" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>针对应用程序网关 v2 SKU (Standard_v2/WAF_v2) 的警报

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>在计算单位使用量超出 75% 的平均使用量时发出警报 

计算单位是对应用程序网关的计算使用量的度量。 检查过去 1 个月内的平均计算单位使用量，并设置在计算单位使用量超出 75% 的平均使用量时会发出的警报。 例如，如果平均使用量为 10 个计算单位，请设置在使用了 7.5 个计算单位时会发出的警报。 这会在用量不断增大时发出警报，并让你从容应对。 如果你认为这种流量将会持续，可以提高最小值，以提醒自己该流量可能会不断增大。 根据需要，按照上述缩放建议进行横向扩展。

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>示例：设置在达到平均 CU 用量的 75% 时发出警报

此示例演示如何使用 Azure 门户设置在达到平均 CU 用量的 75% 时发出警报。 
1. 导航到应用程序网关。
2. 在左侧面板中，选择“监视”选项卡下的“指标” 。 
3. 为“平均当前计算单位数”添加一个指标。 
4. 如果已将最小实例计数设置为平均 CU 用量，请继续设置在使用了最小实例数的 75% 时发出警报。 例如，如果平均用量为 10 个 CU，则设置在使用了 7.5 个 CU 时发出警报。 这会在用量不断增大时发出警报，并让你从容应对。 如果你认为这种流量将会持续，可以提高最小值，以提醒自己该流量可能会不断增大。 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 计算单位警报" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> 你可以根据自己对潜在流量高峰的敏感程度，设置在 CU 用量百分比降低或提高时发出警报。

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>在容量单位使用量超出 75% 的高峰使用量时发出警报 

容量单位根据吞吐量、计算和连接计数来表示总体网关使用量。 检查过去 1 个月内的最大容量单位使用量，并设置在容量单位使用量超出 75% 的高峰使用量时会发出的警报。 例如，如果最大使用量为 100 个容量单位，请设置在使用了 75 个容量单位时会发出的警报。 根据需要，按照上述两项建议进行横向扩展。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>在不正常的主机计数超出阈值时发出警报 

此指标表示应用程序网关无法成功探测的后端服务器数。 这将捕获应用程序网关实例无法连接到后端的问题。 如果此数字超出后端容量的 20%，则会发出警报。 例如 如果你目前在后端池中有 30 个后端服务器，则设置一个在不正常主机计数超过 6 的情况下就会发出的警报。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>在响应状态（4xx、5xx）超出阈值时发出警报 

在应用程序网关响应状态为 4xx 或 5xx 时创建警报。 可能会由于暂时性问题而偶然出现 4xx 或 5xx 响应。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>在失败的请求数超出阈值时发出警报 

在“失败的请求数”指标超出阈值时创建警报。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>在后端最后一个字节的响应时间超出阈值时发出警报 

此指标表示从开始与后端服务器建立连接到收到响应正文的最后一个字节的时间间隔。 创建一个在后端响应延迟比常规值高出某个阈值时会发出的警报。 例如，可以将此项设置为在后端响应延迟比常规值高出 30% 以上时发出警报。

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>在应用程序网关总时间超出阈值时发出警报

此间隔时间是根据从应用程序网关收到 HTTP 请求的第一个字节的时间，到将最后一个响应字节发送到客户端的时间计算的。 应该创建一个在后端响应延迟比常规值高出某个阈值时会发出的警报。 例如，用户可以将此项设置为在总时间延迟比常规值高出 30% 以上时发出警报。

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>设置提供地理筛选和机器人防护的 WAF 来阻止攻击
如果需要在应用程序的前面使用额外的安全层，请为 WAF 功能使用应用程序网关 WAF_v2 SKU。 可将 v2 SKU 配置为仅允许从给定的国家/地区访问你的应用程序。 设置一个 WAF 自定义规则，以基于地理位置明确允许或阻止流量。 有关详细信息，请参阅[地理位置筛选自定义规则](../web-application-firewall/ag/geomatch-custom-rules.md)和[如何通过 PowerShell 在应用程序网关 WAF_v2 SKU 上配置自定义规则](../web-application-firewall/ag/configure-waf-custom-rules.md)。

启用机器人防护以阻止已知恶意的机器人。 这应该可以减少进入应用程序的流量。 有关详细信息，请参阅[机器人防护和设置说明](../web-application-firewall/ag/configure-waf-custom-rules.md)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在应用程序网关和 WAF 上启用诊断

通过诊断日志，你可以查看防火墙日志、性能日志和访问日志。 可在 Azure 中使用这些日志来对应用程序网关进行管理和故障排除。 有关详细信息，请参阅我们的[诊断文档](./application-gateway-diagnostics.md#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>设置 TLS 策略以进一步提高安全性
请确保使用最新的 TLS 策略版本 ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s))。 此版本强制实施 TLS 1.2 和更强的密码。 有关详细信息，请参阅[通过 PowerShell 配置 TLS 策略版本和加密套件](./application-gateway-configure-ssl-policy-powershell.md)。