---
title: 使用 Azure PowerShell 启用 VM 扩展
description: 本文介绍如何使用 Azure PowerShell 将虚拟机扩展部署到在混合云环境中运行的已启用 Azure Arc 的服务器。
ms.date: 08/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a626e42b5447cafcf0fe99876eb0146a02c25f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734409"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>使用 Azure PowerShell 启用 Azure VM 扩展

本文介绍如何使用 Azure PowerShell 将已启用 Azure Arc 的服务器支持的 Azure VM 扩展部署到 Linux 或 Windows 混合计算机并将其卸载。

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持管理 VM 扩展，也不支持将 VM 扩展部署到 Azure 虚拟机。 对于 Azure VM，请查看下面的 [VM 扩展概述](../../virtual-machines/extensions/overview.md)一文。

## <a name="prerequisites"></a>必备条件

- 配备 Azure PowerShell 的计算机。 有关说明，请参阅[安装和配置 Azure PowerShell](/powershell/azure/)。

使用 Azure PowerShell 在已启用 Arc 的服务器管理的混合服务器上管理 VM 扩展之前，需要安装 `Az.ConnectedMachine` 模块。 在已启用 Arc 的服务器上运行以下命令：

`Install-Module -Name Az.ConnectedMachine`.

安装完成后，将返回以下消息：

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>启用扩展

若要在已启用 Arc 的服务器上启用 VM 扩展，请将 [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) 与 `-Name`、`-ResourceGroupName`、`-MachineName`、`-Location`、`-Publisher`、`ExtensionType` 和 `-Settings` 参数一起使用。

以下示例在已启用 Arc 的 Linux 服务器上启用 Log Analytics VM 扩展：

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

若要在已启用 Arc 的 Windows 服务器上启用 Log Analytics VM 扩展，请在前面的示例中将 `-ExtensionType` 参数的值更改为 `"MicrosoftMonitoringAgent"`。

以下示例将在已启用 Arc 的服务器上启用自定义脚本扩展：

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension"></a>Key Vault VM 扩展 

> [!WARNING]
> PowerShell 客户端通常会将 `\` 添加到 settings.json 中的 `"`，这会导致 akvvm_service 失败，并出现错误：`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

以下示例将在已启用 Arc 的服务器上启用 Key Vault VM 扩展：

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>列出已安装的扩展

若要获取已启用 Arc 的服务器上的 VM 扩展列表，请将 [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 与 `-MachineName` 和 `-ResourceGroupName` 参数一起使用。

示例：

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>删除已安装的扩展

若要删除已启用 Arc 的服务器上安装的 VM 扩展，请将 [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) 与 `-Name`、`-MachineName` 和 `-ResourceGroupName` 参数一起使用。

例如，要删除适用于 Linux 的日志分析 VM 扩展，请运行以下命令：

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>后续步骤

- 你可以使用 [Azure CLI](manage-vm-extensions-cli.md)、[Azure 门户](manage-vm-extensions-portal.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。

- 可以在 [VM 扩展故障排除指南](troubleshoot-vm-extensions.md)中找到故障排除信息。
