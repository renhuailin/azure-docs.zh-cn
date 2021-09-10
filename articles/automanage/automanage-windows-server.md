---
title: 适用于 Windows Server 的 Azure Automanage
description: 了解对于针对 Windows Server 计算机自动加入和配置的服务，适用于虚拟机的 Azure Automanage 最佳做法。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 68269b511d101f7c2c346a4bee45aef86bda8fe3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223111"
---
# <a name="azure-automanage-for-machines-best-practices---windows-server"></a>适用于计算机的 Azure Automanage 最佳做法 - Windows Server

在 Windows Server VM 上使用 Automanage 计算机最佳做法时，将自动为你加入这些 Azure 服务。 它们对于我们的最佳做法白皮书至关重要，你可以在我们的[云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到这些最佳做法。

对于所有这些服务，我们将自动加入、自动配置、监视有无偏移，并在检测到偏移时进行修复。 若要详细了解此过程，请参阅[适用于虚拟机的 Azure Automanage](automanage-virtual-machines.md)。

## <a name="supported-windows-server-versions"></a>受支持的 Windows Server 版本

Automanage 支持以下 Windows Server 版本：

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows Server 2022 Azure 版本

## <a name="participating-services"></a>参与服务

|服务    |说明    |支持的环境<sup>1</sup>    |支持的首选项<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[计算机见解监视](../azure-monitor/vm/vminsights-overview.md)    |适用于计算机的 Azure Monitor 监视虚拟机的性能和运行状况，包括它们正在运行的进程和对其他资源的依赖关系。 了解[详细信息](../azure-monitor/vm/vminsights-overview.md)。    |生产    |否    |
|[备份](../backup/backup-overview.md)    |Azure 备份提供独立且隔离的备份来防止计算机上的数据被意外破坏。 了解[详细信息](../backup/backup-azure-vms-introduction.md)。 费用因受保护的 VM 数量和大小而异。 了解[详细信息](https://azure.microsoft.com/pricing/details/backup/)。    |生产    |是    |
|[Azure 安全中心](../security-center/security-center-introduction.md)    |Azure 安全中心是一个统一的基础结构安全管理系统，可增强数据中心的安全态势，并跨云中的混合工作负载提供高级威胁防护。 了解[详细信息](../security-center/security-center-introduction.md)。  Automanage 会将 VM 所在的订阅配置为 Azure 安全中心的免费层产品/服务。 如果订阅已加入到 Azure 安全中心，则 Automanage 不会重新配置它。    |生产、开发/测试    |否    |
|[Microsoft Antimalware](../security/fundamentals/antimalware.md)    |适用于 Azure 的 Microsoft 反恶意软件是一种免费实时保护，可帮助识别并删除病毒、间谍软件和其他恶意软件。 当已知恶意软件或不需要的软件试图在 Azure 系统上安装自己或运行时，该服务会生成警报。 注意：Microsoft Antimalware 要求未安装其他反恶意软件，否则它可能无法运作。  了解[详细信息](../security/fundamentals/antimalware.md)。 |生产、开发/测试    |是    |
|[更新管理](../automation/update-management/overview.md)    |可使用 Azure 自动化中的更新管理为计算机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 了解[详细信息](../automation/update-management/overview.md)。    |生产、开发/测试    |否    |
|[更改跟踪和库存](../automation/change-tracking/overview.md) |“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的不同服务、守护程序软件、注册表和文件执行“更改跟踪”，以帮助诊断不需要的更改和引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。  了解[详细信息](../automation/change-tracking/overview.md)。    |生产、开发/测试    |否    |
|[来宾配置](../governance/policy/concepts/guest-configuration.md) | 来宾配置策略用于监视配置并报告计算机的合规性。 Automanage 服务将使用“来宾配置”扩展来安装 [Windows 安全基线](/windows/security/threat-protection/windows-security-baselines)。 对于 Windows 计算机，如果来宾配置服务不合规，则它们会自动重新应用基线设置。 了解[详细信息](../governance/policy/concepts/guest-configuration.md)。    |生产、开发/测试    |否    |
|[启动诊断](../virtual-machines/boot-diagnostics.md)    | 启动诊断是 Azure 虚拟机 (VM) 的一项调试功能，可用于诊断 VM 启动故障。 启动诊断使用户能够通过收集串行日志信息和屏幕截图来观察其 VM 在启动时的状态。 只会在正在使用托管磁盘的计算机上启用该功能。 |生产、开发/测试    |否    |
|[Windows 管理中心](/windows-server/manage/windows-admin-center/azure/manage-vm)    | 使用 Azure 门户中的 Windows 管理中心（预览版）管理 Azure VM 内的 Windows Server 操作系统。 仅由使用 Windows Server 2016 或更高版本的计算机提供支持。 Automanage 通过专用 IP 地址配置 Windows 管理中心。 如果要通过公共 IP 地址与 Windows 管理中心连接，请为端口 6516 打开入站端口规则。 默认情况下，Automanage 为开发/测试配置文件加入 Windows 管理中心。 使用首选项为生产和开发/测试环境启用或禁用 Windows 管理中心。 |生产、开发/测试    |是    |
|[Azure 自动化帐户](../automation/automation-create-standalone-account.md)    |Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 了解[详细信息](../automation/automation-intro.md)。    |生产、开发/测试    |否    |
|[Log Analytics 工作区](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor 将日志数据存储在 Log Analytics 工作区中。该工作区是一个 Azure 资源，也是一个用于收集和聚合数据的容器，充当管理边界。 了解[详细信息](../azure-monitor/logs/design-logs-deployment.md)。    |生产、开发/测试    |否    |


<sup>1</sup> 启用 Automanage 时会提供环境选择。 了解[详细信息](automanage-virtual-machines.md#environment-configuration)。 此外，还可以调整环境的默认设置，并在最佳做法约束内设置自己的首选项。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中启用适用于计算机的 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中启用适用于计算机 Automanage](quick-create-virtual-machines-portal.md)