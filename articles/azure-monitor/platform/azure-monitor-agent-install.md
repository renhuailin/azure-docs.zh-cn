---
title: 安装 Azure Monitor 代理
description: 用于在 Azure 虚拟机和启用了 Azure Arc 的服务器上安装 Azure Monitor 代理 (AMA) 的选项。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 4c6252b31b4be05ea3c0bcf160a28bf335239b23
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324847"
---
# <a name="install-the-azure-monitor-agent-preview"></a> (预览版安装 Azure Monitor 代理) 
本文提供了当前可用于在 Azure 虚拟机和启用了 Azure Arc 的服务器上安装 [Azure Monitor 代理](azure-monitor-agent-overview.md) 的不同选项，还提供了用于创建 [与数据收集规则关联](data-collection-rule-azure-monitor-agent.md) 的选项，这些规则定义了代理应收集的数据。

## <a name="prerequisites"></a>先决条件
安装 Azure Monitor 代理之前，需要满足以下先决条件。

- 必须在 Azure 虚拟机上启用[托管系统标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)。 这不是启用了 Azure Arc 的服务器所必需的。 如果在 [创建和分配使用 Azure 门户的数据收集规则](#install-with-azure-portal)的过程中安装了代理，系统标识将自动启用。
- 必须在虚拟机的虚拟网络上启用 [AzureResourceManager 服务标记](../../virtual-network/service-tags-overview.md) 。

## <a name="virtual-machine-extension-details"></a>虚拟机扩展详细信息
使用下表中的详细信息将 Azure Monitor 代理实现为 [AZURE VM 扩展](../../virtual-machines/extensions/overview.md) 。 可以使用任何方法安装虚拟机扩展，其中包括本文中所述的虚拟机扩展。

| Property | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft Azure。监视器  | Microsoft Azure。监视器 |
| 类型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>使用 Azure 门户安装
若要使用 Azure 门户安装 Azure Monitor 代理，请按照在 Azure 门户中 [创建数据收集规则](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) 的过程进行操作。 这允许你将数据收集规则与一个或多个 Azure 虚拟机或启用了 Azure Arc 的服务器相关联。 代理将安装在尚未安装该代理的任何虚拟机上。


## <a name="install-with-resource-manager-template"></a>安装资源管理器模板
你可以使用资源管理器模板在 Azure 虚拟机和启用了 Azure Arc 的服务器上安装 Azure Monitor 代理，以及创建与数据收集规则的关联。 创建关联之前，必须创建任何数据收集规则。

获取用于安装代理并创建关联的示例模板： 

- [用于在 Azure 和 Azure Arc (安装 Azure Monitor 代理的模板) ](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [用于创建与数据收集规则关联的模板](../samples/resource-manager-data-collection-rules.md)

使用 [任意部署方法为资源管理器模板](../../azure-resource-manager/templates/deploy-powershell.md) （例如以下命令）安装模板。

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>安装 PowerShell
可以在 Azure 虚拟机和启用了 Azure Arc 的服务器上安装 Azure Monitor 代理，使用 PowerShell 命令添加虚拟机扩展。 

### <a name="azure-virtual-machines"></a>Azure 虚拟机
使用以下 PowerShell 命令在 Azure 虚拟机上安装 Azure Monitor 代理。
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>已启用 Azure Arc 的服务器
使用以下 PowerShell 命令安装 Azure Monitor 代理依赖 Arc 启用的服务器。
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
可以在 Azure 虚拟机和启用了 Azure Arc 的服务器上使用用于添加虚拟机扩展的 Azure CLI 命令安装 Azure Monitor 代理。 

### <a name="azure-virtual-machines"></a>Azure 虚拟机
使用以下 CLI 命令将 Azure Monitor 代理安装在 Azure 虚拟机上。
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>已启用 Azure Arc 的服务器
使用以下 CLI 命令安装 Azure Monitor 代理依赖 Arc 启用的服务器。

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>后续步骤

- [创建数据收集规则](data-collection-rule-azure-monitor-agent.md) 以从代理收集数据并将其发送到 Azure Monitor。
