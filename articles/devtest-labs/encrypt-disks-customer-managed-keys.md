---
title: 使用客户管理的密钥加密 OS 磁盘
description: 了解如何在 Azure 开发测试实验室中使用客户管理的密钥来加密操作系统 (OS) 磁盘。
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 2da0e89c5a3f34d9ba967088ea3881f6a30bc1e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632341"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中使用客户管理的密钥来加密操作系统 (OS) 磁盘
服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 将 Azure 中的托管磁盘（OS 磁盘和数据磁盘）上存储的数据保存到云时，SSE 在默认情况下会自动对这些数据进行静态加密。 详细了解 Azure 上的[磁盘加密](../virtual-machines/disk-encryption.md)。 

在开发测试实验室中，作为实验室的一部分创建的所有 OS 磁盘和数据磁盘都使用平台管理的密钥进行加密。 但是，作为实验室所有者，你可以选择使用自己的密钥来加密实验室虚拟机 OS 磁盘。 如果你选择使用自己的密钥来管理加密，可以指定一个客户管理的密钥，用于加密实验室 OS 磁盘中的数据。 若要详细了解采用客户管理的密钥的服务器端加密 (SSE) 以及其他托管磁盘加密类型，请参阅[客户管理的密钥](../virtual-machines/disk-encryption.md#customer-managed-keys)。 另请参阅[使用客户管理的密钥时的限制](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)。

> [!NOTE]
> - 使用客户管理的密钥进行的磁盘加密当前仅适用于开发测试实验室中的 OS 磁盘。 
> 
> - 此设置应用于实验室中新创建的 OS 磁盘。 如果你选择在某个时间点更改磁盘加密集，则实验室中较早的磁盘将继续使用以前的磁盘加密集进行加密。 

以下部分介绍了实验室所有者如何使用客户管理的密钥设置加密。

## <a name="pre-requisites"></a>先决条件

1. 如果没有磁盘加密集，请按照[设置 Key Vault 和磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md)一文的要求操作。 请注意磁盘加密集的以下要求： 

    - 磁盘加密集需要与实验室位于同一区域和订阅中。 
    - 请确保你（实验室所有者）至少对将用于加密实验室 OS 磁盘的磁盘加密集具有“读取者级别访问权限”。 
1. 对于在 2020 年 8 月 1 日之前创建的实验室，实验室所有者需要确保启用实验室系统分配的标识。 为此，实验室所有者可以转到其实验室，单击“配置和策略”，接着单击“标识(预览版)”边栏选项卡，将系统分配的标识的“状态”更改为“启用”，然后单击“保存”。 对于在 2020 年 8 月 1 日之后创建的新实验室，将默认启用实验室的系统分配的标识。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="托管密钥":::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在磁盘加密集上显式授予实验室的系统分配标识读取者角色，并在基础 Azure 订阅上显式授予虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的[“用户访问管理员”角色](../role-based-access-control/built-in-roles.md#user-access-administrator)的成员，以便你可以管理用户对 Azure 资源的访问。 
    1. 在“磁盘加密集”页面上，选择左侧菜单上的“访问控制(IAM)”。 
    1. 在工具栏上选择“+ 添加”，然后选择“添加角色分配”。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="添加角色管理 - 菜单":::
    1. 在“添加角色分配”页面上，选择“读取者”角色或允许更多访问权限的角色。 
    1. 键入要对其使用磁盘加密集的实验室名称，然后从下拉列表中选择实验室名称（实验室的系统分配的标识）。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="选择实验室的系统管理的标识":::        
    1. 在工具栏上选择“保存”。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="保存角色分配":::
3.  使用“订阅” -> “访问控制(IAM)”页面将实验室的系统分配的标识添加到“虚拟机参与者”角色。 步骤类似于前面的步骤。 

    
    1. 导航到 Azure 门户中的“订阅”页。 
    1. 选择“访问控制 (IAM)”。 
    1. 在工具栏上选择“+添加”，然后选择“添加角色分配”。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="“订阅”->“访问控制(IAM)”页":::
    1. 在“添加角色分配”页中，选择“经典虚拟机参与者”作为角色。
    1. 键入实验室名称，然后从下拉列表中选择实验室名称（实验室的系统分配的标识）。 
    1. 在工具栏上选择“保存”。 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>使用客户管理的密钥加密实验室 OS 磁盘 

1. 在 Azure 门户中的实验室主页上，选择左侧菜单中的“配置和策略”。 
1. 在“配置和策略”页上，选择“加密”部分的“磁盘(预览版)”。 默认情况下，“加密类型”设置为“使用平台管理的密钥进行静态加密”。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="“配置和策略”页面的“磁盘”选项卡":::
1. 对于“加密类型”，请从下拉列表中选择“使用客户管理的密钥进行静态加密”。 
1. 对于“磁盘加密集”，请选择之前创建的磁盘加密集。 它与实验室的系统分配标识可访问的磁盘加密集相同。
1. 在工具栏上选择“保存”。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="启用通过客户管理的密钥进行加密的功能":::
1. 在包含以下文本的消息框上选择“确定”：此设置将应用于实验室中新创建的计算机。旧 OS 磁盘仍将使用旧磁盘加密集保持加密。 

    配置完成后，将使用通过磁盘加密集提供的客户管理的密钥来加密实验室 OS 磁盘。 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>如何验证磁盘是否被加密

1. 转到一个实验室虚拟机，该虚拟机是在实验室中启用通过客户管理的密钥进行磁盘加密的功能后创建的。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="已启用磁盘加密的 VM":::
1. 单击 VM 的资源组，然后单击 OS 磁盘。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM 资源组":::
1. 转到“加密”，验证是否已通过所选磁盘加密集将加密设置为客户管理的密钥。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="验证加密":::
  
## <a name="next-steps"></a>后续步骤

请参阅以下文章： 

- [Azure 磁盘加密](../virtual-machines/disk-encryption.md)。 
- [客户管理的密钥](../virtual-machines/disk-encryption.md#customer-managed-keys)