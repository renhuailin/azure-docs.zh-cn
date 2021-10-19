---
title: 安装 Azure Monitor 代理
description: 在 Azure 虚拟机和已启用 Azure Arc 的服务器上安装 Azure Monitor 代理 (AMA) 的选项。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 13e025845dd4fbd51519f85f1879cc11c6ea102d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706896"
---
# <a name="install-the-azure-monitor-agent"></a>安装 Azure Monitor 代理
本文提供在 Azure 虚拟机和已启用 Azure Arc 的服务器上安装 [Azure Monitor 代理](azure-monitor-agent-overview.md)当前可采用的不同选项，还提供用于创建[数据收集规则关联](data-collection-rule-azure-monitor-agent.md)的选项，这些规则将定义代理应收集的数据。

## <a name="prerequisites"></a>先决条件
安装 Azure Monitor 代理之前，需要满足以下先决条件。

- 必须在 Azure 虚拟机上启用[托管系统标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)。 对于已启用 Azure Arc 的服务器，这一点不是必需要求。 如果在[使用 Azure 门户创建和分配数据收集规则](#install-with-azure-portal)的过程中安装代理，系统标识将自动启用。
- 必须在虚拟机的虚拟网络上启用 [AzureResourceManager 服务标记](../../virtual-network/service-tags-overview.md)。
- 虚拟机必须具有以下 HTTPS 终结点的访问权限：
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> Azure Monitor 代理当前不支持专用链接。

## <a name="virtual-machine-extension-details"></a>虚拟机扩展详细信息
Azure Monitor 代理以 [Azure VM 扩展](../../virtual-machines/extensions/overview.md)方式实现，详细信息如下表所示。 可以使用任何方法来安装虚拟机扩展，其中包括本文中所述的方法。

| 属性 | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| 类型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>扩展版本
强烈建议更新到 GA+ 版本，而不是使用预览版。

| 发布日期 | 发行说明 | Windows | Linux |
|:---|:---|:---|:---|:---|
| 2021 年 6 月 | 宣布正式发布。 <ul><li>除指标目标之外的所有功能现已正式发布</li><li>生产质量、安全性和符合性</li><li>在所有公共区域推出</li><li>针对更高 EPS 的性能和缩放性方面的改进</li></ul> [了解详细信息](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| 2021 年 7 月 | <ul><li>直接代理支持</li><li>Log Analytics 网关支持</li></ul> [了解详细信息](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| 2021 年 8 月 | 修复了允许将 Azure Monitor 指标用作唯一目标的问题 | 1.1.2.0 | 1.10.9.0（请勿使用 1.10.7.0） |
| 2021 年 9 月 | 修复了重启代理时导致数据丢失的问题 | 1.1.3.1 | 1.12.2.0 |


## <a name="install-with-azure-portal"></a>使用 Azure 门户安装
要使用 Azure 门户安装 Azure Monitor 代理，请按照在 Azure 门户中[创建数据收集规则](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)的过程进行操作。 这样可以将数据收集规则与一个或多个 Azure 虚拟机或已启用 Azure Arc 的服务器相关联。 代理将安装到尚未安装该代理的任何虚拟机上。


## <a name="install-with-resource-manager-template"></a>使用资源管理器模板安装
可以使用资源管理器模板在 Azure 虚拟机和已启用 Azure Arc 的服务器上安装 Azure Monitor 代理，以及创建数据收集规则关联。 创建关联之前，必须先创建任何数据收集规则。

有关安装代理和创建关联的示例模板，请参阅以下内容： 

- [安装 Azure Monitor 代理的模板（Azure 和 Azure Arc）](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [创建数据收集规则关联的模板](./resource-manager-data-collection-rules.md)

使用[任何部署资源管理器模板的方法](../../azure-resource-manager/templates/deploy-powershell.md)安装模板，例如以下命令。

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>使用 PowerShell 安装
可以使用 PowerShell 命令添加虚拟机扩展，由此在 Azure 虚拟机和已启用 Azure Arc 的服务器上安装 Azure Monitor 代理。 

### <a name="azure-virtual-machines"></a>Azure 虚拟机
使用以下 PowerShell 命令在 Azure 虚拟机上安装 Azure Monitor 代理。
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>已启用 Azure Arc 的服务器
使用以下 PowerShell 命令在已启用 Azure Arc 的服务器上安装 Azure Monitor 代理。
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 命令添加虚拟机扩展，由此在 Azure 虚拟机和已启用 Azure Arc 的服务器上安装 Azure Monitor 代理。 

### <a name="azure-virtual-machines"></a>Azure 虚拟机
使用以下 CLI 命令在 Azure 虚拟机上安装 Azure Monitor 代理。
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
使用以下 CLI 命令在已启用 Azure Arc 的服务器上安装 Azure Monitor 代理。

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="next-steps"></a>后续步骤

- [创建数据收集规则](data-collection-rule-azure-monitor-agent.md)，通过代理收集数据并将其发送给 Azure Monitor。
