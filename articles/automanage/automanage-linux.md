---
title: 适用于 Linux 的 Azure Automanage
description: 了解 Azure Automanage for 虚拟机适用于适用于 Linux 计算机的自动载入和配置的服务的最佳实践。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: b4ca9a69ab56a81e192560a3a61ec90f82cbbe80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688393"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>适用于虚拟机的 Azure Automanage 最佳做法-Linux

当你在 Linux VM 上对虚拟机 (Vm) 使用 Automanage 时，这些 Azure 服务会自动载入。 它们对于我们的最佳实践白皮书至关重要，你可以在我们的 [云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到它。

对于所有这些服务，我们将自动载入、自动配置、监视偏移，并在检测到偏差时进行修正。 若要了解有关此过程的详细信息，请参阅 [Azure Automanage for virtual 计算机](automanage-virtual-machines.md)。

## <a name="supported-linux-distributions-and-versions"></a>支持的 Linux 分发版和版本

Automanage 支持以下 Linux 分发版和版本：

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16.04 和 18.04
- SLES 12 (SP3-SP5 仅) 

## <a name="participating-services"></a>参与服务

>[!NOTE]
> Linux Vm 目前不支持 Microsoft 反恶意软件。

|服务    |说明    |支持的环境<sup>1</sup>    |支持的首选项<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM Insights 监视    |用于 VM 的 Azure Monitor 监视虚拟机的性能和运行状况，包括其正在运行的进程和其他资源的依赖项。 了解[详细信息](../azure-monitor/vm/vminsights-overview.md)。    |生产    |否    |
|备份    |Azure 备份提供独立且隔离的备份来防止 VM 上的数据被意外破坏。 了解[详细信息](../backup/backup-azure-vms-introduction.md)。 费用基于受保护的 Vm 的数量和大小。 了解[详细信息](https://azure.microsoft.com/pricing/details/backup/)。    |生产    |是    |
|Azure 安全中心    |Azure 安全中心是一种统一的基础结构安全管理系统，它增强了数据中心的安全状况，并跨云中的混合工作负荷提供高级威胁防护。 了解[详细信息](../security-center/security-center-introduction.md)。  Automanage 会将 VM 所驻留的订阅配置为 Azure 安全中心的免费层产品。 如果你的订阅已载入到 Azure 安全中心，则 Automanage 将不会重新配置它。    |生产，开发/测试    |否    |
|更新管理    |可以使用 Azure 自动化中的更新管理来管理虚拟机的操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 了解[详细信息](../automation/update-management/overview.md)。    |生产，开发/测试    |否    |
|更改跟踪 & 清单    |“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的服务、守护程序软件、注册表和文件进行更改跟踪，以帮助你诊断不需要的更改并引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。  了解[详细信息](../automation/change-tracking/overview.md)。    |生产，开发/测试    |否    |
|Azure 来宾配置    | 来宾配置策略用于监视配置并报告计算机的符合性。 Automanage 服务将使用来宾配置扩展安装 Azure Linux 基线。 对于 Linux 计算机，来宾配置服务将在仅审核模式下安装基线。 你将能够看到 VM 与基线不符合的情况，但不会自动修正不相容性。 了解[详细信息](../governance/policy/concepts/guest-configuration.md)。    |生产，开发/测试    |否    |
|Azure 自动化帐户    |Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 了解[详细信息](../automation/automation-intro.md)。    |生产，开发/测试    |否    |
|Log Analytics 工作区    |Azure Monitor 将日志数据存储在 Log Analytics 工作区中。该工作区是一个 Azure 资源，也是一个用于收集和聚合数据的容器，充当管理边界。 了解[详细信息](../azure-monitor/logs/design-logs-deployment.md)。    |生产，开发/测试    |否    |


<sup>1</sup> 当启用 Automanage 时，环境选择可用。 了解[详细信息](automanage-virtual-machines.md#environment-configuration)。 你还可以调整环境的默认设置，并在最佳方案约束内设置你自己的首选项。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中为虚拟机启用 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)