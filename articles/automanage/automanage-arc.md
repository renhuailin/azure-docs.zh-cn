---
title: 适用于已启用 Arc 的服务器的 Azure Automanage
description: 了解适用于已启用 Arc 的服务器的 Azure Automanage
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: ce12689548884d4c57363851f7de99b5fb067ef6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456226"
---
# <a name="azure-automanage-for-machines-best-practices---arc-enabled-servers"></a>适用于计算机的 Azure Automanage 最佳做法 - 已启用 Arc 的服务器

在已启用 Arc 的服务器 VM 上使用 Automanage 计算机最佳做法时，将自动为你加入这些 Azure 服务。 它们对于我们的最佳做法白皮书至关重要，你可以在我们的[云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到这些最佳做法。

对于所有这些服务，我们将自动加入、自动配置、监视有无偏移，并在检测到偏移时进行修复。 若要详细了解此过程，请参阅[适用于虚拟机的 Azure Automanage](automanage-virtual-machines.md)。

## <a name="supported-operating-systems"></a>支持的操作系统

对于已启用 Arc 的服务器，Automanage 支持以下操作系统

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+、8
- RHEL 7.4+、8
- Ubuntu 16.04 和 18.04
- SLES 12（仅 SP3-SP5）

## <a name="participating-services"></a>参与服务

|服务    |说明    |支持的环境<sup>1</sup>    |支持的首选项<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[计算机见解监视](../azure-monitor/vm/vminsights-overview.md)    |用于计算机的 Azure Monitor 会监视虚拟机的性能和运行状况，包括它们正在运行的进程和对其他资源的依赖项。 了解[详细信息](../azure-monitor/vm/vminsights-overview.md)。    |生产    |否    |
|[Azure 安全中心](../security-center/security-center-introduction.md)    |Azure 安全中心是一个统一的基础结构安全管理系统，可增强数据中心的安全态势，并跨云中的混合工作负载提供高级威胁防护。 了解[详细信息](../security-center/security-center-introduction.md)。  Automanage 会将 VM 所在的订阅配置为 Azure 安全中心的免费层产品/服务。 如果订阅已加入到 Azure 安全中心，则 Automanage 不会重新配置它。    |生产、开发/测试    |否    |
|[更新管理](../automation/update-management/overview.md)    |可使用 Azure 自动化中的更新管理为计算机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 了解[详细信息](../automation/update-management/overview.md)。    |生产、开发/测试    |否    |
|[更改跟踪和库存](../automation/change-tracking/overview.md) |“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的不同服务、守护程序软件、注册表和文件执行“更改跟踪”，以帮助诊断不需要的更改和引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。  了解[详细信息](../automation/change-tracking/overview.md)。    |生产、开发/测试    |否    |
|[Azure 来宾配置](../governance/policy/concepts/guest-configuration.md)  | 来宾配置策略用于监视配置并报告计算机的合规性。 Automanage 服务将使用“来宾配置”扩展来安装 Azure Linux 基线。 对于 Linux 计算机，来宾配置服务将以仅审核模式安装基线。 你能够看到 VM 与基线不符合的地方，但不会自动修正不符合的项目。 了解[详细信息](../governance/policy/concepts/guest-configuration.md)。    |生产、开发/测试    |否    |
|[Azure 自动化帐户](../automation/automation-create-standalone-account.md)    |Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 了解[详细信息](../automation/automation-intro.md)。    |生产、开发/测试    |否    |
|[Log Analytics 工作区](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor 将日志数据存储在 Log Analytics 工作区中。该工作区是一个 Azure 资源，也是一个用于收集和聚合数据的容器，充当管理边界。 了解[详细信息](../azure-monitor/logs/design-logs-deployment.md)。    |生产、开发/测试    |否    |


<sup>1</sup> 启用 Automanage 时会提供环境选择。 了解[详细信息](automanage-virtual-machines.md#environment-configuration)。 此外，还可以调整环境的默认设置，并在最佳做法约束内设置自己的首选项。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中启用适用于计算机的 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中启用适用于计算机的 Automanage](quick-create-virtual-machines-portal.md)