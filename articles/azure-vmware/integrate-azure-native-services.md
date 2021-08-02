---
title: 集成和部署 Azure 原生服务
description: 了解如何集成和部署 Microsoft Azure 原生工具，以监视和管理 Azure VMware 解决方案工作负载。
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: e7af5617ee62f451f57daf806f70c7c433e40612
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758065"
---
# <a name="integrate-and-deploy-azure-native-services"></a>集成和部署 Azure 原生服务

Microsoft Azure 原生服务可让你监视、管理和保护混合环境（Azure、Azure VMware 解决方案和本地）中的虚拟机 (VM)。 有关详细信息，请参阅 [VM 支持的功能](../security-center/security-center-services.md)。

可与 Azure VMware 解决方案集成的 Azure 原生服务包括：

- Log Analytics 工作区：每个工作区都有自身的数据存储库，以及用于存储日志数据的配置。 数据源和解决方案均配置为将其数据存储在特定工作区中。 使用已启用 Azure Arc 的服务器 VM 扩展支持可为新的和现有的 VM 轻松部署 Log Analytics 代理。 
- Azure 安全中心：统一的基础结构安全管理系统，可增强数据中心的安全性，并跨云中或本地的混合工作负载提供高级威胁防护。 
- Azure Sentinel 是云原生的安全信息事件管理 (SIEM) 解决方案。 它在整个环境中提供安全分析、警报检测和自动威胁响应。 Azure Sentinel 构建在 Log Analytics 工作区的基础之上。
- Azure Arc：将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。 
- Azure 更新管理：管理混合环境中 Windows 和 Linux 计算机的操作系统更新。
- Azure Monitor：综合性的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 它不需要部署。 

在本文中，你将在 Azure VMware 解决方案私有云中集成 Azure 原生服务。 你还将了解如何使用工具在 VM 的整个生命周期内对其进行管理。 


## <a name="enable-azure-update-management"></a>启用 Azure 更新管理

Azure 自动化中的 [Azure 更新管理](../automation/update-management/overview.md)可以管理混合环境中 Windows 和 Linux 计算机的操作系统更新。 它会监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区才能使用存储的数据评估 VM 上的更新状态。

1. [创建 Azure 自动化帐户](../automation/automation-create-standalone-account.md)。 

   >[!TIP]
   >可以[使用 Azure 资源管理器 (ARM) 模板创建自动化帐户](../automation/quickstart-create-automation-account-template.md)。 与其他部署方法相比，使用 ARM 模板需要的步骤更少。

1. [从自动化帐户启用更新管理](../automation/update-management/enable-from-automation-account.md)。  这会将 Log Analytics 工作区链接到自动化帐户。 此外，还会在更新管理中启用 Azure VM 和非 Azure VM。

   - 如果你有工作区，请选择“更新管理”。 选择 Log Analytics 工作区和自动化帐户，然后选择“启用”。 安装最多需要 15 分钟才能完成。

   - 如果你要设置新的 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 还可以使用 [CLI](../azure-monitor/logs/quick-create-workspace-cli.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 或 [Azure 资源管理器模板](../azure-monitor/logs/resource-manager-workspace.md)创建工作区。
 
1. 启用更新管理后，可以[在 VM 上部署更新并查看结果](../automation/update-management/deploy-updates.md)。 


## <a name="enable-azure-security-center"></a>启用 Azure 安全中心

Azure 安全中心为 Azure VMware 解决方案和本地虚拟机 (VM) 提供高级威胁防护。 它会评估 Azure VMware 解决方案 VM 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以进行解决。 

Azure 安全中心提供许多功能，包括：
- 文件完整性监视
- 无文件攻击检测
- 操作系统补丁评估 
- 安全配置错误评估
- 终结点保护评估

>[!NOTE]
>Azure 安全中心是一个预配置的工具，不需要部署它，但需要在 Azure 门户启用它。 

若要启用 Azure 安全中心，请参阅[将 Azure 安全中心与 Azure VMware 解决方案集成](azure-security-integration.md)。

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>将 VM 加入已启用 Azure Arc 的服务器

Azure Arc 可将 Azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案和本地基础结构。  利用[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)，可以管理 Windows 和 Linux 物理服务器和虚拟机，这些服务器和虚拟机托管在 Azure（或其他云提供商）外部的企业网络中。

有关为多个 Windows 或 Linux VM 启用已启用 Azure Arc 的服务器的信息，请参阅[将混合计算机大规模连接到 Azure](../azure-arc/servers/onboard-service-principal.md)。

## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>使用已启用 Arc 的 Kubernetes 加入混合 Kubernetes 群集

利用[启用了 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md)，可以附加托管在 Azure VMware 解决方案环境中的 Kubernetes 群集。 

有关详细信息，请参阅[创建已启用 Azure Arc 的加入服务主体](../azure-arc/kubernetes/create-onboarding-service-principal.md)。

## <a name="deploy-the-log-analytics-agent"></a>部署 Log Analytics 代理

可以通过 Log Analytics 代理监视 Azure VMware 解决方案 VM。 连接到 Log Analytics 工作区的虚拟机使用 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)收集有关已安装软件、Microsoft 服务、Windows 注册表和文件以及受监视服务器上的 Linux 守护程序的更改数据。 数据可用时，代理会将其发送到 Azure Monitor Logs 进行处理。 Azure Monitor Logs 将逻辑应用于接收的数据，记录这些数据并使其可用于分析。

使用[已启用 Azure Arc 的服务器 VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)部署 Log Analytics 代理。

## <a name="enable-azure-monitor"></a>启用 Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) 是一个综合性的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 Azure Monitor 的部分新增优势包括： 

   - 无缝监视 

   - 更好的基础结构可见性 

   - 即时通知 

   - 自动解决 

   - 成本效益 

可以收集来自不同源的数据以进行监视和分析。 有关详细信息，请参阅 [Azure Monitor 的监视数据源](../azure-monitor/agents/data-sources.md)。  还可以收集不同类型的数据以进行分析、可视化和发出警报。 有关详细信息，请参阅 [Azure Monitor 数据平台](../azure-monitor/data-platform.md)。 

可以监视来宾操作系统性能，并发现和映射 Azure VMware 解决方案或本地 VM 的应用程序依赖项。 还可以创建警报规则来识别环境中的问题，例如资源使用率较高、缺少补丁、磁盘空间不足以及 VM 的检测信号。 可以通过向 IT 服务管理 (ITSM) 工具发送警报，对检测到的事件设置自动响应。 还可以通过电子邮件发送警报检测通知。


1. [设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)

1. [启用用于 VM 的 Azure Monitor 概述](../azure-monitor/vm/vminsights-enable-overview.md)

1. [为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](../azure-monitor/vm/vminsights-configure-workspace.md)。

1. 创建警报规则以识别环境中的问题：
   - [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)。
   - [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/alerts/alerts-log.md)。
   - 用于设置自动操作和通知的[操作规则](../azure-monitor/alerts/alerts-action-rules.md)。
   - [使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具](../azure-monitor/alerts/itsmc-overview.md)。