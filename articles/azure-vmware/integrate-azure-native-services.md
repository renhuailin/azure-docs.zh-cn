---
title: 使用 Azure 原生服务监视和保护 VM
description: 了解如何集成和部署 Microsoft Azure 原生工具，以监视和管理 Azure VMware 解决方案工作负载。
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: 19453eeb652a0303e0447a80e37b63613aa92255
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799313"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>使用 Azure 原生服务监视和保护 VM

Microsoft Azure 原生服务可让你监视、管理和保护混合环境（Azure、Azure VMware 解决方案和本地）中的虚拟机 (VM)。 在本文中，你将在 Azure VMware 解决方案私有云中集成 Azure 原生服务。 你还将了解如何使用工具在 VM 的整个生命周期内对其进行管理。 

可与 Azure VMware 解决方案集成的 Azure 原生服务包括：

- Azure Arc 可将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。 利用[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)，可以管理 Windows 和 Linux 物理服务器和虚拟机，这些服务器和虚拟机托管在 Azure（或其他云提供商）外部的企业网络中。 可以使用[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 附加在 Azure VMware 解决方案环境中托管的 Kubernetes 群集。 

- Azure Monitor 用于收集、分析和响应来自云和本地环境的遥测数据。 它不需要部署。  你可以监视来宾操作系统性能，以发现和映射 Azure VMware 解决方案或本地 VM 的应用程序依赖项。 通过 Azure Monitor 中的 Log Analytics 工作区可以使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 

   使用 Azure Monitor，你可以[收集不同源的数据进行监视和分析](../azure-monitor/agents/data-sources.md)，还可以[收集不同类型的数据进行分析、可视化和预警](../azure-monitor/data-platform.md)。 还可以创建警报规则来识别环境中的问题，例如资源使用率较高、缺少补丁、磁盘空间不足以及 VM 的检测信号。 可以通过向 IT 服务管理 (ITSM) 工具发送警报，对检测到的事件设置自动响应。 还可以通过电子邮件发送警报检测通知。

- Azure 安全中心增强了数据中心的安全性，并为云中或本地的混合工作负荷提供高级威胁防护。 它会评估 Azure VMware 解决方案 VM 的漏洞，必要时会引发警报，并会将警报转发到 Azure Monitor 以获取解决方法。 例如，它会评估缺少的操作系统补丁、安全错误配置和[终结点保护](../security-center/security-center-services.md)。 你也可以在 [Azure 安全中心](azure-security-integration.md)定义安全策略。

- Azure 更新管理可在 Azure 自动化的混合环境中为 Windows 和 Linux 计算机管理操作系统更新。 它会监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区才能使用存储的数据评估 VM 上的更新状态。

- Log Analytics 工作区存储日志数据。 每个工作区都有其自己的数据存储库和配置，用于存储数据。 可以通过 Log Analytics 代理监视 Azure VMware 解决方案 VM。 连接到 Log Analytics 工作区的虚拟机使用 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)收集有关对受监视服务器上的已安装软件、Microsoft 服务、Windows 注册表和文件以及 Linux 守护程序进行的更改的数据。 数据可用时，代理会将其发送到 Azure Monitor Logs 进行处理。 Azure Monitor Logs 将逻辑应用于接收的数据，记录这些数据并使其可用于分析。 请使用已启用 Azure Arc 的服务器的 [VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)在 VM 上部署 Log Analytics 代理。 



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


## <a name="topology"></a>拓扑

此图显示适用于 Azure VMware 解决方案 VM 的集成监视体系结构。

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="此图显示集成的 Azure 监视体系结构。" border="false":::

Log Analytics 代理支持收集 Azure、Azure VMware 解决方案和本地 VM 中的日志数据。 日志数据将发送到 Azure Monitor 日志，并存储在 Log Analytics 工作区中。 可以使用启用了 Arc 的服务器 [VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)为新的和现有的 VM 部署 Log Analytics 代理。 

Log Analytics 工作区收集日志后，你可以使用 Azure 安全中心来配置 Log Analytics 工作区，以评估 Azure VMware 解决方案 VM 的漏洞状态，并针对任何关键漏洞发出警报。  例如，它会评估缺少的操作系统补丁、安全错误配置和[终结点保护](../security-center/security-center-services.md)。

可以通过 Azure Sentinel 配置 Log Analytics 工作区，以实现警报检测、威胁可见性、搜寻和威胁响应。 在上图中，Azure 安全中心使用 Azure 安全中心连接器连接到 Azure Sentinel。 Azure 安全中心将环境漏洞转发到 Azure Sentinel 来创建事件，并与其他威胁进行映射。 你还可以创建“计划的规则”查询来检测不需要的活动，并将其转换为事件。


## <a name="before-you-start"></a>开始之前

如果你是刚接触 Azure 或不熟悉前面提到的任何服务，请查看以下文章：

