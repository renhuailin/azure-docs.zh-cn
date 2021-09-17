---
title: 了解 Azure VM 规模集 VM 的实例 ID
description: 了解 Azure VM 规模集虚拟机的实例 ID 以及它们出现的各种方式。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 7b9b088eb3e9524e711ba4068c49f172e65b1be8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697456"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>了解 Azure VM 规模集 VM 的实例 ID

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

本文介绍规模集的实例 ID 和显示这些 ID 的各种方法。

## <a name="scale-set-instance-ids"></a>规模集实例 ID

规模集中的每个 VM 均获得唯一标识它的实例 ID。 可在规模集 API 中使用此实例 ID 对规模集中的特定 VM 执行操作。 例如，使用重置映像 API 时，可以指定特定实例 ID 以重置映像：

REST API：`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}`（有关详细信息，请参阅 [REST API 文档](/rest/api/compute/virtualmachinescalesetvms/reimage)）

PowerShell：`Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage`（有关详细信息，请参阅 [PowerShell 文档](/powershell/module/az.compute/set-azvmssvm)）

CLI：`az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}`（有关详细信息，请参阅 [CLI 文档](/cli/azure/vmss)）。

可以通过列出规模集中的所有实例来获取实例 ID 的列表：

REST API：`GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}`（有关详细信息，请参阅 [REST API 文档](/rest/api/compute/virtualmachinescalesetvms/list)）

PowerShell：`Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`（有关详细信息，请参阅 [PowerShell 文档](/powershell/module/az.compute/get-azvmssvm)）

CLI：`az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}`（有关详细信息，请参阅 [CLI 文档](/cli/azure/vmss)）。

也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 列出规模集中的 VM。

下面是 CLI 的某个示例输出，但输出的具体呈现取决于提供给命令的选项：

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

如你所见，“instanceId”属性只是一个十进制数。 删除旧实例后，实例 ID 可以重新用于新实例。

>[!NOTE]
> 对于将实例 ID 分配给规模集中的 VM 的方式 **没有保证**。 有时它们可能会按顺序递增，但并非总是这种情况。 不要依赖于将实例 ID 分配给 VM 的特定方式。

## <a name="scale-set-vm-names"></a>规模集 VM 名称

在上面的示例输出中，也有 VM 的“名称”。 此名称采用“{scale-set-name}_{instance-id}”格式。 在 Azure 门户中列出规模集中的实例时会看到此名称：

![显示 Azure 门户中虚拟机规模集的实例列表的屏幕截图。](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

名称的 {instance-id} 部分是与前面所述的“instanceId”属性相同的十进制数。

## <a name="instance-metadata-vm-name"></a>实例元数据 VM 名称

如果从规模集 VM 中查询[实例元数据](../virtual-machines/windows/instance-metadata-service.md)，会在输出中看到“名称”：

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

此名称与前面所述的名称相同。

## <a name="scale-set-vm-computer-name"></a>规模集 VM 的计算机名

规模集中的每个 VM 还会获得一个分配给它的计算机名称。 此计算机名在[虚拟网络中 Azure 提供的 DNS 名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)中是 VM 的主机名。 此计算机名的格式为“{computer-name-prefix}{base-36-instance-id}”。

{base-36-instance-id} [以 36 为基数](https://en.wikipedia.org/wiki/Base36)并且长度始终为六位。 如果数字的基于 36 的表示形式采用的位数少于六位，{base-36-instance-id} 将使用零进行填充以使其长度为六位。 例如，{computer-name-prefix} 为“nsgvmss”且实例 ID 为 85 的实例将具有计算机名称“nsgvmss00002D”。

>[!NOTE]
> 计算机名称前缀是规模集模型中可以设置的属性，因此它可以与规模集名称本身不同。
