---
title: 使用 Azure 资源转移器将资源移动到另一个区域
description: 了解如何使用 Azure 资源转移器将资源组中的资源移动到另一个区域。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 8eb4dc8ee58d9ced141c7c4e4404596ace727f0c
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459117"
---
# <a name="move-resources-across-regions-from-resource-group"></a>（从资源组）跨区域移动资源

本文介绍如何将特定资源组中的资源移到其他 Azure 区域。 在资源组中选择要移动的资源。 然后，使用 [Azure 资源转移器](overview.md)移动它们。

## <a name="prerequisites"></a>先决条件

- 你需要在想移动的资源所在的订阅上具有“所有者”访问权限。
    - 首次为 Azure 订阅中的特定源和目标映射添加资源时，资源转移器会创建一个订阅信任的[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)（以前称为托管服务标识 (MSI)）。
    - 若要创建标识，并为其分配所需的角色（来源订阅中的参与者或用户访问管理员），用于添加资源的帐户需要对订阅的“所有者”权限。 [详细了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 订阅需要足够的配额来在目标区域中创建源资源。 如果配额不足，请请求上调限制。 [了解详细信息](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 验证与要将 VM 移动到的目标区域关联的定价和费用。 请使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)来帮助你。
- 检查资源转移器是否支持你想要移动的资源：
    - Azure VM 和关联的磁盘
    - NIC
    - 可用性集
    - Azure 虚拟网络
    - 公共 IP 地址
    - 网络安全组 (NSG)：
    - 内部和公共负载均衡器
    - Azure SQL 数据库和弹性池


## <a name="check-vm-requirements"></a>检查 VM 要求

1. 检查要移动的 VM 是否受支持。

    - [验证](support-matrix-move-region-azure-vm.md#windows-vm-support)支持的 Windows VM。
    - [验证](support-matrix-move-region-azure-vm.md#linux-vm-support)支持的 Linux VM 和内核版本。
    - 查看支持的[计算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[存储](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[网络](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)设置。
2. 确保 VM 具有最新的受信任的根证书和已更新的证书吊销列表 (CRL)。 
    - 在运行 Windows 的 Azure VM 上，安装最新的 Windows 更新。
    - 在运行 Linux 的 VM 上，按照 Linux 分销商指南确保计算机具有最新的证书和 CRL。 
3. 允许来自 VM 的出站连接：
    - 如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果使用网络安全组 (NSG) 规则来控制出站连接，请创建这些[服务标记规则](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources-to-move"></a>选择要移动的资源

选择要移动的资源。 将资源移动到源区域订阅中的目标区域。 如果要更改订阅，可以在移动资源后进行更改。

> [!NOTE]
>  请不要选择关联的磁盘，否则会导致操作失败。 当迁移 VM 时，会自动包含关联的磁盘。

1. 在 Azure 门户中打开相关资源组。
2. 在资源组页面中选择要移动的资源。
3. 选择“移动” > “移到其他区域” 。

    ![用于将资源移动到其他区域的选择](./media/move-region-within-resource-group/select-move-region.png)
    
4. 在“源 + 目标”中，选择要将资源移动到的目标区域。 然后，选择“下一步”。


    ![用于选择目标区域的源和目标页面](./media/move-region-within-resource-group/source-target.png)


7. 在“要移动的资源”中，选择“下一步” 。  
8. 在“选择资源”中，选择要移动的资源。 只能添加支持移动的资源。 然后选择“完成”。
9. 在“移动资源”中，选择“下一步” 。 
10. 在“查看 + 添加”中，查看源和目标详细信息。
11. 确认你了解要移动的资源的相关元数据将存储在为此目标创建的资源组中，以及你允许资源转移器创建系统管理的标识来访问订阅资源。
1. 选择“继续”，开始添加资源。

    ![用于检查详细信息并继续移动的摘要页面](./media/move-region-within-resource-group/summary.png)    

11. “添加资源”操作将启动。 操作完成后，通知会显示已添加的资源且部署成功。
13. 在通知中，选择“添加资源用于移动”。

    ![通知中显示的消息](./media/move-region-within-resource-group/notification.png)    


14. 选择通知后，你选择的资源将添加到 Azure 资源转移器中心内的移动集合。  资源转移器可帮助你检查依赖项，然后开始将资源移动到目标区域。

## <a name="resolve-dependencies"></a>解决依赖项问题

正在移动的资源会显示在“跨区域”页面中，状态为“准备挂起”。 按如下所示开始验证：

1. 如果资源在“问题”列中显示“验证依赖项”消息，请选择“验证依赖项”按钮 。 验证过程开始。

    ![用于验证依赖项的按钮](./media/move-region-within-resource-group/validate-dependencies.png)

2. 如果找到依赖项，请选择“添加依赖项”。 
3. 在“添加依赖项”中，选择从属资源 >”添加依赖项” 。 在通知中监视进度。

    ![添加依赖项的按钮](./media/move-region-within-resource-group/add-dependencies.png)

3. 需要时添加其他依赖项，并根据需要验证依赖项。 选择“刷新”，确保资源显示最新状态。

4. 在“跨区域”页上，验证资源现在是否处于“准备挂起”状态，且没有任何问题。

    ![对所有资源显示“准备挂起”状态的页面](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>移动源资源组 

在准备和移动资源之前，目标区域中必须有源资源组。 

### <a name="prepare-to-move-the-source-resource-group"></a>准备以移动源资源组

按如下所示准备：

1. 在“跨区域”中，选择源资源组 >“准备” 。
2. 在“准备资源”中，选择“准备” 。

    ![用于准备源资源组的按钮](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    在准备过程中，资源转移器使用资源组设置生成 Azure 资源管理器 (ARM) 模板。 资源组内的资源不受影响。
    
> [!NOTE]
>  准备资源组后，资源组处于“启动移动挂起”状态。 刷新以显示最新状态。

![显示“启动挂起”状态的状态](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>移动源资源组

按如下所示启动移动：

1. 在“跨区域”中，选择资源组 >“启动移动” 
2. 在“移动资源”中，选择“开始移动” 。 资源组移动到“启动移动进行中”状态。
3. 启动移动后，根据生成的 ARM 模板创建目标资源组。 源资源组变为“提交移动挂起”状态。

    ![显示“提交移动”的状态](./media/move-region-availability-zone/commit-move-pending.png)

要提交并完成移动过程：

1. 在“跨区域”中，选择资源组 >“提交移动” 
2. 在“移动资源”中，选择“提交” 。

> [!NOTE]
> 提交移动后，源资源组处于“删除源挂起”状态。

## <a name="modify-target-settings"></a>修改目标设置

如果不想移动源资源，可执行以下任一操作：

- 在目标区域创建资源，资源的名称和设置与源区域中的资源相同。
- 在目标区域创建新的等效资源。 除了指定的设置，目标资源的设置与源相同。
- 使用目标区域中的现有资源。

按如下所述修改设置：

1. 若要修改设置，请在资源的“目标配置”列中选择条目。
2. 在“目标配置”页面上，指定要使用的目标设置。
    仅对正在编辑的资源进行更改。 需要单独更新所有从属资源。   
    
资源类型决定了具体要修改哪些设置。 [详细了解](modify-target-settings.md)如何编辑目标设置。

## <a name="prepare-resources-to-move"></a>准备要移动的资源

现在，源资源组已移动，你可以准备移动其他资源。

1. 在“跨区域”中，选择要准备的资源。 

    ![用于选择准备其他资源的页面](./media/move-region-availability-zone/prepare-other.png)

2. 选择“准备”。

> [!NOTE]
> - 在准备过程中，VM 上会安装 Azure Site Recovery 移动代理以用于复制。
> - VM 数据定期复制到目标区域。 这不会影响源 VM。
> - 资源移动会为其他源资源生成 ARM 模板。
> - 准备资源后，它们处于“启动移动挂起”状态。

![显示处于启动移动挂起状态的资源的页面](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>启动移动

准备好资源后，现在可以启动移动。

1. 在“跨区域”中，选择状态“启动移动挂起”的资源。 然后，选择“开始移动”。
2. 在“移动资源”中，选择“开始移动” 。

    ![选择“开始移动”按钮](./media/move-region-within-resource-group/initiate-move.png)

3. 跟踪通知栏中的移动进度。


> [!NOTE]
> - 对于 VM，在目标区域中创建副本 VM。 源 VM 已关闭，并出现一段停机时间（通常为几分钟）。<br/>
> - 资源转移器使用已准备的 ARM 模板重新创建其他资源。 通常不会停机。<br/> 
> - 对于负载均衡器，不会复制 NAT 规则。 提交移动后，请在目标区域中创建它们。
> - 对于公共 IP 地址，不会复制 DNS 名称标签。 请在提交移动后重新创建该标签。
> - 准备资源后，它们处于“提交移动挂起”状态。


## <a name="discard-or-commit"></a>放弃还是提交？

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果要测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会将资源返回到“启动移动挂起”状态。
- **提交**：提交即完成移动到目标区域的操作。 提交后，源资源将处于“删除源挂起”状态，你可以决定是否要删除它。


## <a name="discard-the-move"></a>放弃移动 

你可以按如下所示放弃移动：

1. 在“跨区域”中，选择状态为“提交移动挂起”的资源，然后选择“放弃移动”。
2. 在“放弃移动”中，选择“放弃” 。
3. 跟踪通知栏中的移动进度。
4. 当通知显示移动成功时，选择“刷新”。 

> [!NOTE]
> 对于 VM，在放弃资源后，它们处于“启动移动挂起”状态。

## <a name="commit-the-move"></a>提交移动

如果要完成移动过程，请提交移动。 


1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后选择“提交移动”。
2. 在“提交资源”中，选择“提交” 。

    ![用于提交资源以完成移动的页面](./media/move-region-within-resource-group/commit-resources.png)

3. 跟踪通知栏中的提交进度。

> [!NOTE]
> - 提交移动后，VM 将停止复制。 源 VM 不受提交影响。
> - 提交不会影响源网络资源。
> - 提交移动后，资源处于“删除源挂起”状态。

## <a name="configure-settings-after-the-move"></a>在移动后配置设置

1. 对于公共 IP 地址，不会复制 DNS 名称标签，因此在移动完成后，请导航到目标资源并更新该标签。 
2. 对于内部负载均衡器，由于不会复制 NAT 规则，请导航到在目标区域中创建的资源，并更新 NAT 规则。
3. 不会自动从 VM 卸载移动服务。 手动卸载它，或者如果你计划再次移动服务器，则保留它。
## <a name="delete-source-resources-after-commit"></a>提交后删除源资源

移动后，可以选择删除源区域中的资源。 

1. 在“跨区域”中，选择要删除的每个源资源的名称。
2. 在每个资源的属性页中，选择“删除”。

## <a name="delete-additional-resources-created-for-move"></a>删除为移动创建的其他资源

移动后，你可以手动删除移动集合和创建的 Site Recovery 资源。

- 默认情况下，移动集合处于隐藏状态。 要查看它，你需要打开隐藏的资源。
- 必须先删除对缓存存储的锁定，才能删除缓存存储。

按如下所示删除： 

1. 在源区域中找到资源组 ```RegionMoveRG-<sourceregion>-<target-region>``` 中的资源。
2. 查看是否已移动/删除移动集合中的所有 VM 和其他源资源。 这可确保没有挂起的资源使用它们。
2. 删除资源：

    - 移动集合名称为 ```movecollection-<sourceregion>-<target-region>```。
    - 缓存存储账户名称为 ```resmovecache<guid>```
    - 保管库名称为 ```ResourceMove-<sourceregion>-<target-region>-GUID```。

## <a name="next-steps"></a>后续步骤


[了解](about-move-process.md)移动过程。