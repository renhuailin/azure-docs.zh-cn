---
title: 概念 - 监视和保护
description: 了解 Azure 原生服务如何帮助保护 Azure VMware 解决方案工作负载。
ms.topic: conceptual
ms.date: 06/14/2021
ms.openlocfilehash: d735e705ffbccaa0e5fb38951c09b01a22818a0c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758136"
---
# <a name="monitor-and-protect-azure-vmware-solution-workloads"></a>监视和保护 Azure VMware 解决方案工作负载

使用 Microsoft Azure 原生服务，可以监视、管理和保护 Azure VMware 解决方案和本地 VM 中的虚拟机 (VM)。 可与 Azure VMware 解决方案集成的 Azure 原生服务包括：

- Log Analytics 工作区是存储日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置。 数据源和解决方案均配置为将其数据存储在特定工作区中。 使用已启用 Azure Arc 的服务器 VM 扩展支持可为新的和现有的 VM 轻松部署 Log Analytics 代理。 
- Azure 安全中心是一个统一的基础结构安全管理系统。 它增强了数据中心的安全性，并为云中或本地的混合工作负载提供高级威胁防护。 它会评估 Azure VMware 解决方案 VM 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以进行解决。 可以在 Azure 安全中心内定义安全策略。 有关详细信息，请参阅[将 Azure 安全中心与 Azure VMware 解决方案集成](azure-security-integration.md)。
- [Azure Monitor](../azure-monitor/vm/vminsights-enable-overview.md) 提供了一个全面的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 它不需要部署。 使用 Azure Monitor 可以监视来宾操作系统性能，并发现和映射 Azure VMware 解决方案或本地 VM 的应用程序依赖项。 通过 Azure Monitor 中的 Log Analytics 工作区可以使用 Log Analytics 代理或扩展启用日志收集和性能计数器收集。 将数据和日志收集到单个点，并将该数据呈现给不同的 Azure 本机服务。
- Azure Arc 可将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。 利用[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)，可以管理 Windows 和 Linux 物理服务器和虚拟机，这些服务器和虚拟机托管在 Azure（或其他云提供商）外部的企业网络中。 可以使用[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 附加在 Azure VMware 解决方案环境中托管的 Kubernetes 群集。 
- Azure 自动化中的 [Azure 更新管理](../automation/update-management/overview.md)可在混合环境中为 Windows 和 Linux 计算机管理操作系统更新。 它会监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区才能使用存储的数据评估 VM 上的更新状态。 
 


## <a name="topology"></a>拓扑

此图显示适用于 Azure VMware 解决方案 VM 的集成监视体系结构。

:::image type="content" source="media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png" alt-text="此图显示集成的 Azure 监视体系结构。" border="false":::

Log Analytics 代理支持收集 Azure、Azure VMware 解决方案和本地 VM 中的日志数据。 日志数据将发送到 Azure Monitor 日志，并存储在 Log Analytics 工作区中。 可以使用启用了 Arc 的服务器 [VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)为新的和现有的 VM 部署 Log Analytics 代理。 

Log Analytics 工作区收集日志后，可以通过 Azure 安全中心配置 Log Analytics 工作区。 Azure 安全中心评估 Azure VMware 解决方案 VM 的漏洞状态，并针对任何严重漏洞发出警报。 例如，它会评估缺少的操作系统补丁、安全错误配置和[终结点保护](../security-center/security-center-services.md)。

可以通过 Azure Sentinel 配置 Log Analytics 工作区，以实现警报检测、威胁可见性、搜寻和威胁响应。 在上图中，Azure 安全中心使用 Azure 安全中心连接器连接到 Azure Sentinel。 Azure 安全中心会将环境漏洞转发到 Azure Sentinel 来创建事件，并与其他威胁进行映射。 你还可以创建“计划的规则”查询来检测不需要的活动，并将其转换为事件。


## <a name="next-steps"></a>后续步骤

现在你已经了解了 Azure VMware 解决方案网络和互连概念，你可能想要了解：

- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)
- [将 Azure 安全中心与 Azure VMware 解决方案集成](azure-security-integration.md)
- [自动化帐户身份验证](../automation/automation-security-overview.md)
- [设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)和 [Azure Monitor](../azure-monitor/overview.md)
- [Azure 安全中心计划](../security-center/security-center-planning-and-operations-guide.md)和[支持的安全中心平台](../security-center/security-center-os-coverage.md)


