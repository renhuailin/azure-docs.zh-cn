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
ms.openlocfilehash: 97f557ec45530de3f42dd61ee1cded57fd7c33a0
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793739"
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

## <a name="automatic-upgrade-preview"></a>自动升级（预览版）
现已在公共预览版中提供了自动升级依赖项扩展的次要版本的新功能。 若要启用此功能，必须执行以下配置更改。

-   使用[启用预览访问](../automatic-extension-upgrade.md#enabling-preview-access)中的方法之一来为订阅启用该功能。
- 将 `enableAutomaticUpgrade` 属性添加到模板。

Dependency Agent 扩展版本控制方案遵循以下格式：

```
<MM.mm.bb.rr> where M = Major version number, m = minor version number, b = bug number, r = revision number.
```

`enableAutomaticUpgrade` 和 `autoUpgradeMinorVersion` 属性共同确定订阅中虚拟机的升级处理方式。

| enableAutomaticUpgrade | autoUpgradeMinorVersion | 效果 |
|:---|:---|:---|
| true | false | 如果存在更新版本的 bb.rr，则升级依赖项代理。 例如，如果你运行的是 9.6.0.1355，并且较新的版本为 9.6.2.1366，则启用的订阅中的虚拟机将升级到 9.6.2.1366。 |
| true | true |  如果存在较新版本的 mm.bb.rr 或 bb.rr，这会升级依赖项代理。 例如，如果你运行的是 9.6.0.1355，并且较新的版本为 9.7.1.1416，则启用的订阅中的虚拟机将升级到 9.7.1.1416。 同样，如果你运行的是 9.6.0.1355，并且较新的版本为 9.6.2.1366，则启用的订阅中的虚拟机将升级到 9.6.2.1366。 |
| false | true 或 false | 自动升级已禁用。

> [!IMPORTANT]
> 如果将 `enableAutomaticUpgrade` 添加到模板，请确保至少使用 API 版本 2019-12-01。

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

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
