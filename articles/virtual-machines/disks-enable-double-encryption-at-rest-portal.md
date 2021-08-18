---
title: 启用静态双重加密 - Azure 门户 - 托管磁盘
description: 使用 Azure 门户为托管磁盘数据启用静态双重加密。
author: roygara
ms.date: 06/29/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4b8ca72b5446c9adf4320b8dfb670238e973c3ea
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361308"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>使用 Azure 门户为托管磁盘启用静态双重加密

Azure 磁盘存储支持对托管磁盘的静态双重加密。 有关静态双重加密的概念信息以及其他托管磁盘加密类型，请参阅磁盘加密文章的[静态双重加密](disk-encryption.md#double-encryption-at-rest)部分。

## <a name="getting-started"></a>入门

1. 登录 [Azure 门户](https://aka.ms/diskencryptionupdates)。

    > [!IMPORTANT]
    > 必须使用[提供的链接](https://aka.ms/diskencryptionupdates)访问 Azure 门户。 如果不使用该链接，“静态双重加密”目前不会显示在公共 Azure 门户中。

1. 搜索并选择“磁盘加密集”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主 Azure 门户的屏幕截图，搜索栏中突出显示了“磁盘加密集”。":::

1. 选择“+ 添加”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="“磁盘加密集”边栏选项卡的屏幕截图，其中突出显示了“+ 添加”。":::

1. 选择一个支持的区域。
1. 对于“加密类型”，请选择“通过平台管理的密钥和客户管理的密钥进行双重加密”。 

    > [!NOTE]
    > 一旦创建了具有特定加密类型的磁盘加密集，就无法对其进行更改。 如果要使用其他加密类型，则必须创建新的磁盘加密集。

1. 填写剩余信息。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="用于创建磁盘加密集的边栏选项卡的屏幕截图，其中突出显示了“区域”，以及“通过平台管理的密钥和客户管理的密钥进行双重加密”。":::

1. 选择 Azure 密钥保管库和密钥，或者根据需要新建一个。

    > [!NOTE]
    > 创建密钥保管库实例时，必须启用软删除和清除保护。 使用密钥保管库加密托管磁盘时必须使用这些设置，这还可以防止因意外删除而导致数据丢失。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="用于创建密钥保管库的边栏选项卡的屏幕截图。":::

1. 选择“创建”。
1. 导航到创建的磁盘加密集，然后选择显示的错误。 这会配置磁盘加密集，使其可正常工作。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="磁盘加密集显示的错误的屏幕截图，错误文本为：“若要将磁盘、映像或快照关联到此磁盘加密集，必须授予对密钥保管库的权限”。":::

    应会弹出一条通知，此时操作已成功。 这样做使你能够将磁盘加密集与 Key Vault 协同使用。
    
    ![Key Vault 的成功权限和角色分配的屏幕截图。](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. 导航到你的磁盘。
1. 选择“加密”。
1. 对于“加密类型”，请选择“通过平台管理的密钥和客户管理的密钥进行双重加密”。 
1. 选择你的磁盘加密集。
1. 选择“保存”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="托管磁盘的加密边栏选项卡的屏幕截图，其中突出显示了上述加密类型。":::

现已在托管磁盘上启用静态双重加密。


## <a name="next-steps"></a>后续步骤

- [Azure PowerShell - 使用客户管理的密钥进行服务器端加密 - 托管磁盘](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [通过服务器端加密启用客户管理的密钥 - 示例](./linux/disks-enable-customer-managed-keys-cli.md#examples)