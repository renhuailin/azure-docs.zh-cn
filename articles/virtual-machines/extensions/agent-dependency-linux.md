---
title: 适用于 Linux 的 Azure Monitor 依赖项虚拟机扩展
description: 使用虚拟机扩展在 Linux 虚拟机上部署 Azure Monitor 依赖项代理。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: ce4b7ee2fea9d5b2b7c92b5ade1b3ad146382214
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744979"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Azure Monitor 依赖项虚拟机扩展

用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 适用于 Linux 的 Azure VM 依赖项代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装依赖项代理。 本文档详细介绍适用于 Linux 的 Azure VM 依赖项代理虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

适用于 Linux 的 Azure VM 依赖项代理可以针对“适用于 VM 的 Azure Monitor”部署文章中的[支持的操作系统](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)部分中列出的支持的操作系统运行。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示了 Azure Linux VM 上 Azure VM 依赖项代理扩展的架构。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| 发布者 | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行 Azure VM 依赖项代理扩展。

用于虚拟机扩展的 JSON 可以嵌套在虚拟机资源中。 或者，可以将其置于资源管理器 JSON 模板的根部或顶层。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。

以下示例假定依赖项代理扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用。 类型反映了嵌套的配置。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将依赖项代理 VM 扩展部署到现有的虚拟机。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5
```

## <a name="automatic-extension-upgrade"></a>自动扩展升级
现已提供[自动升级依赖项扩展次要版本](../automatic-extension-upgrade.md)的新功能。

要为扩展启用自动扩展升级，必须确保将 `enableAutomaticUpgrade` 属性设置为 `true` 并添加到扩展模板。 必须单独在每个 VM 或 VM 规模集上启用此属性。 可使用[启用](../automatic-extension-upgrade.md#enabling-automatic-extension-upgrade)部分所述的其中一种方法为 VM 或 VM 规模集启用该功能。

在 VM 或 VM 规模集中启用自动扩展升级后，每当扩展发布者发布了扩展的新版本，该扩展就会自动升级。 按照[此处](../automatic-extension-upgrade.md#how-does-automatic-extension-upgrade-work)所述的可用性优先原则安全地应用升级。

`enableAutomaticUpgrade` 属性的功能与 `autoUpgradeMinorVersion` 不同。 扩展发布者发布新版本时，`autoUpgradeMinorVersion` 属性不会自动触发次要版本更新。 `autoUpgradeMinorVersion` 属性指示扩展是否应使用更新的次要版本（如果在部署时可用）。 但是，部署后，除非重新部署，否则扩展不会升级次要版本，即使此属性设置为 true 也是如此。

为了使扩展版本保持更新，建议对扩展部署使用 `enableAutomaticUpgrade`。

> [!IMPORTANT]
> 如果将 `enableAutomaticUpgrade` 添加到模板，请确保使用 API 2019-12-01 或更高版本。

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/opt/microsoft/dependency-agent/log/install.log
```

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关如何使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
