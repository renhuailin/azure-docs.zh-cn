---
title: 适用于虚拟机的 Azure Automanage 最佳做法
description: 了解对于自动载入和配置的服务，适用于虚拟机的 Azure Automanage 最佳做法。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: c6dd93bc492c8823f5b97e51fce66196502aa36c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769676"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>适用于虚拟机的 Azure Automanage 最佳做法


为虚拟机使用 Automanage 时，将自动载入这些 Azure 服务。 它们对于我们的最佳做法白皮书至关重要，你可以在我们的[云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到这些最佳做法。

对于所有这些服务，我们将自动载入、自动配置、监视偏移，并在检测到偏移时进行协调。 若要详细了解此过程，请参阅[适用于虚拟机的 Azure Automanage](automanage-virtual-machines.md)。


## <a name="participating-services"></a>参与服务

|服务    |说明    |支持的配置文件<sup>1</sup>    |支持的首选项<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM 见解监视    |用于 VM 的 Azure Monitor 监视虚拟机的性能和运行状况，包括它们正在运行的进程和对其他资源的依赖关系。 了解[详细信息](../azure-monitor/vm/vminsights-overview.md)。    |Azure VM 最佳做法 - 生产    |否    |
|备份    |Azure 备份提供独立且隔离的备份来防止 VM 上的数据被意外破坏。 了解[详细信息](../backup/backup-azure-vms-introduction.md)。 费用因受保护的 VM 数量和大小而异。 了解[详细信息](https://azure.microsoft.com/pricing/details/backup/)。    |Azure VM 最佳做法 - 生产    |是    |
|Azure 安全中心    |Azure 安全中心是一个统一的基础结构安全管理系统，可增强数据中心的安全态势，并跨云中的混合工作负载提供高级威胁防护。 了解[详细信息](../security-center/security-center-introduction.md)。  Automanage 会将 VM 所在的订阅配置为 Azure 安全中心的免费层产品/服务。 如果订阅已载入到 Azure 安全中心，则自动管理将不会重新配置它。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |
|Microsoft 反恶意软件    |适用于 Azure 的 Microsoft 反恶意软件是一种免费实时保护，可帮助识别并删除病毒、间谍软件和其他恶意软件。 当已知恶意软件或不需要的软件试图在 Azure 系统上安装自己或运行时，该服务会生成警报。 了解[详细信息](../security/fundamentals/antimalware.md)。 |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |是    |
|更新管理    |可以使用 Azure 自动化中的更新管理为虚拟机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 了解[详细信息](../automation/update-management/overview.md)。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |
|更改跟踪和库存    |“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的不同服务、守护程序软件、注册表和文件执行“更改跟踪”，以帮助诊断不需要的更改和引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。  了解[详细信息](../automation/change-tracking/overview.md)。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |
|来宾配置 | 来宾配置用于监视配置并报告计算机的合规性。 Automanage 服务将使用“来宾配置”扩展来安装 [Windows 安全基线](/windows/security/threat-protection/windows-security-baselines)。 了解[详细信息](../governance/policy/concepts/guest-configuration.md)。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |
|Azure 自动化帐户    |Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 了解[详细信息](../automation/automation-intro.md)。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |
|Log Analytics 工作区    |Azure Monitor 将日志数据存储在 Log Analytics 工作区中。该工作区是一个 Azure 资源，也是一个用于收集和聚合数据的容器，充当管理边界。 了解[详细信息](../azure-monitor/logs/design-logs-deployment.md)。    |Azure VM 最佳做法 - 生产、Azure VM 最佳做法 - 开发/测试    |否    |


<sup>1</sup> 启用 Automanage 时，配置文件可用。 了解[详细信息](automanage-virtual-machines.md)。 还可以调整配置文件的默认设置，并在最佳做法约束内设置你自己的首选项。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中启用适用于虚拟机的 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中启用适用于虚拟机的 Automanage](quick-create-virtual-machines-portal.md)