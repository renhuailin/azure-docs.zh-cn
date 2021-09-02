---
title: 使用 Azure Monitor 监视虚拟机：安全性
description: 了解用于监视虚拟机安全性的服务及其与 Azure Monitor 之间的联系。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674273"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>使用 Azure Monitor 监视虚拟机：安全监视
本文属于[在 Azure Monitor 中监视虚拟机及其工作负载](monitor-virtual-machine.md)方案的一部分。 其中介绍了用于监视虚拟机安全性的 Azure 服务，以及这些服务与 Azure Monitor 之间的联系。 Azure Monitor 旨在监视虚拟机及其他云资源的可用性和性能。 尽管存储在 Azure Monitor 的操作数据对调查安全事件可能有用，但 Azure 中的其他服务也可用于监视安全性。 

> [!IMPORTANT]
> 安全服务有其自己的成本，独立于 Azure Monitor 之外。 在配置这些服务之前，请参阅它们的定价信息，以确定在使用它们方面的适当投资。

## <a name="azure-services-for-security-monitoring"></a>用于安全监视的 Azure 服务
Azure Monitor 侧重于监视活动日志、指标、Log Analytics 支持的源等操作数据，包括 Windows 事件（不包括安全事件）、性能计数器、日志和 Syslog。 Azure 中的安全监视由 Azure 安全中心和 Azure Sentinel 执行。 这些服务各有额外的成本，因此应在实现它们之前确定它们在你的环境中的价值。

[Azure 安全中心](../../security-center/security-center-introduction.md)收集有关 Azure 资源和混合服务器的信息。 虽然安全中心可以收集安全事件，但它主要侧重于收集清单数据、评估扫描结果和策略审核，以突出显示漏洞并推荐纠正措施。 值得注意的功能包括交互式网络映射、实时 VM 访问、自适应网络强化和自适应应用程序控制（用于阻止可疑可执行文件）。

[Azure Defender for Servers](../../security-center/azure-defender.md) 是安全中心提供的服务器评估解决方案。 Defender for Servers 可以将 Windows 安全事件发送到 Log Analytics。 安全中心不依赖于 Windows 安全事件来执行警报或分析。 此功能可用于事件的集中存档，以便执行调查或实现其他目的。

[Azure Sentinel](../../sentinel/overview.md) 是安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Sentinel 从各种 Microsoft 和第三方源收集安全数据，以提供警报、可视化和自动化。 此解决方案侧重于合并尽可能多的安全日志，包括 Windows 安全事件。 Azure Sentinel 还可以收集 Windows 安全事件日志，且通常会与安全中心共享 Log Analytics 工作区。 只有在共享同一工作区时，才能从 Azure Sentinel 或安全中心收集安全事件。 与安全中心不同，安全事件是 Azure Sentinel 警报和分析的关键。

[Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) 是一个企业终结点安全平台，专门用于帮助企业网络防御、检测、调查和响应高级威胁。 它的设计主要侧重于保护 Windows 用户设备。 Defender for Endpoint 使用各种操作系统监视工作站、服务器、平板电脑和手机，以发现安全问题和漏洞。 Defender for Endpoint 与 Microsoft Endpoint Manager 保持严格统一，以便收集数据并提供安全评估。 数据收集主要基于 ETW 跟踪日志，并存储在独立的工作区中。

## <a name="integration-with-azure-monitor"></a>与 Azure Monitor 集成
下表列出了包含安全服务的 Azure Monitor 的集成点。 所有服务均使用相同的 Log Analytics 代理，由于没有任何其他部署到虚拟机的组件，因此复杂性得到了降低。 安全中心和 Azure Sentinel 在同一 Log Analytics 工作区中存储数据，以便可以使用日志查询来关联不同服务所收集的数据。 或者，你也可以创建一个自定义工作簿，将安全数据、可用性及性能数据合并到同一视图中。

| 集成点       | Azure Monitor | Azure 安全中心 | Azure Sentinel | Defender for Endpoint |
|:---|:---|:---|:---|:---|
| 收集安全事件     |   | X | X | X |
| 在 Log Analytics 工作区中存储数据 | X | X | X |   | 
| 使用 Log Analytics 代理     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>工作区设计的注意事项
如[使用 Azure Monitor 监视虚拟机：配置监视](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace)中所述，Azure Monitor 和安全服务需要 Log Analytics 工作区。 根据具体的要求，你可以选择共享公共工作区，或者将可用性和性能数据与安全数据分开。 有关设计工作区配置应考虑的逻辑的完整信息，请参阅[设计 Azure Monitor 日志部署](../logs/design-logs-deployment.md)。

## <a name="agent-deployment"></a>代理部署
可以将安全中心配置为自动将 Log Analytics 代理部署到 Azure 虚拟机。 尽管使用 Azure Monitor 部署同一代理看起来可能是多余的，但由于二者将分别执行各自的配置，你可能想要同时启用它们。 例如，如果安全中心尝试预配已由 Azure Monitor 监视的计算机，它将使用已安装的代理并添加安全中心工作区的配置。

## <a name="next-steps"></a>后续步骤

* [分析为虚拟机收集的监视数据](monitor-virtual-machine-analyze.md)
* [根据收集的数据创建警报](monitor-virtual-machine-alerts.md)
