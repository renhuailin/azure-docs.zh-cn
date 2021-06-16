---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 517bde8d365793906c24d0f242d4adc1b6241e7c
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083692"
---
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 当 OS 和数据磁盘都已加密时，无法按预期在 Windows VM 上禁用数据磁盘加密。 请改为在所有磁盘上禁用加密。

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **使用资源管理器模板禁用加密：** 

    1. 单击[在正在运行的 Windows VM 上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm-without-aad)模板中的“部署到 Azure”。
    2. 选择订阅、资源组、位置、VM、卷类型、法律条款和协议。
    3.  单击“购买”，在正在运行的 Windows VM 上禁用磁盘加密。 
