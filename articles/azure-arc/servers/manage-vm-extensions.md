---
title: 启用了 Azure Arc 的服务器的 VM 扩展管理
description: 启用 Azure Arc 的服务器可以管理虚拟机扩展的部署，这些扩展提供部署后配置和自动化任务和非 Azure Vm。
ms.date: 12/14/2020
ms.topic: conceptual
ms.openlocfilehash: 55e21f9c6bcd2dfe5f995093034773f2a87d9b03
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504502"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>启用了 Azure Arc 服务器的虚拟机扩展管理

虚拟机 (VM) 扩展是小型应用程序，用于在 Azure Vm 上提供部署后配置和自动化任务。 例如，如果虚拟机要求安装软件、防病毒保护或运行其中的脚本，则可以使用 VM 扩展。

使用启用了 azure Arc 的服务器，可以将 Azure VM 扩展部署到非 Azure Windows 和 Linux Vm，从而简化混合计算机在其生命周期中的管理。 可以在混合计算机上或通过启用了 Arc 的服务器管理的服务器上使用以下方法来管理 VM 扩展：

- [Azure 门户](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [资源管理器模板](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主要优点

支持 Azure Arc 的服务器 VM 扩展支持提供以下主要优势：

- 使用 [Azure 自动化状态配置](../../automation/automation-dsc-overview.md) 集中存储配置，并通过 DSC VM 扩展维护启用混合连接的计算机的所需状态。

- 收集日志数据以便通过 Log Analytics 代理 VM 扩展启用 [Azure Monitor 中的日志](../../azure-monitor/platform/data-platform-logs.md) 进行分析。 这对于跨不同类型的源中的数据进行复杂分析非常有用。

- 在 [用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)中，会分析 Windows 和 Linux vm 的性能，并监视其进程和其他资源和外部进程的依赖项。 这是通过同时启用 Log Analytics 代理和依赖项代理 VM 扩展来实现的。

- 使用自定义脚本扩展在混合连接的计算机上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置或管理任务。

- 自动刷新 [Azure Key Vault](../../key-vault/general/overview.md)中存储的证书。

## <a name="availability"></a>可用性

VM 扩展功能仅在 [受支持区域](overview.md#supported-regions)的列表中提供。 确保你在其中一个区域中载入计算机。

## <a name="extensions"></a>扩展

在此版本中，我们在 Windows 和 Linux 计算机上支持以下 VM 扩展。

|扩展 |OS |发布者 |其他信息 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft PowerShell|[Windows PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理 |Windows |Microsoft.EnterpriseCloud.Monitoring |[适用于 Windows 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft 依赖项代理 | Windows |Microsoft.Compute | [适用于 Windows 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-windows.md)|
|密钥保管库 | Windows | Microsoft.Compute | [适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft Azure 扩展 |[Linux 自定义脚本扩展版本2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[适用于 Linux 的 PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理 |Linux |Microsoft.EnterpriseCloud.Monitoring |[适用于 Linux 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft 依赖项代理 | Linux |Microsoft.Compute | [适用于 Linux 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-linux.md) |
|密钥保管库 | Linux | Microsoft.Compute | [适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md) |

若要了解有关 Azure 连接的计算机代理包以及有关扩展代理组件的详细信息，请参阅 [代理概述](agent-overview.md#agent-component-details)。

## <a name="prerequisites"></a>必备条件

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
- Azure Resource Manager 模板

在部署扩展之前，需要完成以下操作：

1.  (自签名或导入) [创建保管库和证书](../../key-vault/certificates/quick-create-portal.md)。

2. 向启用了 Azure Arc 的服务器授予对证书机密的访问权限。 如果使用的是 [RBAC 预览](../../key-vault/general/rbac-guide.md)，请搜索 Azure Arc 资源的名称，并为其分配 **Key Vault 机密用户 (预览版)** 角色。 如果你使用 [Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)，请为 Azure Arc 资源的系统分配的标识分配机密 **获取** 权限。

### <a name="connected-machine-agent"></a>Connected Machine 代理

验证计算机是否与支持 Azure 连接的计算机代理的 Windows 和 Linux 操作系统的 [支持版本](agent-overview.md#supported-operating-systems) 相匹配。

Windows 和 Linux 上此功能支持的已连接计算机代理的最低版本为1.0 版。

若要将计算机升级到所需的代理版本，请参阅 [升级代理](manage-agent.md#upgrading-agent)。

## <a name="next-steps"></a>后续步骤

你可以使用 [Azure CLI](manage-vm-extensions-cli.md)、 [Azure PowerShell](manage-vm-extensions-powershell.md)、 [Azure 门户](manage-vm-extensions-portal.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。
