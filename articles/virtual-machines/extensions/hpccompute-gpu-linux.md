---
title: NVIDIA GPU 驱动程序扩展 - Azure Linux VM
description: 用于在运行 Linux 的 N 系列计算 VM 上安装 NVIDIA GPU 驱动程序的 Microsoft Azure 扩展。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98ac0d72ee3bcb7e99030c0d6748cb359c148924
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446044"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>适用于 Linux 的 NVIDIA GPU 驱动程序扩展

## <a name="overview"></a>概述

此扩展在 Linux N 系列 VM 上安装 NVIDIA GPU 驱动程序。 根据 VM 系列，此扩展安装 CUDA 或 GRID 驱动程序。 使用此扩展安装 NVIDIA 驱动程序时，即表示你接受并同意 [NVIDIA 最终用户许可协议](https://go.microsoft.com/fwlink/?linkid=874330)的条款。 在安装过程中，VM 可能会重新启动以完成驱动程序安装。

我们提供了有关手动安装驱动程序和当前支持的版本的说明。 有关详细信息，请参阅[适用于 Linux 的 Azure N 系列 GPU 驱动程序安装](../linux/n-series-driver-setup.md)。
此扩展也可用于在 [Windows N 系列 VM](hpccompute-gpu-windows.md) 上安装 NVIDIA GPU 驱动程序。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

此扩展支持以下 OS 发行版，具体取决于特定 OS 版本对驱动程序的支持。

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS、18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.3、7.4、7.5、7.6、7.7、7.8 |
| Linux：CentOS | 7.3、7.4、7.5、7.6、7.7、7.8 |

### <a name="internet-connectivity"></a>Internet 连接

用于 NVIDIA GPU 驱动程序的 Microsoft Azure 扩展要求目标 VM 连接到 Internet 并具有访问权限。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示此扩展的架构。

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>属性

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>设置

所有设置都是可选的。 默认行为是，如果安装驱动程序时不要求更新内核，则不进行相应更新，请安装最新的受支持驱动程序和 CUDA 工具包（根据情况）。

| 名称 | 说明 | 默认值 | 有效值 | 数据类型 |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | 更新内核，即使安装驱动程序时不需要更新 | false | true、false | boolean |
| driverVersion | NV：GRID 驱动程序版本<br> NC/ND：CUDA 工具包版本。 将自动安装所选 CUDA 的最新驱动程序。 | 最新 | 受支持的驱动程序版本的[列表](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) | string |
| installCUDA | 安装 CUDA 工具包。 仅适用于 NC/ND 系列 VM。 | 是 | true、false | boolean |


## <a name="deployment"></a>部署


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板 

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要部署后配置的一个或多个虚拟机时，模板是理想选择。

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

以下示例假定扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

以下示例对上述 Azure 资源管理器和 PowerShell 示例进行了镜像。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

以下示例还添加了两个可选的自定义设置，作为非默认驱动程序安装的示例。 具体而言，它将 OS 内核更新为最新版本，并安装特定的 CUDA 工具包版本驱动程序。 同样，请注意，“--settings”是可选项，并且是默认设置。 请注意，更新内核可能会增加扩展安装时间。 另外，选择的特定（较旧）CUDA 工具包版本可能并不总是与较新的内核兼容。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户以及使用 Azure PowerShell 和 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件。 请参考此文件以跟踪（任何长期运行的）安装的状态以及对任何故障进行排查。

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>退出代码

| 退出代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 0 | 操作成功 |
| 1 | 扩展的用法不正确 | 检查执行输出日志 |
| 10 | 用于 Hyper-V 和 Azure 的 Linux Integration Services 不可用或未安装 | 检查 lspci 的输出 |
| 11 | 在此 VM 大小上找不到 NVIDIA GPU | 使用[受支持的 VM 大小和 OS](../linux/n-series-driver-setup.md) |
| 12 | 不支持的映像产品/服务 |
| 13 | 不支持的 VM 大小 | 请使用 N 系列 VM 部署 |
| 14 | 操作失败 | 检查执行输出日志 |


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。

有关 N 系列 VM 的详细信息，请参阅 [GPU 优化虚拟机大小](../sizes-gpu.md)。
