---
title: Azure VMware 解决方案 Vm 的生命周期管理
description: 了解如何通过 Microsoft Azure 本机工具来管理 Azure VMware 解决方案 Vm 生命周期的所有方面。
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d8224a37e46b336ebf889fe1c075930f34f10ca4
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988529"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware 解决方案 Vm 的生命周期管理

Microsoft Azure 本机工具可用于监视和管理 Azure 环境中)  (Vm 的虚拟机。 此外，还允许在 Azure VMware 解决方案和本地 Vm 上监视和管理 Vm。 在此概述中，我们将介绍 Azure 提供的集成监视体系结构，以及如何使用其本机工具来管理 Azure VMware 解决方案 Vm 的整个生命周期。

## <a name="benefits"></a>优点

- Azure 本机服务可用于在混合环境中管理 Vm (Azure、Azure VMware 解决方案和本地) 。
- 集成了 Azure、Azure VMware 解决方案和本地 Vm 的监视和可见性。
- 通过 azure Automation 中的 Azure 更新管理，你可以管理 Windows 和 Linux 计算机的操作系统更新。 
- Azure 安全中心提供高级威胁防护，其中包括：
    - 文件完整性监视
    - 无文件安全警报
    - 操作系统修补程序评估
    - 安全配置错误评估
    - 终结点保护评估 
- 使用启用了 Azure Arc 的服务器为新的和现有的 Vm 轻松部署 Log Analytics 代理。 
- Azure Monitor 中的 Log Analytics 工作区使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 将数据和日志收集到一个点，并将这些数据提供给不同的 Azure 本机服务。 
- 增加了 Azure Monitor 的优点： 
    - 无缝监视 
    - 更好的基础结构可见性 
    - 即时通知 
    - 自动解决 
    - 成本效率 

## <a name="integrated-azure-monitoring-architecture"></a>集成的 Azure 监视体系结构

下图显示了适用于 Azure VMware 解决方案 Vm 的集成监视体系结构。

![集成的 Azure 监视体系结构](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>开始之前

如果你不熟悉 Azure 或不熟悉前面提到的任何服务，请查看以下文章：

- [自动化帐户身份验证概述](../automation/automation-security-overview.md)
- [设计 Azure Monitor 日志部署](../azure-monitor/platform/design-logs-deployment.md) 和 [Azure Monitor](../azure-monitor/overview.md)
- Azure 安全中心的[规划](../security-center/security-center-planning-and-operations-guide.md)和[受支持的平台](../security-center/security-center-os-coverage.md)
- [启用用于 VM 的 Azure Monitor 概述](../azure-monitor/insights/vminsights-enable-overview.md)
- [什么是启用了 Azure Arc 的服务器？](../azure-arc/servers/overview.md)[什么是启用 Azure Arc Kubernetes？](../azure-arc/kubernetes/overview.md)
- [更新管理概述](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>集成和部署 Azure 本机服务

### <a name="enable-azure-update-management"></a>启用 Azure 更新管理

Azure Automation 中的 azure 更新管理在混合环境中为 Windows 和 Linux 计算机管理操作系统更新。 它监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区以使用存储的数据来评估 Vm 上的更新状态。

1.  在将 Log Analytics 添加到 Azure 更新管理之前，首先需要 [创建一个 Azure 自动化帐户](../automation/automation-create-standalone-account.md)。 如果你喜欢使用模板创建帐户，请参阅[使用 Azure 资源管理器模板创建自动化帐户](../automation/quickstart-create-automation-account-template.md)。

2. **Log Analytics 工作区** 使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 若要创建 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。 如果愿意，还可以通过 [CLI](../azure-monitor/learn/quick-create-workspace-cli.md)、 [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)或 [Azure 资源管理器模板](../azure-monitor/samples/resource-manager-workspace.md)创建工作区。

3. 若要为 Vm 启用 Azure 更新管理，请参阅 [从自动化帐户启用更新管理](../automation/update-management/enable-from-automation-account.md)。 在此过程中，你需要将 Log Analytics 工作区与自动化帐户相链接。 
 
4. 将 Vm 添加到 Azure 更新管理后，你可以 [在 vm 上部署更新并查看结果](../automation/update-management/deploy-updates.md)。 

### <a name="enable-azure-security-center"></a>启用 Azure 安全中心

Azure 安全中心在云中和本地跨混合工作负荷提供高级威胁防护。 它将评估 Azure VMware 解决方案 Vm 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以解决问题。

Azure 安全中心不需要部署。 有关详细信息，请参阅 [虚拟机支持的功能](../security-center/security-center-services.md)列表。

1. 若要将 Azure VMware 解决方案 Vm 和非 Azure Vm 添加到安全中心，请参阅 [快速入门：设置 Azure 安全中心](../security-center/security-center-get-started.md)。 

2. 从非 Azure 环境中添加 Azure VMware 解决方案 Vm 或 Vm 后，在安全中心启用 Azure Defender。 安全中心将评估 Vm，以发现潜在的安全问题。 它还在 "概述" 选项卡中提供建议。有关详细信息，请参阅 [Azure 安全中心中的安全建议](../security-center/security-center-recommendations.md)。

3. 可以在 Azure 安全中心中定义安全策略。 有关配置安全策略的信息，请参阅使用 [安全策略](../security-center/tutorial-security-policy.md)。

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>将 Vm 载入启用了 Azure Arc 的服务器

Azure Arc 将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。

- 有关为多个 Windows 或 Linux Vm 启用启用了 Azure Arc 的服务器的信息，请参阅 [大规模将混合计算机连接到 azure](../azure-arc/servers/onboard-service-principal.md)。

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>启用了 Arc 的内置混合 Kubernetes 群集 Kubernetes

可以使用启用了 Azure Arc Kubernetes 的 Azure VMware 解决方案环境中托管的 Kubernetes 群集。 

- 有关详细信息，请参阅 [创建启用了 Azure Arc 的载入服务主体](../azure-arc/kubernetes/create-onboarding-service-principal.md)。

### <a name="deploy-the-log-analytics-agent"></a>部署 Log Analytics 代理

可以通过 Log Analytics 代理监视 Azure VMware 解决方案 Vm (也称为 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理) 。 启用 Azure Automation 更新管理时，已创建 Log Analytics 工作区。

- 使用 [启用了 Azure Arc 的服务器](../azure-arc/servers/manage-vm-extensions.md)部署 Log Analytics 代理。

### <a name="enable-azure-monitor"></a>启用 Azure Monitor

Azure Monitor 是一种全面的解决方案，用于从云和本地环境收集、分析和操作遥测数据。 它不需要部署。 利用 Azure Monitor，你可以监视来宾操作系统的性能，并发现和映射 Azure VMware 解决方案或本地 Vm 的应用程序依赖项。

- Azure Monitor 允许收集来自不同源的数据以进行监视和分析。 有关详细信息，请参阅 [Azure Monitor 的监视数据源](../azure-monitor/platform/data-sources.md)。

- 收集各种类型的数据以进行分析、可视化和警报。 有关详细信息，请参阅 [Azure Monitor 数据平台](../azure-monitor/platform/data-platform.md)。

- 若要配置 Log Analytics 工作区 Azure Monitor，请参阅为 [用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](../azure-monitor/insights/vminsights-configure-workspace.md)。

- 你可以创建警报规则，以确定你的环境中的问题，例如资源的高使用、缺少修补程序、磁盘空间不足和 Vm 的检测信号。 你还可以通过向 IT 服务管理 (ITSM) 工具发送警报，来设置对检测到的事件的自动响应。 还可以通过电子邮件发送警报检测通知。 若要创建此类规则，请参阅：
    - [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)。
    - [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/platform/alerts-log.md)。
    - 用于设置自动操作和通知的[操作规则](../azure-monitor/platform/alerts-action-rules.md)。
    - [使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具](../azure-monitor/platform/itsmc-overview.md)。
    
 ## <a name="next-steps"></a>后续步骤

现在，你已介绍使用 Azure 的本机工具来管理 Azure VMware 解决方案 Vm 的整个生命周期，你可能想要了解：

- [通过 Azure 安全中心保护 Azure VMware 解决方案 vm](azure-security-integration.md)。
- [设置 Azure VMware 解决方案的 Azure 备份服务器](set-up-backup-server-for-azure-vmware-solution.md)。
- [将 Azure VMware 解决方案集成到中心和辐射型体系结构中](concepts-hub-and-spoke.md)。
