---
title: Azure 澳大利亚中的网关日志记录、审核和可见性
description: 如何在澳大利亚区域根据澳大利亚政府的具体政策、法规和法律要求配置日志记录、审核和可见性。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: dae5f20231a6ccf348299a0f04fb6f2ccfda36ff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183471"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Azure 澳大利亚中的网关日志记录、审核和可见性

检测和应对网络安全威胁依赖于生成、收集和分析与系统操作相关的数据。

Microsoft 在 Azure 中提供了内置工具，以帮助你实现日志记录、审核和可见性来管理 Azure 中部署的系统的安全性。 另外还提供了一个符合澳大利亚网络安全中心 (ACSC) 使用者指南以及信息安全手册 (ISM) 意图的参考体系结构。

网关充当网络层的信息流控制机制，它还可以在开放系统互连 (OSI) 模型的更高层控制信息。 对于控制安全域之间的数据流，以及防止从外部网络进行未经授权的访问而言，网关是必需的。 由于网关在控制安全域之间的信息流方面至关重要，任何故障（特别是分类较高的故障）都可能造成严重后果。 因此，用于提醒相关人员注意可能导致网络安全事件的情况的可靠机制对于网关尤为重要。

为网关实现日志记录和警报功能有助于检测网络安全事件、入侵企图和异常使用模式。 此外，在单独的安全日志服务器上存储事件日志可以增大攻击者删除日志记录信息以销毁有针对性的网络入侵证据的难度。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亚网络安全中心 (ACSC) 要求

ACSC 信息安全手册 (ISM) 中定义了英联邦系统的整体安全要求。 为了帮助英联邦实体满足 Azure 中的这些要求，《ACSC 使用者指南 – 受保护的 Microsoft Azure》和《ACSC 认证报告 – Microsoft Azure》出版物详细说明了以下与日志记录、审核和可见性相关的具体要求 ：

1. 为了缓解由于使用共享底层云资源而带来的风险，英联邦实体必须选用 Microsoft Azure 提供的功能（包括 Azure 安全中心、Azure Monitor、Azure Policy 和 Azure 顾问）来帮助实体对其 Azure 工作负载执行实时监视

2. ACSC 还建议英联邦实体将所有必需的安全日志转发到 ACSC，以供澳大利亚政府进行全面监视

3. 为了帮助缓解风险，英联邦实体应在其 Azure 订阅中进行以下配置：

    * 启用 Azure 安全中心
    * 升级到标准层
    * 在受支持的 Azure VM 中启用 Microsoft Monitoring Agent 的自动预配
    * 在安全中心仪表板上定期对安全建议和警报进行审查，确定其优先级并执行相应的缓解措施

4. 政府实体必须启用从其 Azure 订阅到 ACSC 的日志和事件转发，以便可以在 ACSC 中查看不符合本指南的情况。 Azure 事件中心提供用于执行将外部日志流式传输到 ACSC 或者由英联邦实体拥有的本地系统的功能

5. 英联邦实体应使其在 Azure 中启用的日志记录符合 ISM 中规定的要求

6. Microsoft 将在 Azure 中保留日志 90 天。 客户实体必须实施日志存档制度，以确保可以按照 NAA AFDA 的要求将日志保留七年

7. 具有本地或基于 Azure 的安全信息和事件管理 (SIEM) 功能的英联邦实体也可以将日志转发到这些系统

8. 英联邦实体应为网络安全组 (NSG) 和虚拟机实施网络观察程序流日志。 这些日志应存储在仅包含安全日志的专用存储帐户中，对该存储帐户的访问应受 Azure 基于角色的访问控制 (Azure RBAC) 的保护

9. 英联邦实体必须实施 ACSC 使用者指南，确保 Azure 工作负载满足 ISM 中所述的日志记录和监视意图。 英联邦实体还必须选用相应的 Azure 功能，以帮助 ACSC 获得与澳大利亚政府使用 Azure 的情况相关的实时监控、警报和日志

