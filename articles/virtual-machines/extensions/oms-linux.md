---
title: 适用于 Linux 的 Log Analytics 虚拟机扩展
description: 使用虚拟机扩展在 Linux 虚拟机上部署 Log Analytics 代理。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/18/2020
ms.openlocfilehash: 352aea7f000082a2f978005d958b41669e191584
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181565"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Log Analytics 虚拟机扩展

## <a name="overview"></a>概述

Azure Monitor 日志提供跨云和本地资产的监视、警报和警报修正功能。 适用于 Linux 的 Log Analytics 虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 本文档详细介绍适用于 Linux 的 Log Analytics 虚拟机扩展支持的平台、配置和部署选项。

>[!NOTE]
>从 Microsoft Operations Management Suite (OMS) 过渡到 Azure Monitor 期间，Windows 或 Linux 的 OMS 代理称为 Windows 或 Linux 的 Log Analytics 代理。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

有关支持的 Linux 发行版的详细信息，请参阅 [Azure Monitor 代理的概述](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)一文。

### <a name="agent-and-vm-extension-version"></a>代理和 VM 扩展版本
下表提供每次发布的 Log Analytics VM 扩展和 Log Analytics 代理捆绑包的版本映射。 并附有 Log Analytics 代理捆绑包版本的发行说明链接。 发行说明包括有关可用于给定代理版本的 bug 修补程序和新功能的详细信息。  

| Log Analytics Linux VM 扩展版本 | Log Analytics 代理捆绑包版本 | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心自动预配 Log Analytics 代理并将其连接到 Azure 订阅中由 ASC 创建的默认 Log Analytics 工作区。 如果使用 Azure 安全中心，请勿按照本文档中的步骤运行。 这样做会覆盖已配置的工作区并断开与 Azure 安全中心的连接。

### <a name="internet-connectivity"></a>Internet 连接

适用于 Linux 的 Log Analytics 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Log Analytics 代理扩展的架构。 此扩展需要目标 Log Analytics 工作区的工作区 ID 和工作区密钥，这些值可在 Azure 门户中的 [Log Analytics](../../azure-monitor/vm/monitor-virtual-machine.md) 工作区中找到。 由于工作区密钥应视为敏感数据，因此将它存储在受保护的设置配置中。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。 请注意，**workspaceId** 和 **workspaceKey** 区分大小写。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>上面的架构假定，将其放置在模板的根级别。 如果将其放在模板的虚拟机资源中，则应更改 `type` 和 `name` 属性，如[后文](#template-deployment)所述。

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| workspaceId (e.g) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (e.g) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>模板部署

>[!NOTE]
>[诊断 VM 扩展](./diagnostics-linux.md)中还随附了 Log Analytics VM 扩展的某些组件。 由于这种体系结构，如果在同一 ARM 模板中对两个扩展进行实例化，则可能会发生冲突。 为避免这些安装时冲突，请使用 [`dependsOn` 指令](../../azure-resource-manager/templates/resource-dependency.md#dependson)，确保按顺序安装扩展。 可按任一顺序安装扩展。

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要进行部署后配置（例如，载入 Azure Monitor 日志）的一个或多个虚拟机时，模板是理想选择。 包含 Log Analytics 代理 VM 扩展的示例资源管理器模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/oms-extension-ubuntu-vm)中找到。 

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

以下示例假定 VM 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用，并且类型反映了嵌套的配置。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将 Log Analytics 代理 VM 扩展部署到现有的虚拟机。 将下面的 myWorkspaceKey 值替换为工作区密钥，并将 myWorkspaceId 值替换为工作区 ID。 这些值可在 Azure 门户的“高级设置”下的 Log Analytics 工作区中找到。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 9 | 过早调用 enable | 将 [Azure Linux 代理](./update-linux-agent.md)更新为可用的最新版本。 |
| 10 | VM 已连接至 Log Analytics 工作区 | 要将 VM 连接到扩展架构中指定的工作区，请在公共设置中将“stopOnMultipleConnections”设置为 false，或删除该属性。 连接到工作区后，此 VM 立即开始计费。 |
| 11 | 提供给扩展的无效配置 | 按上述示例设置部署所需的所有属性值。 |
| 17 | Log Analytics 包安装失败 | 
| 19 | OMI 包安装失败 | 
| 20 | SCX 包安装失败 |
| 51 | VM 的操作系统不支持此扩展 | |
| 52 | 由于缺少依赖项，此扩展失败 | 若要详细了解缺少的依赖项，请查看输出和日志。 |
| 53 | 由于配置参数缺失或错误，此扩展失败 | 若要详细了解错误原因，请查看输出和日志。 此外，检查工作区 ID 的正确性，并验证计算机是否连接到 Internet。 |
| 55 | 无法连接到 Azure Monitor 服务或缺少所需的包或 dpkg 包管理器已锁定| 确保系统具有 Internet 访问权限，或已提供有效 HTTP 代理。 此外，检查工作区 ID 的正确性，并验证是否已安装 curl 和 tar 实用程序。 |

有关其他故障排除信息，可查看 [Log Analytics-Agent-for-Linux 故障排除指南](../../azure-monitor/visualize/vmext-troubleshoot.md)。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。