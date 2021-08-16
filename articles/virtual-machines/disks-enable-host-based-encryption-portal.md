---
title: 通过主机加密启用端到端加密 - Azure 门户 - 托管磁盘
description: 通过主机加密在 Azure 托管磁盘 - Azure 门户上启用端到端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/14/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ad053a0e97a8efa50fbb01798e639fb33e769bef
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112078757"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure 门户通过主机加密来启用端到端加密

启用主机加密时，存储在 VM 主机上的数据将静态加密，且已加密的数据将流向存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅：

* Linux：[主机加密 - VM 数据的端到端加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

* Windows：[主机加密 - VM 数据的端到端加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>先决条件

必须先为订阅启用此功能，然后才能使用 VM/VMSS 的 EncryptionAtHost 属性。 请按照以下步骤为订阅启用该功能：

1. Azure 门户：在 [Azure 门户](https://portal.azure.com)中选择 Cloud Shell 图标：

    ![用于从 Azure 门户启动 Cloud Shell 的图标](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
1.  执行以下命令，为订阅注册该功能

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（需要几分钟）。

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


使用[提供的链接](https://aka.ms/diskencryptionupdates)登录到 Azure 门户。

> [!IMPORTANT]
> 必须使用[提供的链接](https://aka.ms/diskencryptionupdates)访问 Azure 门户。 如果不使用该链接，“主机加密”目前不会显示在公共 Azure 门户中。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>创建 Azure Key Vault 和磁盘加密集

启用该功能后，需要设置 Azure Key Vault 和磁盘加密集（如果尚未设置）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

设置了 Azure Key Vault 和磁盘加密集之后，现在可以部署 VM，它将在主机上使用加密。

1. 搜索“虚拟机”，然后选择“+ 添加”以创建 VM 。
1. 创建新的虚拟机，选择适当的区域和支持的 VM 大小。
1. 根据需要填写“基本”边栏选项卡上的其他值，然后继续填写“磁盘”边栏选项卡 。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="虚拟机创建“基本”边栏选项卡的屏幕截图，其中突出显示了“区域”和“VM 大小”。":::

1. 在“磁盘”边栏选项卡上，为“主机加密”选择“是”  。
1. 根据需要进行剩余选择。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="虚拟机创建“磁盘”边栏选项卡的屏幕截图，其中突出显示了“主机加密”。":::

1. 完成 VM 部署过程，选择适合环境的选项。

现在，你已经部署了一个启用了主机加密的 VM，与它相关的所有磁盘都将使用主机加密进行加密。

## <a name="next-steps"></a>后续步骤

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
