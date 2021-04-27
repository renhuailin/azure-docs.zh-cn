---
title: 使用 Azure 资源转移器跨区域移动加密的 Azure VM
description: 了解如何使用 Azure 资源转移器将加密的 Azure VM 移动到另一个区域。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600686"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>教程：跨区域移动加密的 Azure VM

本文介绍如何使用 [Azure 资源转移器](overview.md)将加密的 Azure 虚拟机 (VM) 移动到不同的 Azure 区域。 

加密的 VM 可描述为：

- 具有启用了 Azure 磁盘加密的磁盘的 VM。 有关详细信息，请参阅[使用 Azure 门户创建和加密 Windows 虚拟机](../virtual-machines/windows/disk-encryption-portal-quickstart.md)。
- 使用客户管理的密钥 (CMK) 进行静态加密或服务器端加密的 VM。 有关详细信息，请参阅[使用 Azure 门户为托管磁盘启用使用客户管理的密钥进行服务器端加密](../virtual-machines/disks-enable-customer-managed-keys-portal.md)。


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 检查先决条件。 
> * 对于启用了 Azure 磁盘加密的 VM，将密钥和机密从源区域密钥保管库复制到目标区域密钥保管库。
> * 准备移动 VM，并在源区域中选择要从其中进行移动的资源。
> * 解决资源依赖项。
> * 对于启用了 Azure 磁盘加密的 VM，手动分配目标密钥保管库。 对于使用客户管理的密钥进行服务器端加密的 VM，在目标区域中手动分配磁盘加密集。
> * 移动密钥保管库和磁盘加密集。
> * 准备并移动源资源组。 
> * 准备并移动其他资源。
> * 确定是要放弃还是提交移动。 
> * 移动后，可以选择删除源区域中的资源。