- [自动化帐户身份验证概述](../automation/automation-security-overview.md)
- [设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)和 [Azure Monitor](../azure-monitor/overview.md)
- Azure 安全中心的[计划](../security-center/security-center-planning-and-operations-guide.md)和[受支持平台](../security-center/security-center-os-coverage.md)
- [启用用于 VM 的 Azure Monitor 概述](../azure-monitor/vm/vminsights-enable-overview.md)
- [什么是已启用 Azure Arc 的服务器？](../azure-arc/servers/overview.md)和[什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [更新管理概述](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>启用 Azure 更新管理

Azure 自动化中的 [Azure 更新管理](../automation/update-management/overview.md)可以管理混合环境中 Windows 和 Linux 计算机的操作系统更新。 它会监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区才能使用存储的数据评估 VM 上的更新状态。

1. 首先需要[创建 Azure 自动化帐户](../automation/automation-create-standalone-account.md)，然后才能将 Log Analytics 工作区添加到 Azure 更新管理。 

   >[!TIP]
   >可以[使用 Azure 资源管理器 (ARM) 模板创建自动化帐户](../automation/quickstart-create-automation-account-template.md)。 与其他部署方法相比，使用 ARM 模板需要的步骤更少。

1. [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 如果愿意，还可以通过 [CLI](../azure-monitor/logs/resource-manager-workspace.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 或 [Azure 资源管理器模板](../azure-monitor/logs/resource-manager-workspace.md)创建工作区。

1. [从自动化帐户启用更新管理](../automation/update-management/enable-from-automation-account.md)。 在该过程中，需将 Log Analytics 工作区与自动化帐户链接。 
 
1. 启用更新管理后，可以[在 VM 上部署更新并查看结果](../automation/update-management/deploy-updates.md)。 


## <a name="enable-azure-security-center"></a>启用 Azure 安全中心

评估 Azure VMware 解决方案 VM 的漏洞，必要时引发警报。 这些安全警报可以转发到 Azure Monitor 以进行解决。 有关详细信息，请参阅 [VM 支持的功能](../security-center/security-center-services.md)。

Azure 安全中心提供许多功能，包括：
- 文件完整性监视
- 无文件攻击检测
- 操作系统补丁评估 
- 安全配置错误评估
- 终结点保护评估

>[!NOTE]
>Azure 安全中心是一个预配置的工具，不需要部署它，但需要在 Azure 门户启用它。 


1. [将 Azure VMware 解决方案 VM 添加到安全中心](azure-security-integration.md#add-azure-vmware-solution-vms-to-security-center)。 

2. [在安全中心启用 Azure Defender](../security-center/enable-azure-defender.md)。 安全中心会评估 VM 中是否存在潜在安全问题。 它还会在“概述”选项卡中提供[安全建议](../security-center/security-center-recommendations.md)。 

3. 在 Azure 安全中心[定义安全策略](../security-center/tutorial-security-policy.md)。 

有关详细信息，请参阅[将 Azure 安全中心与 Azure VMware 解决方案集成](azure-security-integration.md)。



## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>将 VM 加入已启用 Azure Arc 的服务器

将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。  有关为多个 Windows 或 Linux VM 启用已启用 Azure Arc 的服务器的信息，请参阅[将混合计算机大规模连接到 Azure](../azure-arc/servers/onboard-service-principal.md)。



## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>使用已启用 Arc 的 Kubernetes 加入混合 Kubernetes 群集


使用已启用 Azure Arc 的 Kubernetes 附加在 Azure VMware 解决方案环境中托管的 Kubernetes 群集。 有关详细信息，请参阅[创建已启用 Azure Arc 的加入服务主体](../azure-arc/kubernetes/create-onboarding-service-principal.md)。


## <a name="deploy-the-log-analytics-agent"></a>部署 Log Analytics 代理

通过 Log Analytics 代理监视 Azure VMware 解决方案 VM。 连接到 Log Analytics 工作区的虚拟机使用 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)收集有关已安装软件、Microsoft 服务、Windows 注册表和文件以及受监视服务器上的 Linux 守护程序的更改数据。 数据可用时，代理会将其发送到 Azure Monitor Logs 进行处理。 Azure Monitor Logs 将逻辑应用于接收的数据，记录这些数据并使其可用于分析。

使用[已启用 Azure Arc 的服务器 VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)部署 Log Analytics 代理。




## <a name="enable-azure-monitor"></a>启用 Azure Monitor

可以[收集不同源的数据进行监视和分析](../azure-monitor/agents/data-sources.md)，还可以[收集不同类型的数据进行分析、可视化和预警](../azure-monitor/data-platform.md)。 还可以创建警报规则来识别环境中的问题，例如资源使用率较高、缺少补丁、磁盘空间不足以及 VM 的检测信号。 可以通过向 IT 服务管理 (ITSM) 工具发送警报，对检测到的事件设置自动响应。 还可以通过电子邮件发送警报检测通知。

监视来宾操作系统性能，以发现和映射 Azure VMware 解决方案或本地 VM 的应用程序依赖项。 通过 Azure Monitor 中的 Log Analytics 工作区可以使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 


1. [设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)

1. [启用用于 VM 的 Azure Monitor 概述](../azure-monitor/vm/vminsights-enable-overview.md)

1. [为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](../azure-monitor/vm/vminsights-configure-workspace.md)。

1. 创建警报规则以识别环境中的问题：

   - [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)。

   - [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/alerts/alerts-log.md)。

   - 用于设置自动操作和通知的[操作规则](../azure-monitor/alerts/alerts-action-rules.md)。

   - [使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具](../azure-monitor/alerts/itsmc-overview.md)。


## <a name="next-steps"></a>后续步骤

了解 Azure VMware 解决方案网络和互连概念以后，就可以了解如何[将 Azure 安全中心与 Azure VMware 解决方案集成](azure-security-integration.md)了。