---
title: 启用了 Azure Arc 的服务器概述
description: 了解如何使用启用了 Azure Arc 的服务器像管理 Azure 资源一样管理在 Azure 外部托管的服务器。
ms.date: 08/27/2021
ms.topic: overview
ms.openlocfilehash: 2a6ed9eb865ed588653cd9ce5a41863af2db6de4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123108709"
---
# <a name="what-is-azure-arc-enabled-servers"></a>什么是启用了 Azure Arc 的服务器？

利用启用了 Azure Arc 的服务器，可以管理托管在 Azure 外部（不管是在你的企业网络上还是在其他云提供商处）的 Windows 和 Linux 物理服务器和虚拟机。 这种管理体验旨在与原生 Azure 虚拟机的管理方式保持一致。 当混合计算机连接到 Azure 时，它将成为一台联网计算机，被视为 Azure 中的资源。 每台已连接的计算机都有一个资源 ID，使计算机能够包含在资源组中。 现可受益于标准 Azure 构造，例如 Azure Policy 和应用标记。 为客户管理本地基础结构的服务提供商可以通过使用 [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) 来管理其混合计算机，就像跨多个客户环境管理本机 Azure 资源一样。

若要向混合计算机提供此体验，需要在每台计算机上安装 Azure Connected Machine 代理。 此代理不提供任何其他功能，而且不会取代 Azure [Log Analytics 代理](../../azure-monitor/agents/log-analytics-agent.md)。 在以下情况下，需要适用于 Windows 和 Linux 的 Log Analytics 代理：

* 想要主动监视计算机上运行的 OS 和工作负载，
* 使用自动化 Runbook 或解决方案（如更新管理）来管理它，或者
* 使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-introduction.md)）。

## <a name="supported-cloud-operations"></a>支持的云操作 

将计算机连接到启用了 Azure Arc 的服务器时，你能够执行下表中所述的以下操作功能。

|Operations 函数 |说明 | 
|--------------------|------------|
|**治理** ||
| Azure Policy |向计算机内部的审核设置分配 [Azure Policy 来宾配置](../../governance/policy/concepts/guest-configuration.md)。 若要了解将 Azure Policy 来宾配置策略与启用了 Arc 的服务器配合使用的成本，请参阅 Azure Policy [定价指南](https://azure.microsoft.com/pricing/details/azure-policy/)|
|**保护** ||
| Azure 安全中心 | 使用 [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint) 保护非 Azure 服务器（包括通过 [Azure Defender](../../security-center/defender-for-servers-introduction.md)），用于威胁检测、漏洞管理和主动监视潜在安全威胁。 Azure 安全中心提供来自检测到的威胁的警报和修正建议。 |
| Azure Sentinel | 可以对连接到启用了 Arc 的服务器的计算机[配置 Azure Sentinel ](scenario-onboard-azure-sentinel.md)以收集与安全相关的事件，并将这些事件与其他数据源相关联。 |
|**配置** ||
| Azure 自动化 |使用[更改跟踪和清单](../../automation/change-tracking/overview.md)功能，实现有关安装的软件、Microsoft 服务、Windows 注册表和文件以及 Linux 守护程序的配置更改。<br> 使用[更新管理](../../automation/update-management/overview.md)，为 Windows 和 Linux 服务器管理操作系统更新。 |
| Azure 自动管理 | 在使用[为启用了 Arc 的服务器自动管理计算机](../../automanage/automanage-arc.md)时，加入一组 Azure 服务。 |
| VM 扩展 | 使用支持的[启用了 Arc 的服务器 VM 扩展](manage-vm-extensions.md)，为非 Azure Windows 或 Linux 计算机提供部署后配置和自动化任务。 |
|**监视**|
| Azure Monitor | 监视已连接的计算机来宾操作系统性能，并发现应用程序组件，以使用 [VM 见解](../../azure-monitor/vm/vminsights-overview.md)来监视其进程以及与其他资源的依赖项。 使用 [Log Analytics 代理](../../azure-monitor/agents/agents-overview.md#log-analytics-agent)从计算机上运行的操作系统或工作负载收集其他日志数据，例如性能数据和事件。 此数据存储在 [Log Analytics 工作区](../../azure-monitor/logs/design-logs-deployment.md)中。 |

> [!NOTE]
> 目前，不支持直接从启用了 Arc 的服务器直接启用 Azure 自动化更新管理。 请参阅[从自动化帐户启用更新管理](../../automation/update-management/enable-from-automation-account.md)，以了解要求以及如何为服务器启用更新管理。

从混合计算机收集并存储在 Log Analytics 工作区中的日志数据现在包含特定于计算机的属性，例如资源 ID，以支持[资源上下文](../../azure-monitor/logs/design-logs-deployment.md#access-mode)日志访问。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

若要详细了解如何使用启用了 Arc 的服务器跨混合和多云环境实现 Azure 监视、安全性和更新服务，请观看以下视频。

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>支持的区域

有关启用了 Azure Arc 的服务器支持的区域的最终列表，请参阅 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)页。

在大多数情况下，创建安装脚本时选择的位置应该是在地理位置上最接近你的计算机位置的 Azure 区域。 静态数据存储在包含你指定区域的 Azure 地理区域中，如果你有数据驻留要求，这可能也会影响你对区域的选择。 如果你的计算机连接到的 Azure 区域受中断影响，则已连接的计算机不受影响，但使用 Azure 的管理操作可能无法完成。 如果发生区域性服务中断，而你有多个支持异地冗余服务的位置，则最好将各个位置的计算机连接到另一个 Azure 区域。

以下有关已连接计算机的元数据信息将被收集并存储在配置 Azure Arc 计算机资源的区域中：

- 操作系统名称和版本
- 计算机名称
- 计算机完全限定域名 (FQDN)
- Connected Machine 代理版本

例如，如果计算机在美国东部区域注册了 Azure Arc，则此数据存储在美国区域中。

### <a name="supported-environments"></a>支持的环境

启用了 Arc 的服务器支持管理在 Azure 外部托管的物理服务器和虚拟机。 若要详细了解哪些托管 VM 的混合云环境是受支持的，请参阅 [Connected Machine 代理先决条件](agent-overview.md#supported-environments)。

> [!NOTE]
> 不能通过启用了 Arc 的服务器来管理 Azure 中运行的虚拟机，这是设计使然，本身不支持这种功能。

### <a name="agent-status"></a>代理状态

Connected Machine 代理每 5 分钟向服务发送一条定期检测信号消息。 如果服务停止从计算机接收这些检测信号消息，系统会将该计算机视为脱机，并会在 15 到 30 分钟内在门户中将状态自动更改为“已断开连接”。 收到来自 Connected Machine 代理的后续检测信号消息后，其状态会自动更改为“已连接”。

## <a name="next-steps"></a>后续步骤

* 在多台混合计算机中评估或启用已启用了 Arc 的服务器之前，请先查看[连接的计算机代理概述](agent-overview.md)，以了解要求、有关代理的技术详细信息以及部署方法。

* 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。