## <a name="architecture"></a>体系结构

若要有把握地了解进入和离开 Azure 环境的网络流量，必须在一组适当的组件上启用所需的日志记录。 这可以确保环境的完全可见性，并提供必要的数据来执行分析。

![Azure 监视体系结构](media/visibility.png)

## <a name="components"></a>组件

上面所示的体系结构由提供日志源、日志收集、日志保留、日志分析或事件响应功能的离散组件构成。 此体系结构包含的各个组件通常涉及到可通过 Internet 访问的 Azure 部署。

|函数|组件|
|---|---|
|日志源|<ul><li>应用程序网关</li><li>VPN 网关</li><li>Azure 防火墙</li><li>网络虚拟设备</li><li>Azure 负载均衡器</li><li>虚拟机</li><li>域命名系统 (DNS) 服务器</li><li>Syslog 和/或日志收集服务器</li><li>NSG</li><li>Azure 活动日志</li><li>Azure 诊断日志</li><li>Azure Policy</li></ul>|
|日志收集|<ul><li>事件中心</li><li>网络观察程序</li><li>Log Analytics</li></ul>|
|日志保留|<ul><li>Azure 存储</li></ul>|
|日志分析|<ul><li>Azure 安全中心 (ASC)</li><li>Azure 顾问</li><li>Log Analytics 解决方案<ul><li>流量分析</li><li>DNS Analytics（预览版）</li><li>活动 Log Analytics</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|事件响应|<ul><li>Azure 警报</li><li>Azure 自动化</li></ul>|
|

该体系结构的工作原理是先从所需的源生成日志，然后将日志收集到集中式存储库。 收集日志后，这些日志可以：

* 由 Azure 分析服务用来获取见解；
* 转发到外部系统，或者
* 存档到存储以进行长期保留。

若要对分析工具识别到的关键事件做出响应，可以配置警报并开发自动化功能，以采取必要的措施进行主动管理和响应。

## <a name="general-guidance"></a>一般指南

实施本文中列出的组件时，以下一般指南适用：

* 验证服务的区域可用性，确保所有数据保留在已授权的位置，并部署到澳大利亚中部或澳大利亚中部 2 区域（受保护工作负载的第一优先区域）

* 参阅《Azure - ACSC 认证报告 - 2018 年受保护服务》出版物来了解各个服务的认证状态，并根据《ACSC 使用者指南 - 受保护的 Microsoft Azure》对报告中未包含的任何相关组件执行自我评估 

* 对于本文中未提到的组件，英联邦实体应遵循已包含的有关生成、捕获、分析和保留日志的原则

* 确定高价值系统以及 Azure 中托管的系统的所有网络入口和出口点上的日志记录、审核和可见性并指定其优先级

* 合并日志，并尽量减少日志记录工具（例如存储帐户、Log Analytics 工作区和事件中心）的实例数目

* 通过 Azure 基于角色的访问控制 (Azure RBAC) 来限制管理特权

* 使用多重身份验证 (MFA) 来管理帐户或在 Azure 中配置资源

* 跨多个订阅集中管理日志收集时，请确保管理员在每个订阅中拥有必要的特权

* 确保虚拟机的网络连接和任何所需代理配置（包括网络虚拟设备 (NVA)、日志收集服务器和 DNS 服务器）能够连接到所需的 Azure 服务（例如 Log Analytics 工作区、事件中心和存储）

* 将 Microsoft Monitoring Agent (MMA) 配置为利用 TLS 版本 1.2

* 使用 Azure Policy 来监视合规性并强制实施合规措施

* 强制实施对所有数据存储库（例如存储和数据库）进行加密

* 使用本地冗余存储 (LRS) 和快照来提高存储帐户和关联数据的可用性

* 考虑使用异地冗余存储 (GRS) 或场外存储，以符合灾难恢复策略

