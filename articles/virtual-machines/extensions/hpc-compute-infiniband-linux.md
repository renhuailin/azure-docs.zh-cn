---
title: InfiniBand 驱动程序扩展 - Azure Linux VM
description: 用于在运行 Linux 的 H 和 N 系列计算 VM 上安装 InfiniBand 驱动程序的 Microsoft Azure 扩展。
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 18e7132d6c767ecd1a7cd085d5b563a89d6af300
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446078"
---
# <a name="infiniband-driver-extension-for-linux"></a>适用于 Linux 的 InfiniBand 驱动程序扩展

此扩展在运行 Linux 的启用了 InfiniBand 和 SR-IOV（“r”大小）的 [H 系列](../sizes-hpc.md)和 [N 系列](../sizes-gpu.md) VM 上安装 InfiniBand OFED 驱动程序。 扩展为 Connect-X NIC 安装适当的驱动程序，具体取决于 VM 系列。

[在 HPC VM 上启用 InfiniBand](../workloads/hpc/enable-infiniband.md#manual-installation) 中提供了有关 OFED 驱动程序的手动安装的说明。

扩展也适用于为 [Windows VM](hpc-compute-infiniband-windows.md) 安装 InfiniBand 驱动程序。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

此扩展支持以下 OS 发行版，具体取决于特定 OS 版本对驱动程序的支持。

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS、18.04 LTS、20.04 LTS |
| Linux：CentOS | 7.4、7.5、7.6、7.7、8.1、8.2 |
| Linux：Red Hat Enterprise Linux | 7.4、7.5、7.6、7.7、8.1、8.2 |

### <a name="internet-connectivity"></a>Internet 连接

适用于 InfiniBand 驱动程序的 Microsoft Azure 扩展要求目标 VM 连接到 Internet 并可访问 Internet。

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
| type | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1.1 | int |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>将扩展添加到虚拟机规模集

以下示例在部署在名为 myResourceGroup 的资源组中部署的名为 myVMSS 的现有虚拟机规模集中的所有支持 RDMA VM 上安装最新版本 1.1 InfiniBandDriverLinux 扩展 ：

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
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

扩展执行输出将记录到以下文件。 请参考此文件以跟踪安装的状态以及对任何故障进行排查。

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>退出代码

下表根据扩展安装过程的退出代码描述了有意义和建议的操作。

| 退出代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 0 | 操作成功 |
| 1 | 扩展的用法不正确 | 检查执行输出日志 |
| 10 | 用于 Hyper-V 和 Azure 的 Linux Integration Services 不可用或未安装 | 检查 lspci 的输出 |
| 11 | 在此 VM 大小上找不到 Mellanox InfiniBand | 使用[受支持的 VM 大小和 OS](../sizes-hpc.md) |
| 12 | 不支持的映像产品/服务 |
| 13 | 不支持的 VM 大小 | 使用启用了 InfiniBand（“r”大小）的 [H 系列](../sizes-hpc.md)和 [N 系列](../sizes-gpu.md)N 系列 VM 来部署 |
| 14 | 操作失败 | 检查执行输出日志 |


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，可以通过 [Azure 支持站点](https://azure.microsoft.com/support/options/)提交支持事件。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关启用 InfiniBand（“r”大小）的详细信息，请参阅 [H 系列](../sizes-hpc.md)和 [N 系列](../sizes-gpu.md) VM。

> [!div class="nextstepaction"]
> [详细了解 Linux VM 扩展和功能](features-linux.md)
