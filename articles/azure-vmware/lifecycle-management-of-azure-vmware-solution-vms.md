---
title: Azure VMware 解决方案 VM 的生命周期管理
description: 了解如何使用 Microsoft Azure 本机工具管理 Azure VMware 解决方案 VM 生命周期的所有方面。
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 2cb9964b68769b1e784cebf62b4d336b355c68fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572208"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware 解决方案 VM 的生命周期管理

Microsoft Azure 本机工具使你可以在 Azure 环境中监视和管理虚拟机 (VM)。 不过，它们还使你可以监视和管理 Azure VMware 解决方案的 VM 和本地 VM。 在此概述中，我们将介绍 Azure 提供的集成监视体系结构，以及如何使用其本机工具在整个生命周期中管理 Azure VMware 解决方案 VM。

## <a name="benefits"></a>好处

- Azure 本机服务可用于在混合环境（Azure、Azure VMware 解决方案和本地）中管理 VM。
- Azure、Azure VMware 解决方案和本地 VM 的集成监视和可见性。
- 使用 Azure 自动化中的 Azure 更新管理可以为 Windows 和 Linux 计算机管理操作系统更新。 
- Azure 安全中心提供高级威胁防护，包括：
    - 文件完整性监视
    - 无文件安全警报
    - 操作系统补丁评估
    - 安全配置错误评估
    - 终结点保护评估 
- 使用已启用 Azure Arc 的服务器 VM 扩展支持可为新的和现有的 VM 轻松部署 Log Analytics 代理。 
- 通过 Azure Monitor 中的 Log Analytics 工作区可以使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 将数据和日志收集到单个点，并将该数据呈现给不同的 Azure 本机服务。 
- Azure Monitor 的新增好处包括： 
    - 无缝监视 
    - 更好的基础结构可见性 
    - 即时通知 
    - 自动解决 
    - 成本效益 

## <a name="integrated-azure-monitoring-architecture"></a>集成 Azure 监视体系结构

下图显示适用于 Azure VMware 解决方案 VM 的集成监视体系结构。

![集成 Azure 监视体系结构](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>开始之前

如果你是刚接触 Azure 或不熟悉前面提到的任何服务，请查看以下文章：

- [自动化帐户身份验证概述](../automation/automation-security-overview.md)
- [设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)和 [Azure Monitor](../azure-monitor/overview.md)
- Azure 安全中心的[计划](../security-center/security-center-planning-and-operations-guide.md)和[受支持平台](../security-center/security-center-os-coverage.md)
- [启用用于 VM 的 Azure Monitor 概述](../azure-monitor/vm/vminsights-enable-overview.md)
- [什么是已启用 Azure Arc 的服务器？](../azure-arc/servers/overview.md)和[什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [更新管理概述](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>集成和部署 Azure 本机服务

### <a name="enable-azure-update-management"></a>启用 Azure 更新管理

Azure 自动化中的 Azure 更新管理可在混合环境中为 Windows 和 Linux 计算机管理操作系统更新。 它会监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区才能使用存储的数据评估 VM 上的更新状态。

1.  首先需要[创建 Azure 自动化帐户](../automation/automation-create-standalone-account.md)，然后才能将 Log Analytics 添加到 Azure 更新管理。 如果你喜欢使用模板创建帐户，请参阅[使用 Azure 资源管理器模板创建自动化帐户](../automation/quickstart-create-automation-account-template.md)。

2. 通过 Log Analytics 工作区可以使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 若要创建 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 如果愿意，还可以通过 [CLI](../azure-monitor/logs/quick-create-workspace-cli.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 或 [Azure 资源管理器模板](../azure-monitor/logs/resource-manager-workspace.md)创建工作区。

3. 若要为 VM 启用 Azure 更新管理，请参阅 [从自动化帐户启用更新管理](../automation/update-management/enable-from-automation-account.md)。 在该过程中，会将 Log Analytics 工作区与自动化帐户链接。 
 
4. 将 VM 添加到 Azure 更新管理后，便可以 [在 VM 上部署更新并查看结果](../automation/update-management/deploy-updates.md)。 

### <a name="enable-azure-security-center"></a>启用 Azure 安全中心

在云中和本地，Azure 安全中心跨混合工作负载提供高级威胁防护。 它会评估 Azure VMware 解决方案 VM 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以进行解决。

Azure 安全中心不需要部署。 有关详细信息，请参阅[虚拟机支持的功能](../security-center/security-center-services.md)列表。

1. 若要将 Azure VMware 解决方案 VM 和非 Azure VM 添加到安全中心，请参阅[快速入门：设置Azure 安全中心](../security-center/security-center-get-started.md)。 

2. 添加 Azure VMware 解决方案 VM 或来自非 Azure 环境的 VM 后，在安全中心内启用 Azure Defender。 安全中心会评估 VM 中是否存在潜在安全问题。 它还会在“概述”选项卡中提供建议。有关详细信息，请参阅 [Azure 安全中心内的安全建议](../security-center/security-center-recommendations.md)。

3. 可以在 Azure 安全中心内定义安全策略。 有关配置安全策略的信息，请参阅[使用安全策略](../security-center/tutorial-security-policy.md)。

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>将 VM 加入已启用 Azure Arc 的服务器

Azure Arc 可将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。

- 有关为多个 Windows 或 Linux VM 启用已启用 Azure Arc 的服务器的信息，请参阅[将混合计算机大规模连接到 Azure](../azure-arc/servers/onboard-service-principal.md)。

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>使用已启用 Arc 的 Kubernetes 加入混合 Kubernetes 群集

可以使用已启用 Azure Arc 的 Kubernetes 附加在 Azure VMware 解决方案环境中托管的 Kubernetes 群集。 

- 有关详细信息，请参阅[创建已启用 Azure Arc 的加入服务主体](../azure-arc/kubernetes/create-onboarding-service-principal.md)。

### <a name="deploy-the-log-analytics-agent"></a>部署 Log Analytics 代理

Azure VMware 解决方案 VM 可以通过 Log Analytics 代理（也称为 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理）进行监视。 启用 Azure 自动化更新管理时，已创建 Log Analytics 工作区。

- 使用[已启用 Azure Arc 的服务器 VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)部署 Log Analytics 代理。

### <a name="enable-azure-monitor"></a>启用 Azure Monitor

Azure Monitor 提供了一个全面的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 它不需要部署。 使用 Azure Monitor 可以监视来宾操作系统性能，并发现和映射 Azure VMware 解决方案或本地 VM 的应用程序依赖项。

- Azure Monitor 使你可以收集来自不同源的数据以进行监视和分析。 有关详细信息，请参阅 [Azure Monitor 的监视数据源](../azure-monitor/agents/data-sources.md)。

- 收集不同类型的数据以用于分析、可视化和警报。 有关详细信息，请参阅 [Azure Monitor 数据平台](../azure-monitor/data-platform.md)。

- 若要使用 Log Analytics 工作区配置 Azure Monitor，请参阅[为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](../azure-monitor/vm/vminsights-configure-workspace.md)。

- 可以创建警报规则来识别环境中的问题，例如资源使用率较高、缺少补丁、磁盘空间不足以及 VM 的检测信号。 还可以通过向 IT 服务管理 (ITSM) 工具发送警报，对检测到的事件设置自动响应。 还可以通过电子邮件发送警报检测通知。 若要创建此类规则，请参阅：
    - [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)。
    - [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/alerts/alerts-log.md)。
    - 用于设置自动操作和通知的[操作规则](../azure-monitor/alerts/alerts-action-rules.md)。
    - [使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具](../azure-monitor/alerts/itsmc-overview.md)。