|资源|URL|
|---|---|
|澳大利亚法规和政策合规性文档|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 产品 - 澳大利亚区域和非区域产品|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure安全和审核日志管理白皮书|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft Monitoring Agent 配置|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](../azure-monitor/agents/log-analytics-agent.md)|
|

## <a name="component-guidance"></a>组件指南

本部分介绍每个组件的用途及其在整体日志记录、审核和可见性体系结构中发挥的作用。 此外，还提供了用于访问参考文档、指南和教程等有用资源的其他链接。

## <a name="log-sources"></a>日志源

必须先生成所需的日志，然后才能完成任何分析、警报或报告。 Azure 日志分为控制/管理日志、数据平面日志和已处理的事件。

|类型|说明|
|---|---|
|控制/管理日志|提供有关 Azure 资源管理器操作的信息|
|数据平面日志|提供有关在使用 Azure 资源过程中引发的事件的信息，例如虚拟机中的日志，以及通过 Azure Monitor 提供的诊断日志|
|已处理的事件|提供有关已由 Azure 处理的已分析事件/警报的信息，例如 Azure 安全中心在何处处理并分析了订阅以提供安全警报|
|

### <a name="application-gateway"></a>应用程序网关

Azure 应用程序网关是 Azure 环境的可能入口点之一，因此你需要捕获用来与 Web 应用程序通信的传入连接相关的信息。 应用程序网关可提供与 Web 应用程序使用情况相关的重要信息，并可以帮助检测网络安全事件。 应用程序网关将元数据发送到活动日志和 Azure Monitor 中的诊断日志，然后，可以在 Log Analytics 中利用这些数据，或者将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|应用程序网关文档|[https://docs.microsoft.com/azure/application-gateway/](../application-gateway/index.yml)|
|应用程序网关快速入门指南|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](../application-gateway/quick-create-portal.md)|
|

### <a name="vpn-gateway"></a>VPN 网关

