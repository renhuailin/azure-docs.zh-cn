---
title: 使用 Azure 资源转移器跨区域移动加密的 Azure VM
description: 了解如何使用 Azure 资源转移器将加密的 Azure VM 移动到另一个区域
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361003"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>教程：跨区域移动加密的 Azure VM

本文介绍如何使用 [Azure 资源转移器](overview.md)将加密的 Azure VM 移动到不同的 Azure 区域。 我们所说的加密是指：

- VM，其中具有启用了 Azure 磁盘加密的磁盘。 [了解详细信息](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- 或者，使用客户管理的密钥 (CMK) 进行静态加密（服务器端加密）的 VM。 [了解详细信息](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 检查先决条件。 
> * 对于启用了 Azure 磁盘加密的 VM，将密钥和机密从源区域密钥保管库复制到目标区域密钥保管库。
> * 准备好要进行移动的 VM，并在源区域中选择要移动的资源。
> * 解决资源依赖项。
> * 对于启用了 Azure 磁盘加密的 VM，手动分配目标密钥保管库。 对于使用客户管理的密钥进行服务器端加密的 VM，在目标区域中手动分配磁盘加密集。
> * 移动密钥保管库和/或磁盘加密集。
> * 准备并移动源资源组。 
> * 准备并移动其他资源。
> * 确定是放弃还是提交移动。 
> * 移动后，可以选择删除源区域中的资源。

> [!NOTE]
> 教程显示尝试方案的最快路径，并使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。 然后登录到 [Azure 门户](https://portal.azure.com)。

## <a name="prerequisites"></a>先决条件

**要求** |**详细信息**
--- | ---
**订阅权限** | 检查以确定你对包含要移动的资源的订阅具有“所有者”访问权限。<br/><br/> 为什么需要“所有者”访问权限？ 首次为 Azure 订阅中的特定源和目标对添加资源时，资源转移器将创建受订阅信任的[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)（以前称为托管服务标识 (MSI)）。 若要创建标识并为其分配所需的角色（来源订阅中的参与者和用户访问管理员），用于添加资源的帐户需要有该订阅的“所有者”权限。 [详细了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
**VM 支持** | 检查要移动的 VM 是否受支持。<br/><br/> - [验证](support-matrix-move-region-azure-vm.md#windows-vm-support)受支持的 Windows VM。<br/><br/> - [验证](support-matrix-move-region-azure-vm.md#linux-vm-support)受支持的 Linux VM 和内核版本。<br/><br/> - 检查受支持的[计算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[存储](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[网络](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)设置。
密钥保管库要求（Azure 磁盘加密） | 如果为 VM 启用了 Azure 磁盘加密，则除了需要源区域中的密钥保管库之外，还需要目标区域中的密钥保管库。 [创建密钥保管库](../key-vault/general/quick-create-portal.md)。<br/><br/> 对于源区域和目标区域中的密钥保管库，你需要以下权限：<br/><br/> - 密钥权限：密钥管理操作（Get、List）；加密操作（Decrypt 和 Encrypt）。<br/><br/> - 机密权限：机密管理操作（Get、List 和 Set）<br/><br/> - 证书（List 和 Get）。
磁盘加密集（使用 CMK 的服务器端加密） | 如果使用的 VM 是利用 CMK 进行服务器端加密的，则除了需要源区域中的磁盘加密集之外，还需要目标区域中的磁盘加密集。 [创建磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)。<br/><br/> 如果将 HSM 密钥用于客户管理的密钥，则在区域之间移动是不受支持的。
目标区域配额 | 订阅需要足够的配额来创建要在目标区域中移动的资源。 如果没有配额，请[请求其他限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。
目标区域费用 | 验证与要将 VM 移动到的目标区域关联的定价和费用。 请使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)来帮助你。


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>使用 Azure 磁盘加密 (ADE) 验证用户对 VM 密钥保管库的权限

如果要移动启用了 Azure 磁盘加密的 VM，则需要按照[下文](#copy-the-keys-to-the-destination-key-vault)所述运行脚本，执行该脚本的用户应该具有相应的权限。 请参阅下表，了解所需权限。 通过在 Azure 门户中导航到该密钥保管库，可找到更改权限的选项 - 在“设置”下选择“访问策略” 。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="用于打开密钥保管库访问策略的按钮。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

如果没有用户权限，请选择“添加访问策略”，并指定权限。 如果用户帐户已有策略，请在“用户”下，根据下表设置权限。

使用 ADE 的 Azure VM 可具有以下变体，且需要相应地为相关组件设置权限。
- 仅使用机密对磁盘进行加密的默认选项
- 使用[密钥加密密钥](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)增加了安全性

### <a name="source-region-keyvault"></a>源区域密钥保管库

需要为执行脚本的用户设置以下权限 

组件 | 所需权限
--- | ---
机密|  Get 权限 <br> </br> 在“机密权限”>  “机密管理操作”中，选择“Get”   
键 <br> </br> 如果要使用密钥加密密钥 (KEK)，除了机密以外，你还需要此权限| Get 和 Decrypt 权限 <br> </br> 在“密钥权限” > “密钥管理操作”中，选择“Get”  。 在“加密操作”中，选择“Decrypt” 。

### <a name="destination-region-keyvault"></a>目标区域密钥保管库

在“访问策略”中，请确保启用“用于卷加密的 Azure 磁盘加密” 。 

需要为执行脚本的用户设置以下权限 

组件 | 所需权限
--- | ---
机密|  Set 权限 <br> </br> 在“机密权限”>  “机密管理操作”中，选择“Set”   
键 <br> </br> 如果要使用密钥加密密钥 (KEK)，除了机密以外，你还需要此权限| Get、Create 和 Encrypt 权限 <br> </br> 在“密钥权限” > “密钥管理操作”中，选择“Get”和“Create”   。 在“加密操作”中，选择“Encrypt” 。

除了上述权限外，在目标密钥保管库中，你还需要为资源转移器用来代表你访问 Azure 资源的[托管系统标识](./common-questions.md#how-is-managed-identity-used-in-resource-mover)添加权限。 

1. 在“设置”下，选择“添加访问策略” 。 
2. 在“选择主体”中，搜索 MSI。 MSI 名称是 ```movecollection-<sourceregion>-<target-region>-<metadata-region>```。 
3. 为 MSI 添加以下权限

组件 | 所需权限
--- | ---
机密|  Get 和 List 权限 <br> </br> 在“机密权限”>  “机密管理操作”中，选择“Get”和“List”    
键 <br> </br> 如果要使用密钥加密密钥 (KEK)，除了机密以外，你还需要此权限| Get、List 权限 <br> </br> 在“密钥权限” > “密钥管理操作”中，选择“Get”和“List”   



### <a name="copy-the-keys-to-the-destination-key-vault"></a>将密钥复制到目标密钥保管库

你需要使用我们提供的脚本，以便将加密机密和密钥从源密钥保管库复制到目标密钥保管库。

- 请在 PowerShell 中运行该脚本。 建议运行最新的 PowerShell 版本。
- 具体而言，该脚本需要以下模块：
    - Az.Compute
    - Az.KeyVault（版本 3.0.0
    - Az.Accounts（版本 2.2.3）

请按如下所述运行：

1. 在 GitHub 中导航到该[脚本](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)。
2. 将该脚本的内容复制到一个本地文件，并将其命名为“Copy-keys.ps1”。
3. 运行该脚本。
4. 登录到 Azure。
5. 在“用户输入”弹出窗口中，选择来源订阅、资源组和源 VM。 然后选择目标位置，并选择磁盘和密钥加密的目标保管库。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="用于输入脚本值的弹出窗口。" :::


6. 在脚本完成之后，屏幕输出会显示 CopyKeys 已成功。

## <a name="prepare-vms"></a>准备 VM

1. 在[检查 VM 是否满足要求](#prerequisites)后，请确保已打开需要移动的 VM。 所有需要在目标区域中可用的 VM 磁盘都必须在 VM 中附加并初始化。
3. 检查 VM 是否有最新的受信任根证书和经过更新的证书吊销列表 (CRL)。 要执行此操作：
    - 在 Windows VM 上，安装最新的 Windows 更新。
    - 在 Linux VM 上，遵循分发服务器指南，让计算机具有最新的证书和 CRL。 
4. 允许来自 VM 的出站连接，如下所示：
    - 如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果使用网络安全组 (NSG) 规则来控制出站连接，请创建这些[服务标记规则](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources-to-move"></a>选择要移动的资源


- 可以在所选源区域中的任何资源组中选择任何受支持的资源类型。  
- 将资源移动到与源区域相同的订阅中的目标区域。 如果要更改订阅，可以在移动资源后进行更改。

选择资源，如下所示：

1. 在 Azure 门户中，搜索“资源转移器”。 然后，在“服务”下，选择“Azure 资源转移器” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure 门户中资源转移器的搜索结果。" :::

2. 在“概述”中，单击“跨区域移动” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="用于添加要移动到其他区域的资源的按钮。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. 在“移动资源” > “源 + 目标”中，选择来源订阅和区域 。
4. 在“目标”中，选择要将 VM 移动到的区域。 然后单击“下一步”  。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="用于选择源区域和目标区域的页面。" :::

5. 在“要移动的资源”中，单击“选择资源” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="用于选择要移动的资源的按钮。" :::

6. 在“选择资源”中，选择 VM。 只能添加[支持移动](#prepare-vms)的资源。 然后单击“完成”。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="用于选择要移动的 VM 的页面。" :::

    > [!NOTE]
    >  在本教程中，我们将选择一个使用客户管理的密钥进行服务器端加密的 VM (rayne-vm)，以及一个启用了磁盘加密的 VM (rayne-vm-ade)。

7.  在“要移动的资源”中，单击“下一步” 。
8. 在“查看”中，检查源和目标的设置。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="用于检查设置并继续进行移动的页面。" :::

9. 单击“继续”，开始添加资源。
10. 选择通知图标以跟踪进度。 在添加过程成功完成后，请选择通知中的“已添加要移动的资源”。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="用于确认已成功添加资源的通知。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. 单击通知后，查看“跨区域”页上的资源。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="显示处于“准备已挂起”状态的已添加资源的页面。" :::

> [!NOTE]
> - 添加的资源会处于“准备已挂起”状态。
> - VM 的资源组会自动添加。
> - 如果将“目标配置”条目修改为使用目标区域中已存在的资源，资源状态就会被设置为“提交已挂起”，因为无需为该资源启动移动。
> - 如果要删除已经添加的资源，操作方法将取决于你在移动过程中的进度。 [了解详细信息](remove-move-resources.md)。


## <a name="resolve-dependencies"></a>解决依赖项问题

1. 如果任何资源在“问题”列中显示“验证依赖项”消息，请选择“验证依赖项”按钮 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="用于检查依赖项的按钮。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    验证过程开始。
2. 如果找到依赖项，请单击“添加依赖项”  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="用于添加依赖项的按钮。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. 在“添加依赖项”中，保留默认的“显示所有依赖项”选项 。

    - “显示所有依赖项”会遍历某个资源的所有直接和间接依赖项。 例如，对于某个 VM，该选项会显示 NIC、虚拟网络、网络安全组 (NSG)，等等。
    - “只显示第一级依赖项”只会显示直接依赖项。 例如，对于某个 VM，该选项会显示 NIC，而不会显示虚拟网络。
 
4. 请选择需要添加的依赖资源 >“添加依赖项”。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="从依赖项列表中选择依赖项。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. 再次验证依赖项。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="用于再次验证的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>分配目标资源

与加密关联的目标资源需要手动分配。

- 如果要移动的 VM 启用了 Azure 磁盘加密 (ADE)，则目标区域中的密钥保管库将会显示为依赖项。
- 如果要移动使用客户管理的密钥 (CMK) 进行服务器端加密的 VM，则目标区域中的磁盘加密集就会显示为依赖项。 
- 由于本教程要移动启用了 ADE 的 VM 和使用 CMK 的 VM，因此，目标密钥保管库和磁盘加密集都会显示为依赖项。

按如下所示进行手动分配：

1. 在磁盘加密集条目中，在“目标配置”列中选择“未分配的资源” 。
2. 在“配置设置”中，选择目标磁盘加密集。 然后选择“保存更改”。
3. 可以选择保存并验证所修改的资源的依赖项，或者也可以只是保存更改，然后一次性验证所有修改的内容。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="用于选择目标区域中磁盘加密集的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    在添加目标资源后，磁盘加密集的状态会变为“提交移动已挂起”。
3. 在密钥保管库条目中，选择“目标配置”列中的“未分配的资源” 。 在“配置设置”中，选择目标密钥保管库。 保存更改。 

在此阶段，磁盘加密集和密钥保管库状态会变为“提交移动已挂起”。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="用于选择准备其他资源的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

提交并完成加密资源的移动过程。

1. 在“跨区域”中，选择资源（磁盘加密集或密钥保管库）>“提交移动” 。
2. 在“移动资源”中，单击“提交” 。

> [!NOTE]
> 在提交移动后，该资源会处于“删除源已挂起”状态。


## <a name="move-the-source-resource-group"></a>移动源资源组 

在准备和移动 VM 之前，VM 资源组必须存在于目标区域中。 

### <a name="prepare-to-move-the-source-resource-group"></a>准备以移动源资源组

在准备过程中，资源转移器使用资源组设置生成 Azure 资源管理器 (ARM) 模板。 资源组内的资源不受影响。

按如下所示准备：

1. 在“跨区域”中，选择源资源组 >“准备” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="准备资源组。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. 在“准备资源”中，单击“准备” 。

> [!NOTE]
> 准备资源组后，资源组处于“启动移动挂起”状态。 

 
### <a name="move-the-source-resource-group"></a>移动源资源组

按如下所示启动移动：

1. 在“跨区域”中，选择资源组 >“启动移动” 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="用于启动移动的按钮。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. 在“移动资源”中，单击“启动移动” 。 资源组移动到“启动移动进行中”状态。   
3. 启动移动后，根据生成的 ARM 模板创建目标资源组。 源资源组变为“提交移动挂起”状态。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="查看“提交移动已挂起”状态。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

要提交并完成移动过程：

1. 在“跨区域”中，选择资源组 >“提交移动” 。
2. 在“移动资源”中，单击“提交” 。

> [!NOTE]
> 提交移动后，源资源组处于“删除源挂起”状态。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="查看”删除移动已挂起”状态。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>准备要移动的资源

现在，加密资源和源资源组已移动，你可以准备移动其他处于“准备已挂起”状态的资源。


1. 在“跨区域”中，再次验证并解决任何存在的问题。
2. 如果要在开始移动之前编辑目标设置，请在资源的“目标配置”列中选择链接，然后编辑设置。 如果编辑目标 VM 设置，则目标 VM 大小不应小于源 VM 大小。
3. 为要移动的处于“准备已挂起”状态的资源选择“准备”。
3. 在“准备资源”中，选择“准备” 

    - 准备过程中，会在 VM 上安装 Azure Site Recovery 移动代理，以复制它们。
    - VM 数据定期复制到目标区域。 这不会影响源 VM。
    - 资源移动会为其他源资源生成 ARM 模板。

准备资源后，它们处于“启动移动挂起”状态。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="显示处于“启动移动已挂起”状态的资源的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>启动移动

准备好资源后，现在可以启动移动。 

1. 在“跨区域”中，选择状态“启动移动挂起”的资源。 然后单击“启动移动”。
2. 在“移动资源”中，单击“启动移动” 。
3. 跟踪通知栏中的移动进度。

    - 对于 VM，在目标区域中创建副本 VM。 源 VM 已关闭，并出现一段停机时间（通常为几分钟）。
    - 资源转移器使用已准备的 ARM 模板重新创建其他资源。 通常不会停机。
    - 移动资源后，它们处于“提交移动挂起”状态。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="显示处于“提交移动已挂起”状态的资源的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>放弃还是提交？

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果要测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会将资源返回到“启动移动挂起”状态。
- **提交**：提交即完成移动到目标区域的操作。 提交后，源资源将处于“删除源挂起”状态，你可以决定是否要删除它。


## <a name="discard-the-move"></a>放弃移动 

你可以按如下所示放弃移动：

1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后单击“放弃移动”  。
2. 在“放弃移动”中，单击“放弃” 。
3. 跟踪通知栏中的移动进度。


> [!NOTE]
> 放弃资源后，VM 处于“启动移动挂起”状态。

## <a name="commit-the-move"></a>提交移动

如果要完成移动过程，请提交移动。 

1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后单击“提交移动”。
2. 在“提交资源”中，单击“提交” 。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="用于提交资源以完成移动的页面。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. 跟踪通知栏中的提交进度。

> [!NOTE]
> - 提交移动后，VM 将停止复制。 源 VM 不受提交影响。
> - 提交不会影响源网络资源。
> - 提交移动后，资源处于“删除源挂起”状态。



## <a name="configure-settings-after-the-move"></a>在移动后配置设置

- 不会自动从 VM 卸载移动服务。 手动卸载它，或者如果你计划再次移动服务器，则保留它。
- 移动后，修改 Azure 基于角色的访问控制 (Azure RBAC) 规则。

## <a name="delete-source-resources-after-commit"></a>提交后删除源资源

移动后，可以选择删除源区域中的资源。 

1. 在“跨区域”中，选择要删除的每个源资源。 然后选择“删除资源”。
2. 在“删除源”中，查看需要删除的内容，并在“确认删除”中，键入“是”  。 该操作不可逆，因此请仔细检查！
3. 在键入“是”后，选择“删除源” 。

> [!NOTE]
>  在“资源移动”门户中，无法删除资源组、密钥保管库或 SQL Server 服务器。 需要分别从每个资源的属性页中删除这些资源。


## <a name="delete-additional-resources-created-for-move"></a>删除为移动创建的其他资源

移动后，你可以手动删除移动集合和创建的 Site Recovery 资源。

- 默认情况下，移动集合处于隐藏状态。 要查看它，你需要打开隐藏的资源。
- 必须先删除对缓存存储的锁定，才能删除缓存存储。

按如下所示删除： 
1. 找到资源组 ```RegionMoveRG-<sourceregion>-<target-region>``` 中的资源。
2. 查看是否已移动或删除源区域中的所有 VM 和其他源资源。 这可确保没有挂起的资源使用它们。
2. 删除资源：

    - 移动集合名称为 ```movecollection-<sourceregion>-<target-region>```。
    - 缓存存储账户名称为 ```resmovecache<guid>```
    - 保管库名称为 ```ResourceMove-<sourceregion>-<target-region>-GUID```。
## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 已将加密的 Azure VM 及其依赖资源移动到了另一个 Azure 区域。


现在，尝试将 Azure SQL 数据库和弹性池移动到另一区域。

> [!div class="nextstepaction"]
> [移动 Azure SQL 资源](./tutorial-move-region-sql.md)
