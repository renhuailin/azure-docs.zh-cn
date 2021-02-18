---
title: 启用了 Azure Arc 的服务器的 VM 扩展管理
description: 启用 Azure Arc 的服务器可以管理虚拟机扩展的部署，这些扩展提供部署后配置和自动化任务和非 Azure Vm。
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: b39149eb7ac572ac3bd50bb6303f28d2340f387d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580853"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>通过已启用 Azure Arc 的服务器进行虚拟机扩展管理

虚拟机 (VM) 扩展是小型应用程序，用于在 Azure Vm 上提供部署后配置和自动化任务。 例如，如果虚拟机要求安装软件、防病毒保护或运行其中的脚本，则可以使用 VM 扩展。

使用启用了 azure Arc 的服务器，可以将 Azure VM 扩展部署到非 Azure Windows 和 Linux Vm，从而简化混合计算机在其生命周期中的管理。 可以在混合计算机上或通过启用了 Arc 的服务器管理的服务器上使用以下方法来管理 VM 扩展：

- [Azure 门户](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [资源管理器模板](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主要优点

支持 Azure Arc 的服务器 VM 扩展支持提供以下主要优势：

- 使用 [Azure 自动化状态配置](../../automation/automation-dsc-overview.md) 集中存储配置，并通过 DSC VM 扩展维护启用混合连接的计算机的所需状态。

- 收集日志数据以便通过 Log Analytics 代理 VM 扩展启用 [Azure Monitor 中的日志](../../azure-monitor/logs/data-platform-logs.md) 进行分析。 这对于跨不同类型的源中的数据进行复杂分析非常有用。

- 在 [用于 VM 的 Azure Monitor](../../azure-monitor/vm/vminsights-overview.md)中，会分析 Windows 和 Linux vm 的性能，并监视其进程和其他资源和外部进程的依赖项。 这是通过同时启用 Log Analytics 代理和依赖项代理 VM 扩展来实现的。

- 使用自定义脚本扩展在混合连接的计算机上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置或管理任务。

- 自动刷新 [Azure Key Vault](../../key-vault/general/overview.md)中存储的证书。

## <a name="availability"></a>可用性

VM 扩展功能仅在 [受支持区域](overview.md#supported-regions)的列表中提供。 确保你在其中一个区域中载入计算机。

## <a name="extensions"></a>扩展

在此版本中，我们在 Windows 和 Linux 计算机上支持以下 VM 扩展。

若要了解有关 Azure 连接的计算机代理包以及有关扩展代理组件的详细信息，请参阅 [代理概述](agent-overview.md#agent-component-details)。

### <a name="windows-extensions"></a>Windows 扩展

|分机 |Publisher |类型 |其他信息 |
|----------|----------|-----|-----------------------|
|Azure Defender 集成漏洞扫描程序 |Qualys |WindowsAgent.AzureSecurityCenter |[Azure 和混合计算机的 azure Defender 集成漏洞评估解决方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自定义脚本扩展 |Microsoft.Compute | CustomScriptExtension |[Windows 自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md)|
|PowerShell DSC |Microsoft PowerShell |DSC |[Windows PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理 |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[适用于 Windows 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-windows.md)|
|用于 VM 的 Azure Monitor (insights)  |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [适用于 Windows 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 证书同步 | 。保管库 |KeyVaultForWindows | [适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor 代理 |Microsoft Azure。监视器 |AzureMonitorWindowsAgent |[ (预览版安装 Azure Monitor 代理) ](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux 扩展

|分机 |Publisher |类型 |其他信息 |
|----------|----------|-----|-----------------------|
|Azure Defender 集成漏洞扫描程序 |Qualys |LinuxAgent.AzureSecurityCenter |[Azure 和混合计算机的 azure Defender 集成漏洞评估解决方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自定义脚本扩展 |Microsoft Azure 扩展 |CustomScript |[Linux 自定义脚本扩展版本2](../../virtual-machines/extensions/custom-script-linux.md) |
|PowerShell DSC |Microsoft.OSTCExtensions |DSCForLinux |[适用于 Linux 的 PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理 |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[适用于 Linux 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-linux.md) |
|用于 VM 的 Azure Monitor (insights)  |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[适用于 Linux 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 证书同步 | 。保管库 |KeyVaultForLinux | [适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor 代理 |Microsoft Azure。监视器 |AzureMonitorLinuxAgent |[ (预览版安装 Azure Monitor 代理) ](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>先决条件

此功能依赖于订阅中的以下 Azure 资源提供程序：

- Microsoft.HybridCompute
- Microsoft.GuestConfiguration

如果尚未注册，请按照 [注册 Azure 资源提供程序](agent-overview.md#register-azure-resource-providers)中的步骤进行操作。

务必查看上表中引用的每个 VM 扩展的文档，以了解它是否有任何网络或系统要求。 这可以帮助你避免在依赖于 VM 扩展的 Azure 服务或功能中遇到任何连接问题。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 扩展

适用于 Linux 的 Log Analytics 代理 VM 扩展需要在目标计算机上安装 Python 2.x。 

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 扩展 (预览版) 

Key Vault VM 扩展 (预览版) 不支持以下 Linux 操作系统：

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

仅支持使用以下内容来部署 Key Vault VM 扩展 (预览版) ：

- Azure CLI
- Azure PowerShell
- Azure 资源管理器模板

在部署扩展之前，需要完成以下操作：

1.  (自签名或导入) [创建保管库和证书](../../key-vault/certificates/quick-create-portal.md)。

2. 向启用了 Azure Arc 的服务器授予对证书机密的访问权限。 如果使用的是 [RBAC 预览](../../key-vault/general/rbac-guide.md)，请搜索 Azure Arc 资源的名称，并为其分配 **Key Vault 机密用户 (预览版)** 角色。 如果你使用 [Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)，请为 Azure Arc 资源的系统分配的标识分配机密 **获取** 权限。

### <a name="connected-machine-agent"></a>Connected Machine 代理

验证计算机是否与支持 Azure 连接的计算机代理的 Windows 和 Linux 操作系统的 [支持版本](agent-overview.md#supported-operating-systems) 相匹配。

Windows 和 Linux 上此功能支持的已连接计算机代理的最低版本为1.0 版。

若要将计算机升级到所需的代理版本，请参阅 [升级代理](manage-agent.md#upgrading-agent)。

## <a name="next-steps"></a>后续步骤

你可以使用 [Azure CLI](manage-vm-extensions-cli.md)、 [Azure PowerShell](manage-vm-extensions-powershell.md)、 [Azure 门户](manage-vm-extensions-portal.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。