VPN 网关是 Azure 环境中各种通信（例如与本地环境和管理流量的连接）的潜在入口点。 VPN 网关上的日志记录提供与 Azure 环境建立的连接的见解和可跟踪性。 日志记录可提供审核和分析，并有助于检测或调查恶意连接或异常连接。 VPN 网关日志将发送到 Azure Monitor 活动日志，然后，可以在 Log Analytics 中利用这些日志，或者将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|VPN 网关文档|[https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|澳大利亚政府特定的 VPN 网关指南|[Azure VPN 网关配置](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure 防火墙

Azure 防火墙提供 Azure 环境中受控的出口点，生成的日志（包括有关已尝试和成功的出站连接的信息）是日志记录策略中的重要元素。 这些日志可以验证系统是否按设计运行，并有助于检测企图连接到未经授权的外部系统的恶意代码或行动者。 Azure 防火墙将日志写入到活动日志和 Azure Monitor 中的诊断日志，然后，可以在 Log Analytics 中利用这些日志，或者将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|Azure 防火墙文档|[https://docs.microsoft.com/azure/firewall/](../firewall/index.yml)|
|教程：监视 Azure 防火墙日志和指标|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](../firewall/firewall-diagnostics.md)|
|

### <a name="network-virtual-appliances-nva"></a>网络虚拟设备 (NVA)

NVA 可用于补充 Azure 原生提供的安全功能。 在 NVA 上生成的日志是可用于检测网络安全事件的宝贵资源，并且是整体日志记录、审核和可见性策略的重要组成部分。 若要从 NVA 捕获日志，请利用 Microsoft Monitoring Agent (MMA)。 对于不支持安装 MMA 的 NVA，请考虑使用 Syslog 或其他日志收集服务器来中继日志。

|资源|链接|
|---|---|
|网络虚拟设备概述|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|NVA 文档|有关 Azure 中相关 NVA 的实现，请参阅供应商文档|
|

### <a name="azure-load-balancer"></a>Azure 负载均衡器

Azure 负载均衡器日志用于获取与 Azure 中部署的系统相关的连接和使用情况的有用信息。 这些信息可用于监视运行状况和可用性，同时也形成了另一个重要组件用于获取通信流量的所需见解以及检测恶意或异常流量模式。 Azure 负载均衡器将日志写入到活动日志和 Azure Monitor 中的诊断日志，然后，可以在 Log Analytics 中利用这些日志，或者将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|Azure 负载均衡器文档|[https://docs.microsoft.com/azure/load-balancer](../load-balancer/index.yml)|
|标准负载均衡器的指标和运行状况诊断|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](../load-balancer/load-balancer-standard-diagnostics.md)|
|

### <a name="virtual-machines"></a>虚拟机

虚拟机是发送和接收网络通信、处理数据及提供服务的终结点。 由于虚拟机可以托管数据或关键系统服务，因此确保它们正常运行并可检测网络安全事件至关重要。 虚拟机收集各种事件和审核日志，这些日志可以跟踪系统的运行情况以及对该系统执行的操作。 可以使用 Microsoft Monitoring Agent 将在虚拟机上收集的日志转发到 Log Analytics 工作区，然后，Azure 安全中心和适用的 Log Analytics 解决方案可在该工作区中分析这些日志。 虚拟机还可以直接与 Azure 事件中心集成，或者直接或通过日志收集服务器与 SIEM 集成。

|资源|链接|
|---|---|
|虚拟机|[https://docs.microsoft.com/azure/virtual-machines](../virtual-machines/index.yml)|
|从虚拟机收集数据|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](../azure-monitor/vm/monitor-virtual-machine.md)|
|将虚拟机日志流式传输到事件中心|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)|
|

### <a name="domain-name-services-dns-servers"></a>域名服务 (DNS) 服务器

DNS 服务器日志提供有关系统在内部或外部尝试访问的服务的关键信息。 捕获 DNS 日志有助于识别网络安全事件，并提供事件类型以及可能受影响的系统的见解。 可以在 DNS 服务器上使用 Microsoft 管理代理 (MMA) 将日志转发到 Log Analytics，以便在 DNS Analytics（预览版）中使用。

|资源|链接|
|---|---|
|虚拟网络的 Azure 名称解析|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog 和日志收集服务器

若要从网络虚拟设备接收日志，或者从其他系统接收自定义安全日志以便在 SIEM 中使用，可以在 Azure VNet 中部署专用服务器。 可以在 Syslog 服务器上收集 Syslog 日志，并将其中继到 Log Analytics 进行分析。 日志收集服务器是与集中式监视系统或 SIEM 使用的任何日志聚合和分发功能相关的通用术语。 日志收集服务器可用于简化网络体系结构和安全性，以及在筛选和聚合日志后将其分发到集中化功能。

|资源|链接|
|---|---|
|Log Analytics 中的 Syslog 数据源|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](../azure-monitor/agents/data-sources-syslog.md)|
|日志收集服务器|有关监视和 SIEM 体系结构的详细信息，请参阅供应商文档|
|

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

NSG 控制 Azure 中的虚拟网络的传入和传出流量。 NSG 对允许或拒绝的流量流应用规则，这些流量包括 Azure 内部的流量，以及在 Azure 与外部网络（例如本地或 Internet）之间传送的流量。 NSG 应用于虚拟网络中的子网，或应用于单个网络接口。 若要捕获有关进入和离开 Azure 中系统的流量的信息，可以通过网络观察程序 NSG 流日志功能启用 NSG 日志。 这些日志用于形成系统标准操作的基线，并且是流量分析（提供 Azure 中托管的系统的流量模式详细见解）的数据源。

|资源|链接|
|---|---|
|网络安全组文档|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|针对网络安全组进行流日志记录简介|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](../network-watcher/network-watcher-nsg-flow-logging-overview.md)|
|教程：使用 Azure 门户记录虚拟机的传入和传出网络流量|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](../network-watcher/network-watcher-nsg-flow-logging-portal.md)|
|

### <a name="azure-activity-log"></a>Azure 活动日志

