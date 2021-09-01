---
title: 通过 Azure 资源转移器在区域之间移动 Azure VM 时修改目标设置
description: 了解如何通过 Azure 资源转移器在区域之间移动 Azure VM 时修改目标设置。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 7044414d47f685062331bc4aceb1b538d6f9a062
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461580"
---
# <a name="modify-destination-settings"></a>修改目标设置

本文介绍通过 [Azure 资源转移器](overview.md)在 Azure 区域之间移动资源时如何修改目标设置。


## <a name="modify-vm-settings"></a>修改 VM 设置

移动 Azure VM 和关联资源时，可以修改目标设置。 我们建议：

- 仅在验证移动集合后更改目标设置。 但是：
    - 如果要移动源资源，则通常可以在启动初始移动过程之前修改这些设置。
    - 如果分配源区域中的现有资源，在移动提交完成之前一直可以修改目标设置。
- 在准备资源之前修改设置，因为在准备完成后，某些目标属性可能无法编辑。

### <a name="settings-you-can-modify"></a>你可以修改的设置

下表中汇总了你可以修改的配置设置。

**资源** | **选项** 
--- | --- 
**VM 名称** | 目标选项：<br/><br/> - 在目标区域中创建一个名称相同的新 VM。<br/><br/> - 在目标区域中创建一个名称不同的新 VM。<br/><br/> - 使用目标区域中现有的 VM。<br/><br/> 如果创建新 VM，则除修改的设置之外，将为新的目标 VM 分配与源相同的设置。
VM 可用性区域 | 将在其中放置目标 VM 的可用性区域。 如果不想更改源设置，或者不想将 VM 放到可用性区域，请选择“不适用”。
**VM SKU** | 将用于目标 VM 的 [VM 类型](https://azure.microsoft.com/pricing/details/virtual-machines/series/)（在目标区域中可用）。<br/><br/> 所选的目标 VM 不应小于源 VM。
VM 可用性集 | 将在其中放置目标 VM 的可用性集。 如果不想更改源设置，或者不想将 VM 放到可用性集，请选择“不适用”。
VM 密钥保管库 | 在 VM 上启用 Azure 磁盘加密时，关联的密钥保管库。
磁盘加密集 | VM 使用客户管理的密钥进行服务器端加密时，关联的磁盘加密集。
**资源组** | 将在其中放置目标 VM 的资源组。
网络资源 | 适用于网络接口、虚拟网络 (Vnet/) 和网络安全组/网络接口选项：<br/><br/> - 在目标区域中创建一个名称相同的新资源。<br/><br/> - 在目标区域中创建一个名称不同的新资源。<br/><br/> - 使用目标区域中的现有网络资源。<br/><br/> 如果创建新目标资源，则除修改的设置之外，将为新的目标资源分配与源资源相同的设置。
公共 IP 地址名称、SKU 和区域 | 指定标准公共 IP 地址的名称、[SKU](../virtual-network/public-ip-addresses.md#sku) 和[区域](../virtual-network/public-ip-addresses.md#standard)。<br/><br/> 如果希望它实现区域冗余，请以“区域冗余”形式输入。
负载均衡器名称、SKU 和区域 | 指定负载均衡器的名称、SKU（基本或标准）以及区域。<br/><br/> 建议使用“标准 SKU”。<br/><br/> 如果希望它实现区域冗余，请将其指定为“区域冗余”。
**资源依赖关系** | 适用于每个依赖项的选项：<br/><br/>- 资源使用将移动到目标区域的源依赖资源。<br/><br/> - 资源使用位于目标区域的不同依赖资源。 在这种情况下，可以从目标区域的任何类似资源中进行选择。

### <a name="edit-vm-destination-settings"></a>编辑 VM 目标设置

如果不想将依赖资源从源区域移动到目标区域，可以从多个其他选项中选择：

- 在目标区域中创建一个新资源。 除非你指定不同的设置，否则新资源将具有与源资源相同的设置。
- 使用目标区域中的现有资源。

确切行为取决于资源类型。 [详细了解](modify-target-settings.md)如何修改目标设置。

可以使用资源移动集合中的“目标配置”条目来修改资源的目标设置。 

若要修改设置，请执行以下操作： 

1. 在“跨区域”页面 >“目标配置”列中，单击资源条目的链接 。
2. 在“配置设置”中，可以在目标区域中创建一个新 VM。
3. 为目标 VM 分配新的可用性区域、可用性集或 SKU。 可用性区域和 SKU。

仅对正在编辑的资源进行更改。 你需要单独更新所有依赖资源。


## <a name="modify-sql-settings"></a>修改 SQL 设置

移动 Azure SQL 数据库资源时，可以修改用于移动的目标设置。 

- 对于 SQL 数据库：
    - 建议先修改目标配置设置，然后再准备进行移动。
    - 可以修改目标数据库的设置，以及数据库的区域冗余。
- 对于弹性池：
    -  在开始移动前可以随时修改目标配置。
    - 可以修改目标弹性池以及该池的区域冗余。 

### <a name="sql-settings-you-can-modify"></a>你可以修改的 SQL 设置

**设置** | SQL 数据库 | **弹性池**
--- | --- | ---
**名称** | 在目标区域中创建一个名称相同的新数据库。<br/><br/> 在目标区域中创建一个名称不同的新数据库。<br/><br/> 使用目标区域中的现有数据库。 | 在目标区域中创建一个具名称相同的新弹性池。<br/><br/> 在目标区域中创建一个名称不同的新弹性池。<br/><br/> 使用目标区域中的现有弹性池。
**区域冗余** | 若要从支持区域冗余的区域移动到不支持区域冗余的区域，请在区域设置中键入“禁用”。<br/><br/> 若要从不支持区域冗余的区域移动到支持区域冗余的区域，请在区域设置中键入“启用”。 | 若要从支持区域冗余的区域移动到不支持区域冗余的区域，请在区域设置中键入“禁用”。<br/><br/> 若要从不支持区域冗余的区域移动到支持区域冗余的区域，请在区域设置中键入“启用”。

### <a name="edit-sql-destination-settings"></a>编辑 SQL 目标设置

修改 Azure SQL 数据库资源目标设置的方式如下所示： 

1. 在“跨区域”中，对于要修改的资源，单击“目标配置”条目 。
2. 在“配置设置”中，指定上表中汇总的目标设置。


## <a name="modify-settings-in-powershell"></a>修改 PowerShell 中的设置

可以修改 PowerShell 中的设置。

1)  检索要编辑其属性的移动资源。 例如检索 VM 运行：

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  将资源设置复制到目标资源设置对象。

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  设置目标资源设置对象中的参数。 例如更改目标 VM 的名称：

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  更新“移动资源目标”设置。 在此示例中，我们将 VM 的名称“PSDemoVM”更改为“PSDemoVMTarget” 。

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    “输出”
    “修改目标设置后的输出文本”![](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>后续步骤

[将 Azure VM 移动到另一个区域](tutorial-move-region-virtual-machines.md)。
