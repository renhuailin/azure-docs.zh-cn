---
title: 通过已启用 Azure Arc 的服务器进行虚拟机扩展管理
description: 已启用 Azure Arc 的服务器可以管理虚拟机扩展的部署，这些扩展向非 Azure VM 提供部署后配置和自动化任务。
ms.date: 08/11/2021
ms.topic: conceptual
ms.openlocfilehash: 20ae8b6cbb29a9a0b43592c3b242707bb2d3add6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727325"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>通过已启用 Azure Arc 的服务器进行虚拟机扩展管理

虚拟机 (VM) 扩展是小型应用程序，可在 Azure VM 上提供部署后配置和自动化任务。 例如，如果虚拟机需要安装软件、进行防病毒保护或运行脚本，便可以使用 VM 扩展。

使用已启用 Azure Arc 的服务器，可以更新 Azure VM 扩展、将其部署到非 Azure Windows 和 Linux VM，也可从中删除，从而简化在混合计算机生命周期内对其进行的管理。 在由已启用 Arc 的服务器管理的混合计算机或服务器上，可以使用以下方法来管理 VM 扩展：

- [Azure 门户](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [资源管理器模板](manage-vm-extensions-template.md)

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持管理 VM 扩展，也不支持将 VM 扩展部署到 Azure 虚拟机。 对于 Azure VM，请查看下面的 [VM 扩展概述](../../virtual-machines/extensions/overview.md)一文。

> [!NOTE]
> 目前只能通过 Azure 门户更新扩展。 目前不支持从 Azure CLI、Azure PowerShell 或使用 Azure 资源管理器模板执行此操作。

## <a name="key-benefits"></a>主要优点

已启用 Azure Arc 的服务器 VM 扩展支持提供了以下主要优势：

- 通过启用 Log Analytics 代理 VM 扩展，使用 [Azure Monitor Logs](../../azure-monitor/logs/data-platform-logs.md) 来收集日志数据进行分析。 Log Analytics 使其有助于在不同类型源的日志数据中进行复杂分析。

- [VM 见解](../../azure-monitor/vm/vminsights-overview.md)分析 Windows 和 Linux VM 的性能状况，并监视这些 VM 在其他资源和外部进程中的进程和依赖关系。 这是通过同时启用 Log Analytics 代理和 Dependency 代理 VM 扩展来实现的。

- 使用自定义脚本扩展在连接的混合计算机上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置或管理任务。

- 自动刷新 [Azure Key Vault](../../key-vault/general/overview.md) 中存储的证书。

## <a name="availability"></a>可用性

VM 扩展功能仅在[受支持区域](overview.md#supported-regions)列表所列的区域中提供。 确保在其中一个区域中加入计算机。

## <a name="extensions"></a>扩展

在此版本中，我们在 Windows 和 Linux 计算机上支持以下 VM 扩展。

若要了解有关 Azure Connected Machine 代理包以及 Extension 代理组件的详细信息，请参阅[代理概述](agent-overview.md#agent-component-details)。

> [!NOTE]
> 对于已启用 Arc 的服务器，删除了针对 DSC VM 扩展的最近支持。 另外，我们建议使用自定义脚本扩展来管理服务器或计算机的部署后配置。

已启用 Arc 的服务器支持在资源组或其他 Azure 订阅之间移动安装了一个或多个 VM 扩展的计算机，而不会影响其配置。 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../../active-directory/develop/quickstart-create-new-tenant.md)中。 有关在处理之前移动资源和注意事项的详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

### <a name="windows-extensions"></a>Windows 扩展

|扩展名 |Publisher |类型 |其他信息 |
|----------|----------|-----|-----------------------|
|Azure Defender 集成漏洞扫描程序 |Qualys |WindowsAgent.AzureSecurityCenter |[适用于 Azure 和混合计算机的 Azure Defender 集成式漏洞评估解决方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自定义脚本扩展 |Microsoft.Compute | CustomScriptExtension |[Windows 自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics 代理 |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[适用于 Windows 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-windows.md)|
|用于 VM 的 Azure Monitor（见解） |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [适用于 Windows 的 Dependency 代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 证书同步 | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor 代理 |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[安装 Azure Monitor 代理（预览版）](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux 扩展

|扩展名 |Publisher |类型 |其他信息 |
|----------|----------|-----|-----------------------|
|Azure Defender 集成漏洞扫描程序 |Qualys |LinuxAgent.AzureSecurityCenter |[适用于 Azure 和混合计算机的 Azure Defender 集成式漏洞评估解决方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自定义脚本扩展 |Microsoft.Azure.Extensions |CustomScript |[Linux 自定义脚本扩展版本 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics 代理 |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[适用于 Linux 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-linux.md) |
|用于 VM 的 Azure Monitor（见解） |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[适用于 Linux 的 Dependency 代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 证书同步 | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor 代理 |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[安装 Azure Monitor 代理（预览版）](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>先决条件

此功能依赖于订阅中的以下 Azure 资源提供程序：

- Microsoft.HybridCompute
- Microsoft.GuestConfiguration

如果尚未注册，请按照[注册 Azure 资源提供程序](agent-overview.md#register-azure-resource-providers)中的步骤进行操作。

务必查看上表中引用的每个 VM 扩展的文档，以了解它是否有任何网络或系统要求。 这可以帮助你避免在 Azure 服务或者在依赖于该 VM 扩展的功能中遭遇任何连接问题。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 扩展

适用于 Linux 的 Log Analytics 代理 VM 扩展需要在目标计算机上安装 Python 2.x。

### <a name="azure-key-vault-vm-extension"></a>Azure Key Vault VM 扩展 

Key Vault VM 扩展不支持以下 Linux 操作系统：

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

仅支持使用以下内容来部署 Key Vault VM 扩展：

- Azure CLI
- Azure PowerShell
- Azure 资源管理器模板

在部署扩展之前，需要完成以下操作：

1. [创建保管库和证书](../../key-vault/certificates/quick-create-portal.md)（自签名或导入）。

2. 向已启用 Azure Arc 的服务器授予对证书机密的访问权限。 如果使用 [RBAC 预览版](../../key-vault/general/rbac-guide.md)，请搜索 Azure Arc 资源的名称，并为其分配“Key Vault 机密用户(预览)”角色。 如果使用 [Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)，请为 Azure Arc 资源系统分配的标识分配机密“Get”权限。

### <a name="connected-machine-agent"></a>Connected Machine 代理

验证计算机是否与适用于 Azure Connected Machine 代理的 Windows 和 Linux 操作系统的[受支持版本](agent-overview.md#supported-operating-systems)相匹配。

Windows 和 Linux 上此功能支持的 Connected Machine 代理的最低版本为 1.0 版。

若要将计算机升级到所需的代理版本，请参阅[升级代理](manage-agent.md#upgrading-agent)。

## <a name="next-steps"></a>后续步骤

可以使用 [Azure CLI](manage-vm-extensions-cli.md)、[Azure PowerShell](manage-vm-extensions-powershell.md) 在 [Azure 门户](manage-vm-extensions-portal.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)中部署、管理和删除 VM 扩展。