Azure Monitor 中的 Azure 活动日志是一种方便用户深入了解 Azure 中发生的订阅级别事件的订阅日志。 活动日志有助于确定针对 Azure 订阅中的资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作者和操作时间”。 活动日志对于跟踪 Azure 环境中发生的配置更改至关重要。 Azure 活动日志自然而然可在 Log Analytics 解决方案中使用，并可发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure 活动日志文档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|将 Azure 活动日志流式传输到事件中心|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](../azure-monitor/essentials/activity-log.md#legacy-collection-methods)|
|

### <a name="azure-diagnostic-log"></a>Azure 诊断日志

Azure Monitor 诊断日志是 Azure 服务发出的日志，提供与该服务的操作相关的各种频繁生成的数据。 诊断日志详细提供资源操作的见解，可用于满足审核或故障排除等各种要求。 Azure 诊断日志自然而然可在 Log Analytics 解决方案中使用，并可发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure 诊断日志文档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|诊断日志的支持服务|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](../azure-monitor/essentials/resource-logs-schema.md)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy 强制实施有关如何部署资源（例如类型、位置和配置）的规则。 可以配置 Azure Policy，以确保只能部署符合要求的资源。 Azure Policy 是保持 Azure 环境完整性的核心组件。 与 Azure Policy 相关的事件将记录到 Azure 活动日志，并自然而然可在 Log Analytics 解决方案中使用，或者可以发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure Policy 文档|[https://docs.microsoft.com/azure/governance/policy](../governance/policy/index.yml)|
|通过 Azure 蓝图利用 Azure Policy 和资源管理器模板|[https://docs.microsoft.com/azure/governance/blueprints/overview](../governance/blueprints/overview.md)|
|

## <a name="log-collection"></a>日志收集

从多个日志源生成日志后，需将其存储在集中位置才能进行持续访问和分析。 Azure 为日志收集提供多种方法和选项，可根据日志类型和要求利用这些方法和选项。

### <a name="event-hubs"></a>事件中心

事件中心的用途是聚合各种源的日志数据以便分发。 可将日志数据从事件中心发送到 SIEM、发送到 ACSC 以确保合规，或者发送到存储以便长期保留。

|资源|链接|
|---|---|
|事件中心文档|[https://docs.microsoft.com/azure/event-hubs](../event-hubs/index.yml)|
|有关事件中心和外部工具的指南|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics 是 Azure Monitor 的一部分，用于进行日志分析。 Log Analytics 使用工作区作为存储机制，其中的日志数据可用于 Azure 中提供的各种分析工具和解决方案。 Log Analytics 可直接与各种 Azure 组件集成，也可以通过 Microsoft Monitoring Agent 与虚拟机集成。

|资源|链接|
|---|---|
|Log Analytics 文档|[https://docs.microsoft.com/azure/azure-monitor](../azure-monitor/index.yml)|
|教程：在 Log Analytics 中分析数据|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](../azure-monitor/logs/log-analytics-tutorial.md)|
|

### <a name="network-watcher"></a>网络观察程序

ACSC 建议使用网络观察程序来帮助了解和捕获 Azure 订阅中的网络流量。 NSG 流日志为 Log Analytics（原生通过 Azure 提供增强的可见性、分析和报告）中的流量分析解决方案提供输入。 网络观察程序还直接从 Azure 门户提供数据包捕获功能，而无需登录到虚拟机。 使用数据包捕获可以创建数据包捕获会话来跟踪传入和传出虚拟机的流量。

|资源|链接|
|---|---|
|网络观察程序|[https://docs.microsoft.com/azure/network-watcher](../network-watcher/index.yml)|
|数据包捕获概述|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](../network-watcher/network-watcher-packet-capture-overview.md)|
|

## <a name="log-retention"></a>日志保留期

对于澳大利亚政府组织而言，必须根据澳大利亚国家档案局[行政职能处置管理局 (AFDA)](https://www.naa.gov.au/information-management/records-authorities/types-records-authorities/afda-express-version-2-functions) 的要求保留 Azure 中捕获的日志，其中规定日志最长可以保留七年。

|日志位置|保持期|
|---|---|
|Azure 活动日志|最长 90 天|
|Log Analytics 工作区|最长两年|
|事件中心|最长七天|
|

你需要负责确保按照 AFDA 和其他法规要求适当存档日志。

### <a name="azure-storage"></a>Azure 存储

Azure 存储是 Azure 中用于长期保留日志的存储库。 Azure 存储可用于存档来自 Azure 的日志，包括事件中心、Azure 活动日志和 Azure 诊断日志。 数据在存储中的保留期可设置为零，也可以指定为若干天。 零天保留期表示永久保留日志；或者，保留期值也可以是介于 1 和 2147483647 之间的任意天数。

|资源|链接|
|---|---|
|Azure 存储文档|[https://docs.microsoft.com/azure/storage](../storage/index.yml)|
|通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](../event-hubs/event-hubs-capture-overview.md)|
|教程：使用 Azure 存储将 Azure 指标和日志数据存档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](../azure-monitor/essentials/platform-logs-overview.md)|
|Azure 存储复制|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](../storage/common/storage-redundancy.md)|
|创建 Blob 的快照|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>日志分析

生成日志并将其存储在集中位置后，必须对其进行分析才能有助于检测已尝试或成功的安全事件。 检测到安全事件时，机构需要能够对这些事件做出响应，以及跟踪、遏制和修正任何威胁。

### <a name="azure-security-center-asc"></a>Azure 安全中心 (ASC)

Azure 安全中心提供统一的安全管理和高级威胁防护。 Azure 安全中心可对各种工作负载应用安全策略、限制威胁攻击面，以及检测和响应攻击。 Azure 安全中心提供各种 Azure 组件的仪表板和分析。 ACSC 使用者指南中规定必须使用 Azure 安全中心。

|资源|链接|
|---|---|
|Azure 安全中心文档|[https://docs.microsoft.com/azure/security-center](../security-center/index.yml)|
|快速入门：将 Azure 订阅加入安全中心标准层|[https://docs.microsoft.com/azure/security-center/security-center-get-started](../security-center/security-center-get-started.md)|
|

### <a name="traffic-analytics"></a>流量分析

流量分析是一种基于云的解决方案，可用于洞察 Azure 中的用户和应用程序活动。 流量分析可分析网络观察程序 NSG 流日志，以提供 Azure 中流量流的见解。 流量分析用于提供与不同虚拟网络中出现的网络流量相关的仪表板、报告、分析和事件响应功能。 流量分析提供重要见解，并帮助识别和解决网络安全事件。

|资源|链接|
|---|---|
|流量分析文档|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](../network-watcher/traffic-analytics.md)|
|

### <a name="azure-advisor"></a>Azure 顾问

Azure 顾问分析资源配置和其他数据，以推荐解决方案来帮助提高资源的性能、安全性和高可用性，同时找到降低 Azure 总体支出的机会。 Azure 顾问由 ACSC 推荐，提供有关 Azure 环境配置的易于访问的详细建议。

|资源|链接|
|---|---|
|Azure 顾问文档|[https://docs.microsoft.com/azure/advisor](../advisor/index.yml)|
|Azure 顾问入门|[https://docs.microsoft.com/azure/advisor/advisor-get-started](../advisor/advisor-get-started.md)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics（预览版）

DNS Analytics 是收集、分析和关联 Windows DNS 分析与审核日志以及其他相关数据的 Log Analytics 解决方案。 DNS Analytics 可以识别尝试解析恶意域名的客户端、过时的资源记录、经常查询的域名和频繁发出请求的 DNS 客户端。 DNS Analytics 还提供 DNS 服务器上的请求负载和动态 DNS 注册失败的的见解。 DNS Analytics 用于提供与 Azure 环境中发出的 DNS 查询相关的仪表板、报告、分析和事件响应功能。 DNS Analytics 提供重要见解，并帮助识别和解决网络安全事件。

|资源|链接|
|---|---|
|DNS Analytics 文档|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](../azure-monitor/insights/dns-analytics.md)|
|

### <a name="activity-log-analytics"></a>活动 Log Analytics

Activity Log Analytics 是帮助分析和搜索多个 Azure 订阅中 Azure 活动日志的 Log Analytics 解决方案。 Activity Log Analytics 用于提供与针对整个 Azure 环境中的资源执行的操作相关的集中式仪表板、报告、分析和事件响应功能。 Activity Log Analytics 可帮助进行审核和调查。

|资源|链接|
|---|---|
|收集和分析 Log Analytics 中的 Azure 活动日志|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](../azure-monitor/essentials/activity-log.md)|
|

### <a name="security-information-and-event-management-siem"></a>安全信息和事件管理 (SIEM)

SIEM 系统提供集中式存储、审核和分析安全日志，具有用于引入各种日志数据的已定义机制，以及用于分析、报告和事件检测与响应的智能工具。 可以使用包含 Azure 日志记录信息的 SIEM 功能来补充 Azure 原生提供的安全功能。 英联邦实体可以根据具体的要求，利用托管在 Azure 中的虚拟机上、本地或作为服务型软件 (SaaS) 功能提供的 SIEM。

|资源|链接|
|---|---|
|Azure Sentinel（预览版）|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM 文档|有关 SIEM 体系结构和指南，请参阅供应商文档|
|使用 Azure Monitor 与 SIEM 工具集成|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>澳大利亚网络安全中心

澳大利亚网络安全中心 (ACSC) 是澳大利亚政府的国家网络安全主管单位。 它将澳大利亚政府的各项网络安全功能汇集在一起，以提高澳大利亚社区的网络复原能力，并在数字时代为澳大利亚的经济和社会繁荣提供支持。 ACSC 建议英联邦实体将系统生成的所有必需的日志文件、事件和日志转发到 ACSC，以供澳大利亚政府进行全面监视。

|资源|链接|
|---|---|
|澳大利亚网络安全中心网站|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>事件响应

生成相应的日志、将其收集到集中式存储库并执行分析，可以提高对系统的了解程度并提供检测网络安全事件的机制。 检测到事件后，下一步是对这些事件做出反应，执行操作来保持系统正常运行，并防范服务和数据泄露。 Azure 提供多种服务的组合来有效响应发生的任何事件。

### <a name="azure-alerts"></a>Azure 警报

Azure 警报可用于通知支持人员和安全人员响应特定的事件。 这样，英联邦实体便可以主动响应本文中列出的分析服务所引发的相关事件的检测。

|资源|链接|
|---|---|
|Microsoft Azure 中的警报概述|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](../azure-monitor/alerts/alerts-overview.md)|
|管理和响应 Azure 安全中心的安全警报|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](../security-center/security-center-managing-and-responding-alerts.md)|
|Azure Monitor 日志警报|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](../azure-monitor/alerts/alerts-log.md)|
|

### <a name="azure-automation"></a>Azure 自动化

英联邦实体可以通过 Azure 自动化触发操作来响应事件。 此类操作可以是在虚拟机上启动数据包捕获、运行工作流、停止或者启动虚拟机或服务，或者其他各种任务。 通过自动化可以快速响应警报而无需手动干预，从而减少了响应时间，降低了事件的严重性。

|资源|链接|
|---|---|
|Azure 自动化文档|[https://docs.microsoft.com/azure/automation](../automation/index.yml)|
|操作指南：使用警报触发 Azure 自动化 Runbook|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](../automation/automation-create-alert-triggered-runbook.md)|
|

## <a name="next-steps"></a>后续步骤

查看有关[网关安全远程管理](gateway-secure-remote-administration.md)的文章，详细了解如何在 Azure 中安全管理网关环境。