> [!NOTE]
> 此教程介绍了尝试方案的最快路径。 它仅使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。 然后登录到 [Azure 门户](https://portal.azure.com)。

## <a name="prerequisites"></a>先决条件

要求 |详细信息
--- | ---
**订阅权限** | 进行检查以确保你对包含待移动资源的订阅具有“所有者”访问权限。<br/><br/> 为什么需要“所有者”访问权限？ 首次为 Azure 订阅中的特定源和目标对添加资源时，资源转移器将创建[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)（以前称为托管服务标识 (MSI)）。 此标识受订阅信任。 用于添加资源的帐户需要有订阅的“所有者”权限，你才可创建标识并为其分配所需的角色（来源订阅中的“参与者”和“用户访问管理员”）  。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
**VM 支持** | 通过执行以下操作，进行检查以确保要移动的 VM 受支持：<li>[验证](support-matrix-move-region-azure-vm.md#windows-vm-support)支持的 Windows VM。<li>[验证](support-matrix-move-region-azure-vm.md#linux-vm-support)支持的 Linux VM 和内核版本。<li>查看支持的[计算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[存储](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[网络](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)设置。
**密钥保管库要求（Azure 磁盘加密）** | 如果已为 VM 启用 Azure 磁盘加密，则需要源区域和目标区域中的密钥保管库。 有关详细信息，请参阅[创建密钥保管库](../key-vault/general/quick-create-portal.md)。<br/><br/> 对于源区域和目标区域中的密钥保管库，你需要以下权限：<li>密钥权限：密钥管理操作（Get、List）和加密操作（Decrypt 和 Encrypt）<li>机密权限：机密管理操作（Get、List 和 Set）<li>证书（List 和 Get）
磁盘加密集（使用 CMK 的服务器端加密） | 如果使用的 VM 是利用 CMK 进行服务器端加密的，则需要源区域和目标区域中的磁盘加密集。 有关详细信息，请参阅[创建磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)。<br/><br/> 如果将硬件安全模块（HSM 密钥）用于客户管理的密钥，则不支持在区域之间移动。
目标区域配额 | 订阅需要足够的配额来创建要在目标区域中移动的资源。 如果没有配额，请[请求其他限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。
目标区域费用 | 验证与要将 VM 移动到的目标区域关联的定价和费用。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)。


## <a name="verify-permissions-in-the-key-vault"></a>验证密钥保管库中的权限

如果要移动启用了 Azure 磁盘加密的 VM，则需要按照[将密钥复制到目标密钥保管库](#copy-the-keys-to-the-destination-key-vault)部分所述运行脚本。 执行脚本的用户应具有执行该操作的相应权限。 若要了解所需的权限，请参阅下表。 可以转到 Azure 门户中的密钥保管库，查找用于更改权限的选项。 在“设置”下，选择“访问策略” 。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="密钥设置窗格中“访问策略”链接的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

如果没有用户权限，请选择“添加访问策略”，然后指定权限。 如果用户帐户已有策略，请在“用户”下，根据下表中的说明设置权限。

使用 Azure 磁盘加密的 Azure VM 可具有以下变体，并且你需要根据其相关组件设置权限。 VM 可能具有：
- 仅使用机密对磁盘进行加密的默认选项。
- 使用[密钥加密密钥 (KEK)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek) 增加了安全性。

### <a name="source-region-key-vault"></a>源区域密钥保管库

对于执行脚本的用户，请设置针对以下组件的权限： 

组件 | 所需权限
--- | ---
机密 |  *Get* <br></br> 选择“机密权限” > “机密管理操作”，然后选择“Get”  。 
键 <br></br> 如果你使用的是 KEK，则除了针对机密的权限外，还需要具有这些权限。 | Get 和 Decrypt  <br></br> 选择“密钥权限” > “密钥管理操作”，然后选择“Get”  。 在“加密操作”中，选择“Decrypt” 。

### <a name="destination-region-key-vault"></a>目标区域密钥保管库

在“访问策略”中，请确保启用“用于卷加密的 Azure 磁盘加密” 。 

对于执行脚本的用户，请设置针对以下组件的权限： 

组件 | 所需权限
--- | ---
机密 |  *设置* <br></br> 选择“机密权限” > “机密管理操作”，然后选择“Set”  。 
键 <br></br> 如果你使用的是 KEK，则除了针对机密的权限外，还需要具有这些权限。 | Get、Create 和 Encrypt   <br></br> .选择“密钥权限” > “密钥管理操作”，然后选择“Get”和“Create”   。 在“加密操作”中，选择“Encrypt” 。

<br>

除了上述权限外，在目标密钥保管库中，你还需要为资源转移器用来代表你访问 Azure 资源的[托管系统标识](./common-questions.md#how-is-managed-identity-used-in-resource-mover)添加权限。 

1. 在“设置”下，选择“添加访问策略” 。 
1. 在“选择主体”中，搜索 MSI。 MSI 名称是 ```movecollection-<sourceregion>-<target-region>-<metadata-region>```。 
1. 对于 MSI，请添加以下权限：

    组件 | 所需权限
    --- | ---
    机密|  Get 和 List  <br></br> 选择“机密权限” > “机密管理操作”，然后选择“Get”和“List”   。 
    键 <br></br> 如果你使用的是 KEK，则除了针对机密的权限外，还需要具有这些权限。 | Get 和 List  <br></br> 选择“密钥权限” > “密钥管理操作”，然后选择“Get”和“List”   。

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>将密钥复制到目标密钥保管库

使用我们提供的[脚本](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)，将加密机密和密钥从源密钥保管库复制到目标密钥保管库。

- 在 PowerShell 中运行该脚本。 建议使用最新的 PowerShell 版本。
- 具体而言，该脚本需要以下模块：
    - Az.Compute
    - Az.KeyVault（版本 3.0.0）
    - Az.Accounts（版本 2.2.3）

要运行脚本，请执行以下操作：

1. 在 GitHub 中打开该[脚本](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)。
1. 将该脚本的内容复制到一个本地文件，并将其命名为“Copy-keys.ps1”。
1. 运行该脚本。
1. 登录到 Azure 门户。
1. 在“用户输入”窗口的下拉列表中，选择源订阅、资源组和源 VM，然后选择目标位置以及磁盘和密钥加密的目标保管库。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="用于输入脚本值的“用户输入”窗口的屏幕截图。" :::

1. 选择“选择”按钮  。 
   
   脚本运行完毕后，会出现一条消息，通知你 CopyKeys 已成功完成。

## <a name="prepare-vms"></a>准备 VM

1. 检查并确保 VM 满足[先决条件](#prerequisites)后，请确保已启用要移动的 VM。 所有需要在目标区域中可用的 VM 磁盘都必须在 VM 中附加并初始化。
1. 为了确保 VM 具有最新的受信任的根证书和经过更新的证书吊销列表 (CRL)，请执行以下操作：
    - 在 Windows VM 上，安装最新的 Windows 更新。
    - 在 Linux VM 上，遵循分发服务器指南，让计算机具有最新的证书和 CRL。 
1. 若要允许来自 VM 的出站连接，请执行以下操作之一：
    - 如果使用基于 URL 的防火墙代理来控制出站连接，请[允许访问 URL](support-matrix-move-region-azure-vm.md#url-access)。
    - 如果使用网络安全组 (NSG) 规则来控制出站连接，请创建这些[服务标记规则](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-the-resources-to-move"></a>选择要移动的资源

- 可以在所选源区域中的任何资源组中选择任何受支持的资源类型。  
- 可以将资源移动到与源区域相同的订阅中的目标区域。 如果要更改订阅，可以在移动资源后进行更改。

若要选择资源，请执行以下操作：

1. 在 Azure 门户中，搜索“资源转移器”。 然后，在“服务”下，选择“Azure 资源转移器” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure 门户中“Azure 资源转移器”的搜索结果的屏幕截图。" :::

1. 在 Azure 资源转移器的“概述”窗格上，选择“跨区域移动” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="“跨区域移动”按钮的屏幕截图，该按钮用于添加要移动到另一个区域的资源。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. 在“移动资源”窗格中，选择“源 + 目标”选项卡 。然后在下拉列表中，选择源订阅和区域。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="用于选择源区域和目标区域的页面。" :::

1. 在“目标”下，选择要将 VM 移动到的区域，然后选择“下一步” 。

1. 选择“要移动的资源”选项卡，然后选择“选择资源” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="“移动资源”窗格和“选择资源”按钮的屏幕截图。" :::

1. 在“选择资源”窗格中，选择要移动的 VM。 如[选择要移动的资源](#select-the-resources-to-move)部分中所述，只可添加支持移动的资源。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="用于选择要移动的 VM 的“选择资源”窗格的屏幕截图。" :::

    > [!NOTE]
    >  在本教程中，你将选择一个使用客户管理的密钥进行服务器端加密的 VM (rayne-vm)，以及一个启用了磁盘加密的 VM (rayne-vm-ade)。

1. 选择“完成”。
1. 选择“要移动的资源”选项卡，然后选择“下一步” 。
1. 选择“查看”选项卡，然后检查源和目标设置。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="用于检查源和目标设置的窗格的屏幕截图。" :::

1. 选择“继续”，开始添加资源。
1. 选择通知图标以跟踪进度。 成功完成该过程后，请在“通知”窗格中选择“已添加要移动的资源” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="用于确认已成功添加资源的“通知”窗格的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. 选择通知后，查看“跨区域”页上的资源。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="状态为“准备已挂起”的已添加资源的屏幕截图。" :::

> [!NOTE]
> - 添加的资源会处于“准备已挂起”状态。
> - VM 的资源组会自动添加。
> - 如果将“目标配置”条目修改为使用目标区域中已存在的资源，资源状态就会被设置为“提交已挂起”，因为无需为该资源启动移动。
> - 如果要删除已经添加的资源，操作方法将取决于你在移动过程中的进度。 有关详细信息，请参阅[管理移动集合和资源组](remove-move-resources.md)。


## <a name="resolve-dependencies"></a>解决依赖项问题

1. 如果任何资源在“问题”列中显示“验证依赖项”消息，请选择“验证依赖项”按钮 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="显示“验证依赖项”按钮的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    验证过程开始。
1. 如果找到依赖项，请选择“添加依赖项”。  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="“添加依赖项”按钮的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. 在“添加依赖项”窗格中，保留默认的“显示所有依赖项”选项 。

    - “显示所有依赖项”会遍历某个资源的所有直接和间接依赖项。 例如，对于某个 VM，该选项会显示 NIC、虚拟网络、网络安全组 (NSG) 等等。
    - “只显示第一级依赖项”只会显示直接依赖项。 例如，对于某个 VM，该选项会显示 NIC，而不会显示虚拟网络。
 
1. 请选择需要添加的依赖资源，然后选择“添加依赖项”。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="依赖项列表和“添加依赖项”按钮的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. 再次验证依赖项。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="用于重新验证依赖项的窗格的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>分配目标资源

需要手动分配与加密关联的目标资源。

- 如果要移动的 VM 启用了 Azure 磁盘加密，则目标区域中的密钥保管库将会显示为依赖项。
- 如果要移动使用 CMK 进行服务器端加密的 VM，则目标区域中的磁盘加密集会显示为依赖项。 
- 由于本教程演示了如何移动启用了 Azure 磁盘加密且使用 CMK 的 VM，因此，目标密钥保管库和磁盘加密集都会显示为依赖项。

若要手动分配目标资源，请执行以下操作：

1. 在磁盘加密集条目中，在“目标配置”列中选择“未分配的资源” 。
1. 在“配置设置”中，选择目标磁盘加密集，然后选择“保存更改” 。
1. 可以保存并验证所修改的资源的依赖项，或者也可以只保存更改，然后一次性验证所有修改的内容。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="用于保存目标区域中的更改的“目标配置”窗格的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    在添加目标资源后，磁盘加密集的状态会更改为“提交移动已挂起”。

1. 在密钥保管库条目中，选择“目标配置”列中的“未分配的资源” 。 在“配置设置”下，选择目标密钥保管库，然后保存更改。 

在此阶段，磁盘加密集和密钥保管库状态会更改为“提交移动已挂起”。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="用于准备其他资源的窗格的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

若要提交并完成加密资源的移动过程，请执行以下操作：

1. 在“跨区域”中，选择资源（磁盘加密集或密钥保管库），然后选择“提交移动” 。
1. 在“移动资源”中，选择“提交” 。

> [!NOTE]
> 提交移动后，资源状态更改为“删除源已挂起”。


## <a name="move-the-source-resource-group"></a>移动源资源组 

在准备和移动 VM 之前，VM 资源组必须存在于目标区域中。 

### <a name="prepare-to-move-the-source-resource-group"></a>准备以移动源资源组

在准备过程中，资源转移器基于资源组设置生成 Azure 资源管理器 (ARM) 模板。 资源组内的资源不受影响。

若要准备移动源资源组，请执行以下操作：

1. 在“跨区域”中，选择源资源组，然后选择“准备” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="“准备资源”窗格上“准备”按钮的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. 在“准备资源”中，选择“准备” 。

> [!NOTE]
> 准备好移动后，资源组状态更改“启动移动已挂起”。 

 
### <a name="move-the-source-resource-group"></a>移动源资源组

通过执行以下操作开始移动源资源组：

1. 在“跨区域”窗格中，选择资源组，然后选择“启动移动” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="“跨区域”窗格中“启动移动”按钮的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. 在“移动资源”窗格中，选择“启动移动” 。 资源组状态更改为“启动移动进行中”。   
1. 启动移动后，系统根据生成的 ARM 模板创建目标资源组。 源资源组状态更改为“提交移动已挂起”。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="“移动资源”窗格的屏幕截图，其中显示资源组状态更改为“提交移动已挂起”。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

若要提交移动并完成该过程，请执行以下操作：

1. 在“跨区域”窗格中，选择资源组，然后选择“提交移动” 。
1. 在“移动资源”窗格中，选择“提交” 。

> [!NOTE]
> 提交移动后，源资源组状态更改为“删除源已挂起”。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="显示状态更改为“删除源已挂起”的源资源组的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>准备要移动的资源

现在，加密资源和源资源组已移动，你可以准备移动当前状态为“准备已挂起”的其他资源。


1. 在“跨区域”窗格中，再次验证移动，并解决任何存在的问题。
1. 如果要在开始移动之前编辑目标设置，请在资源的“目标配置”列中选择链接，然后编辑设置。 如果编辑目标 VM 设置，则目标 VM 大小不应小于源 VM 大小。
1. 为要移动的处于“准备已挂起”状态的资源选择“准备”。
1. 在“准备资源”窗格中，选择“准备” 。

    - 准备过程中，会在 VM 上安装 Azure Site Recovery 移动代理，以复制资源。
    - VM 数据定期复制到目标区域。 这不会影响源 VM。
    - 资源移动会为其他源资源生成 ARM 模板。

> [!NOTE]
> 准备好资源后，资源状态更改“启动移动已挂起”。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="“准备资源”窗格的屏幕截图，其中显示资源处于“启动移动已挂起”状态。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>启动移动

你已准备好资源，现在可以启动移动。 

1. 在“跨区域”窗格中，选择状态为“启动移动已挂起”的资源，然后选择“启动移动”。
1. 在“移动资源”窗格中，选择“启动移动” 。
1. 在通知栏中跟踪移动进度。

    - 对于 VM，在目标区域中创建副本 VM。 源 VM 已关闭，并出现一段停机时间（通常为几分钟）。
    - 资源转移器使用准备的 ARM 模板重新创建其他资源。 通常不会停机。
    - 移动资源后，其状态更改为“提交移动已挂起”。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="状态为“提交移动已挂起”的资源列表的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>放弃还是提交？

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果你在进行测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会使资源返回到“启动移动已挂起”状态。
- **提交**：提交即完成移动到目标区域的操作。 提交源资源后，其状态更改为“删除源已挂起”，你可以决定是否要删除它。


## <a name="discard-the-move"></a>放弃移动 

若要放弃移动，请执行以下操作：

1. 在“跨区域”窗格中，选择状态为“提交移动已挂起”的资源，然后选择“放弃移动”。
1. 在“放弃移动”窗格中，选择“放弃” 。
1. 在通知栏中跟踪移动进度。


> [!NOTE]
> 放弃资源后，VM 状态更改为“启动移动已挂起”。

## <a name="commit-the-move"></a>提交移动

若要完成移动过程，请通过执行以下操作提交移动： 

1. 在“跨区域”窗格中，选择状态为“提交移动已挂起”的资源，然后选择“提交移动”。
1. 在“提交资源”窗格中，选择“提交” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="要提交以完成移动的资源列表的屏幕截图。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. 跟踪通知栏中的提交进度。

> [!NOTE]
> - 提交移动后，VM 停止复制。 源 VM 不受提交影响。
> - 提交过程不会影响源网络资源。
> - 提交移动后，资源状态更改为“删除源已挂起”。



## <a name="configure-settings-after-the-move"></a>在移动后配置设置

- 不会自动从 VM 卸载移动服务。 手动卸载它，或者如果你计划再次移动服务器，则保留它。
- 移动后，修改 Azure 基于角色的访问控制 (RBAC) 规则。

## <a name="delete-source-resources-after-commit"></a>提交后删除源资源

移动后，可以选择删除源区域中的资源。 

1. 在“跨区域”窗格中，选择要删除的每个源资源，然后选择“删除源” 。
1. 在“删除源”中，查看需要删除的内容，并在“确认删除”中，键入“是”  。 该操作不可逆，因此请仔细检查！
1. 在键入“是”后，选择“删除源” 。

> [!NOTE]
>  在“资源移动”门户中，无法删除资源组、密钥保管库或 SQL Server 实例。 需要分别从每个资源的属性页中删除每个资源。


## <a name="delete-resources-that-you-created-for-the-move"></a>删除为移动创建的资源

移动后，你可以手动删除移动集合和在此过程中创建的 Site Recovery 资源。

- 默认情况下，移动集合处于隐藏状态。 要查看它，你需要打开隐藏的资源。
- 必须先删除对缓存存储的锁定，才能删除缓存存储。

若要删除资源，请执行以下操作： 
1. 找到资源组 ```RegionMoveRG-<sourceregion>-<target-region>``` 中的资源。
1. 进行检查以确保已移动或删除源区域中的所有 VM 和其他源资源。 此步骤可确保没有挂起的资源使用它们。
1. 删除资源：

    - 移动集合名称：```movecollection-<sourceregion>-<target-region>```
    - 缓存存储帐户名称：```resmovecache<guid>```
    - 保管库名称：```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 已将加密的 Azure VM 及其依赖资源移动到了另一个 Azure 区域。


接下来，尝试将 Azure SQL 数据库和弹性池移动到另一区域。

> [!div class="nextstepaction"]
> [移动 Azure SQL 资源](./tutorial-move-region-sql.md)
