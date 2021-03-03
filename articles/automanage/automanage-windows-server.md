---
title: 适用于 Windows Server 的 Azure Automanage
description: 了解 Azure Automanage for 虚拟机适用于 Windows Server 计算机自动载入和配置的服务的最佳实践。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: a3bb0a2877c71d19b05f424dc44e302c69577b2d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661817"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>适用于虚拟机的 Azure Automanage 最佳做法-Windows Server

当你在 Windows Server VM 上将 Automanage 用于虚拟机 (Vm) 时，这些 Azure 服务会自动载入。 它们对于我们的最佳实践白皮书至关重要，你可以在我们的 [云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到它。

对于所有这些服务，我们将自动载入、自动配置、监视偏移，并在检测到偏差时进行修正。 若要了解有关此过程的详细信息，请参阅 [Azure Automanage for virtual 计算机](automanage-virtual-machines.md)。

## <a name="supported-windows-server-versions"></a>受支持的 Windows Server 版本

Automanage 支持以下 Windows Server 版本：

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure 版本

## <a name="participating-services"></a>参与服务

|服务    |说明    |支持的环境<sup>1</sup>    |支持的首选项<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM Insights 监视    |用于 VM 的 Azure Monitor 监视虚拟机的性能和运行状况，包括其正在运行的进程和其他资源的依赖项。 了解[详细信息](../azure-monitor/vm/vminsights-overview.md)。    |生产    |否    |
|备份    |Azure 备份提供独立且隔离的备份来防止 VM 上的数据被意外破坏。 了解[详细信息](../backup/backup-azure-vms-introduction.md)。 费用基于受保护的 Vm 的数量和大小。 了解[详细信息](https://azure.microsoft.com/pricing/details/backup/)。    |生产    |是    |
|Azure 安全中心    |Azure 安全中心是一种统一的基础结构安全管理系统，它增强了数据中心的安全状况，并跨云中的混合工作负荷提供高级威胁防护。 了解[详细信息](../security-center/security-center-introduction.md)。  Automanage 会将 VM 所驻留的订阅配置为 Azure 安全中心的免费层产品。 如果你的订阅已载入到 Azure 安全中心，则 Automanage 将不会重新配置它。    |生产，开发/测试    |否    |
|Microsoft 反恶意软件    |适用于 Azure 的 Microsoft 反恶意软件是一种免费实时保护，可帮助识别并删除病毒、间谍软件和其他恶意软件。 当已知恶意软件或不需要的软件试图在 Azure 系统上安装自己或运行时，该服务会生成警报。 了解[详细信息](../security/fundamentals/antimalware.md)。 |生产，开发/测试    |是    |
|更新管理    |可以使用 Azure 自动化中的更新管理来管理虚拟机的操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 了解[详细信息](../automation/update-management/overview.md)。    |生产，开发/测试    |否    |
|更改跟踪 & 清单    |“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的服务、守护程序软件、注册表和文件进行更改跟踪，以帮助你诊断不需要的更改并引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。  了解[详细信息](../automation/change-tracking/overview.md)。    |生产，开发/测试    |否    |
|Azure 来宾配置    | 来宾配置策略用于监视配置并报告计算机的符合性。 Automanage 服务将使用来宾配置扩展安装 [Windows 安全基线](/windows/security/threat-protection/windows-security-baselines) 。 对于 Windows 计算机，如果基线设置不符合，来宾配置服务会自动重新应用这些设置。 了解[详细信息](../governance/policy/concepts/guest-configuration.md)。    |生产，开发/测试    |否    |
|Azure 自动化帐户    |Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 了解[详细信息](../automation/automation-intro.md)。    |生产，开发/测试    |否    |
|Log Analytics 工作区    |Azure Monitor 将日志数据存储在 Log Analytics 工作区中。该工作区是一个 Azure 资源，也是一个用于收集和聚合数据的容器，充当管理边界。 了解[详细信息](../azure-monitor/logs/design-logs-deployment.md)。    |生产，开发/测试    |否    |


<sup>1</sup> 当启用 Automanage 时，环境选择可用。 了解[详细信息](automanage-virtual-machines.md#environment-configuration)。 你还可以调整环境的默认设置，并在最佳方案约束内设置你自己的首选项。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中为虚拟机启用 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)