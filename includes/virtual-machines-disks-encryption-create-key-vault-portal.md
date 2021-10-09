---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de89ce807b69cf9d9ef7db180b32dd1b7fe1d50c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091142"
---
如果你是第一次执行此操作，则为磁盘设置客户托管密钥时将要求你按特定顺序创建资源。 首先，需要创建并设置 Azure Key Vault。

## <a name="set-up-your-azure-key-vault"></a>设置 Azure 密钥保管库

1. 登录到 [Azure 门户](https://aka.ms/diskencryptionupdates)。
1. 搜索并选择“Key Vault”。

    [![Azure 门户的屏幕截图，其中展开了“搜索”对话框。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure Key Vault、磁盘加密集、VM、磁盘和快照必须都位于同一区域和订阅中才能成功部署。

1. 选择“+创建”以创建新的 Key Vault。
1. 创建新的资源组。
1. 输入 Key Vault 名称，选择区域，然后选择定价层。

    > [!NOTE]
    > 创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为 90 天）内保留已删除的密钥。 清除保护可确保在保留期结束之前，无法永久删除已删除的密钥。 这些设置可防止由于意外删除而丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。

1. 选择“审阅 + 创建”，验证选择，然后选择“创建” 。

    ![Azure Key Vault 创建体验的屏幕截图。 显示所创建的特定值](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Key Vault 部署完成后，请选择它。
1. 在“设置”下，选择“密钥” 。
1. 选择“生成/导入”。

    ![Key Vault 资源设置窗格的屏幕截图。 显示设置中的“生成/导入”按钮。](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. 将“密钥类型”设置为“RSA”，将“RSA 密钥大小”设置为“2048”   。
1. 根据需要填写其余选项，然后选择“创建”。

    ![选择“生成/导入”按钮后出现的“密钥”窗格的屏幕截图](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>添加 Azure RBAC 角色

你已创建 Azure 密钥保管库和密钥，现在必须添加 Azure RBAC 角色，以便可以将 Azure 密钥保管库用于磁盘加密集。

1. 选择“访问控制(IAM)”并添加角色。
1. 添加“Key Vault 管理员”、“所有者”或“参与者”角色。

## <a name="set-up-your-disk-encryption-set"></a>设置磁盘加密集

1. 搜索“磁盘加密集”并选择它。
1. 在“磁盘加密集”窗格中，选择“+创建”。
1. 选择资源组，命名加密集，然后选择与 Key Vault 相同的区域。
1. 对于“SSE 加密类型”，请选择“使用客户管理的密钥进行静态加密”。

    > [!NOTE]
    > 一旦创建了具有特定加密类型的磁盘加密集，就无法对其进行更改。 如果要使用其他加密类型，则必须创建新的磁盘加密集。

1. 选择“单击以选择密钥”。
1. 选择先前创建的密钥保管库和密钥，以及版本。
1. 按“选择”。
1. 如果要启用[客户管理的密钥的自动轮换](../articles/virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)，请选择“自动密钥轮换”。
1. 选择“审阅 + 创建”，然后选择“创建” 。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png" alt-text="“磁盘加密创建”窗格的屏幕截图。显示订阅、资源组、磁盘加密集名称、区域以及密钥保管库 + 密钥选择器。" lightbox="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png":::

1. 部署磁盘加密集后，导航到该加密集，然后选择显示的警报。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-alert.png" alt-text="用户选择“若要将磁盘、映像或快照与此磁盘加密集关联，必须授予对密钥保管库的权限”警报的屏幕截图。" lightbox="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-alert.png":::

1. 这会向磁盘加密集授予密钥保管库权限。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-confirmation.png" alt-text="确认已授予权限的屏幕截图。" lightbox="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-confirmation.png":::