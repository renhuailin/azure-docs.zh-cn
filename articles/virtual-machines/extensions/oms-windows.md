---
title: 适用于 Windows 的 Log Analytics 虚拟机扩展
description: 使用虚拟机扩展在 Windows 虚拟机上部署 Log Analytics 代理。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0a7dc06feed0593c616c62dbb8b10fc58cb063b8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280128"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>适用于 Windows 的 Log Analytics 虚拟机扩展

Azure Monitor 日志提供跨云和本地资产的监视功能。 适用于 Windows 的 Log Analytics 代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 本文档详细介绍适用于 Windows 的 Log Analytics 虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

有关支持的 Windows 操作系统的详细信息，请参阅 [Azure Monitor 代理的概述](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)一文。

### <a name="agent-and-vm-extension-version"></a>代理和 VM 扩展版本
下表提供了每次发布的 Windows Log Analytics VM 扩展和 Log Analytics 代理捆绑包的版本映射。 

| Log Analytics Windows 代理捆绑包版本 | Log Analytics Windows VM 扩展版本 | 发布日期 | 发行说明 |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | 2020 年 10 月   | <ul><li>新代理疑难解答</li><li>代理如何处理 Azure 服务的证书更改的更新</li></ul> |
| 10.20.18040 | 1.0.18040.2 | 2020 年 8 月   | <ul><li>解决 Azure Arc 问题</li></ul> |
| 10.20.18038 | 1.0.18038 | 2020 年 4 月   | <ul><li>使用 Azure Monitor 专用链接作用域启用通过专用链接建立连接</li><li>添加引入限制，以避免在引入工作区时突然出现意外流入</li><li>添加对其他 Azure 政府云和区域的支持</li><li>解决 HealthService.exe 出现故障的 bug</li></ul> |
| 10.20.18029 | 1.0.18029 | 2020 年 3 月   | <ul><li>添加 SHA-2 代码签名支持</li><li>改进了 VM 扩展安装和管理</li><li>解决了用于服务器的 Azure Arc 集成中的 Bug</li><li>添加了用于客户支持的内置故障排除工具</li><li>添加对其他 Azure 政府区域的支持</li> |
| 10.20.18018 | 1.0.18018 | 2019 年 10 月 | <ul><li> 次要 bug 修复和稳定性改进 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019 年 7 月 | <ul><li> 次要 bug 修复和稳定性改进 </li><li> MaxExpressionDepth 已增加到10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 年 6 月 | <ul><li> 次要 bug 修复和稳定性改进 </li><li> 添加了在建立代理连接时禁用默认凭据的功能（支持 WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH） </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019 年 3 月 | <ul><li>次要稳定性修复 </li></ul> |
| 10.19.10006 | 不适用 | 2018 年 12 月 | <ul><li> 次要稳定性修复 </li></ul> | 
| 8.0.11136 | 不适用 | 2018 年 9 月 |  <ul><li> 添加了对 VM 移动时检测资源 ID 更改的支持 </li><li> 添加了对使用非扩展安装时报告资源 ID 的支持 </li></ul>| 
| 8.0.11103 | 不适用 |  2018 年 4 月 | |
| 8.0.11081 | 1.0.11081 | 2017 年 11 月 | | 
| 8.0.11072 | 1.0.11072 | 2017 年 9 月 | |
| 8.0.11049 | 1.0.11049 | 2017 年 2 月 | |


### <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心自动预配 Log Analytics 代理并将其连接到 Azure 订阅的默认 Log Analytics 工作区。 如果使用 Azure 安全中心，请勿按照本文档中的步骤运行。 这样做会覆盖已配置的工作区并断开与 Azure 安全中心的连接。

### <a name="internet-connectivity"></a>Internet 连接
适用于 Windows 的 Log Analytics 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Log Analytics 代理扩展的架构。 此扩展需要目标 Log Analytics 工作区的工作区 ID 和工作区密钥。 这些数据可在 Azure 门户的工作区设置中找到。 由于工作区密钥应视为敏感数据，因此将它存储在受保护的设置配置中。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。 请注意，**workspaceId** 和 **workspaceKey** 区分大小写。

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (e.g)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (e.g) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* workspaceId 在 Log Analytics API 中称为 consumerId。

> [!NOTE]
> 有关其他属性，请参阅 Azure 的[将 Windows 计算机连接到 Azure Monitor](../../azure-monitor/agents/agent-windows.md) 一文。

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行 Log Analytics 代理扩展。 包含 Log Analytics 代理 VM 扩展的示例模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/oms-extension-windows-vm)中找到。 

>[!NOTE]
>需要将代理配置为向多个工作区报告时，此模板不支持指定多个工作区 ID 和工作区密钥。 若要将代理配置为向多个工作区报告，请参阅[添加或删除工作区](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace)。  

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内，或放置在 Resource Manager JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

以下示例假定 Log Analytics 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用，并且类型反映了嵌套的配置。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzVMExtension` 命令将 Log Analytics 代理虚拟机扩展部署到现有的虚拟机。 运行命令之前，需将公共和专用的配置存储在 PowerShell 哈希表中。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 模块运行以下命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出记录到在以下目录中发现的文件：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
