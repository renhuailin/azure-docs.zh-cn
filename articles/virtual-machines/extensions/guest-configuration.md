---
title: 来宾配置扩展
description: 了解用于在虚拟机内审核/配置设置的扩展
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88d9ceedffffc3b1ab1b4c00e04f6fbbfe2654a7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433235"
---
# <a name="overview-of-the-guest-configuration-extension"></a>来宾配置扩展概述

来宾配置扩展是一个组件 Azure 策略，它在虚拟机内执行审核和配置操作。
在安装扩展之前，适用于 [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) 和 [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) 的安全基线定义等策略无法检查计算机中的设置。

## <a name="prerequisites"></a>先决条件

对于要向来宾配置服务进行身份验证的计算机，必须有[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
如果已设置以下属性，则符合虚拟机上的标识要求。

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>操作系统

对来宾配置扩展的支持与[记录的端到端解决方案](../../governance/policy/concepts/guest-configuration.md#supported-client-types)的操作系统支持相同。

### <a name="internet-connectivity"></a>Internet 连接

通过来宾配置扩展安装的代理必须能够访问来宾配置分配列出的内容包，并向来宾配置服务报告状态。
计算机可以使用 TCP 端口 443 上的出站 HTTPS 进行连接，或者使用专用网络提供的连接。
若要了解有关专用网络的详细信息，请参阅以下文章：

- [来宾配置，通过 Azure 中的专用链接通信](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [为 Azure 存储使用专用终结点](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>如何安装扩展？

必须将扩展的实例名称设置为“AzurePolicyforWindows”或“AzurePolicyforLinux”，因为上面引用的策略需要这些特定字符串。

默认情况下，所有部署都会更新到最新版本。 除非另外指定，否则属性 _autoUpgradeMinorVersion_ 的值默认为“true”。 发布新版本的扩展时，无需担心更新代码。

### <a name="azure-policy"></a>Azure Policy

要大规模部署最新版本的扩展（包括身份要求），请[指定](../../governance/policy/assign-policy-portal.md) Azure Policy：

[部署先决条件以在虚拟机上启用来宾配置策略](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)。

### <a name="azure-cli"></a>Azure CLI

部署适用于 Linux 的扩展：


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

部署适用于 Windows 的扩展：

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

部署适用于 Linux 的扩展：

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

部署适用于 Windows 的扩展：

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager 模板

部署适用于 Linux 的扩展：

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

部署适用于 Windows 的扩展：

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="bicep"></a>Bicep

部署适用于 Linux 的扩展：

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforLinux'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforLinux'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {}
    protectedSettings: {}
  }
}
```

部署适用于 Windows 的扩展：

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforWindows'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforWindows'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {}
    protectedSettings: {}
  }
}
```

### <a name="terraform"></a>Terraform

部署适用于 Linux 的扩展：

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforLinux"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforLinux"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

部署适用于 Windows 的扩展：

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforWindows"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforWindows"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

## <a name="settings"></a>设置

不需要在扩展上包含任何设置或受保护的设置属性。
代理会从[来宾配置分配](/rest/api/guestconfiguration/guestconfigurationassignments)资源中检索所有此类信息。 例如，[ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation)、[Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode) 和 [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) 属性分别按配置进行托管，而不是在 VM 扩展上托管。

## <a name="next-steps"></a>后续步骤

* 有关 Azure Policy 来宾配置的详细信息，请参阅[了解 Azure Policy 的来宾配置](../../governance/policy/concepts/guest-configuration.md)
* 有关 Linux 代理和扩展工作原理的详细信息，请参阅[适用于 Linux 的 Azure VM 扩展和功能](features-linux.md)。
* 有关 Windows 来宾代理和扩展工作原理的详细信息，请参阅[适用于 Windows 的 Azure VM 扩展和功能](features-windows.md)。  
* 若要安装 Windows 来宾代理，请参阅 [Azure Windows 虚拟机代理概述](agent-windows.md)。  
* 若要安装 Linux 代理，请参阅 [Azure Linux 虚拟机代理概述](agent-linux.md)。  
