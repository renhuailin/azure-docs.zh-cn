---
title: 使用客户管理的密钥加密 Azure HPC 缓存中的数据
description: 如何配合使用 Azure Key Vault 和 Azure HPC 缓存来控制加密密钥访问，而不是使用默认的 Microsoft 管理的加密密钥
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: ae4c52ec1390166eccb0e73d6f81a8553c445b2e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813283"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>将客户管理的加密密钥用于 Azure HPC 缓存

可以使用 Azure Key Vault 来控制用于加密 Azure HPC 缓存中数据的密钥的所有权。 本文介绍如何使用客户管理的密钥进行缓存数据加密。

> [!NOTE]
> 默认情况下，Azure 中存储的所有数据（包括缓存磁盘上存储的所有数据）都使用 Microsoft 管理的密钥进行静态加密。 如果要管理用于对数据进行加密的密钥，则只需按照本文中的步骤进行操作。

即使为缓存磁盘添加了客户密钥，用于保存已缓存数据的托管磁盘上的 [VM 主机加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)也为 Azure HPC 缓存提供保护。 添加客户管理的密钥进行双重加密可为具有高安全性需求的客户提供额外的安全级别。 若要了解详细信息，请阅读 [Azure 磁盘存储的服务器端加密](../virtual-machines/disk-encryption.md)。

为 Azure HPC 缓存启用客户管理的密钥加密有三个步骤：

1. 设置用于存储密钥的 Azure Key Vault。
1. 创建 Azure HPC 缓存时，请选择客户管理的密钥加密，并指定要使用的密钥保管库和密钥。
1. 创建缓存后，对其进行授权以访问密钥保管库。

通过新创建的缓存对加密进行授权之前，加密不会完全设置好（步骤 3）。 这是因为必须将缓存的标识传递到密钥保管库使其成为授权用户。 无法在创建缓存之前执行此操作，因为在创建缓存之前该标识不存在。

创建缓存后，无法在客户管理的密钥与 Microsoft 管理的密钥之间进行更改。 但是，如果缓存使用客户管理的密钥，则可以根据需要[更改](#update-key-settings)加密密钥、密钥版本和密钥保管库。

## <a name="understand-key-vault-and-key-requirements"></a>了解密钥保管库和密钥要求

密钥保管库和密钥必须满足这些要求才能使用 Azure HPC 缓存。

密钥保管库属性：

* **订阅** - 使用用于缓存的同一订阅。
* **区域** - 密钥保管库必须与 Azure HPC 缓存位于同一区域。
* **定价层** - 标准层足以用于 Azure HPC 缓存。
* **软删除** - 如果尚未在密钥保管库上配置 Azure HPC 缓存，Azure HPC 缓存会启用软删除。
* **清除保护** - 必须启用清除保护。
* **访问策略** - 默认设置已足够。
* **网络连接** - 无论选择什么终结点设置，Azure HPC 缓存都必须能够访问密钥保管库。

密钥属性：

* **密钥类型** - RSA
* **RSA 密钥大小** - 2048
* **启用** - 是

密钥保管库访问权限：

* 创建 Azure HPC 缓存的用户必须具有与 [Key Vault 参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)等同的权限。 设置和管理 Azure Key Vault 需要相同的权限。

  有关详细信息，请阅读[保护对密钥保管库的访问](../key-vault/general/security-features.md)。

## <a name="1-set-up-azure-key-vault"></a>1. 设置 Azure Key Vault

可以在创建缓存之前设置密钥保管库和密钥，或者在创建缓存过程中执行此操作。 请确保这些资源满足[上述](#understand-key-vault-and-key-requirements)要求。

创建缓存时，必须指定用于缓存加密的保管库、密钥和密钥版本。

有关详细信息，请阅读 [Azure Key Vault 文档](../key-vault/general/overview.md)。

> [!NOTE]
> Azure Key Vault 使用的订阅必须与 Azure HPC 缓存所用的相同，并且其所在的区域也必须与 Azure HPC 缓存所在的相同。 请确保所选区域[支持这两种产品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 在启用客户管理的密钥的情况下创建缓存

创建 Azure HPC 缓存时，必须指定加密密钥源。 按照[创建 AZURE HPC 缓存](hpc-cache-create.md)中的说明进行操作，并在“磁盘加密密钥”页中指定密钥保管库和密钥。 可以在创建缓存期间创建新的密钥保管库和密钥。

> [!TIP]
> 如果未显示“磁盘加密密钥”页，请确保缓存位于[受支持的区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)之一。

创建缓存的用户必须具有与 [Key Vault 参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)相同或比之更高的权限。

1. 单击按钮以启用专门管理的密钥。 更改此设置后，将显示密钥保管库设置。

1. 单击“选择密钥保管库”，打开密钥选择页面。 选择或创建密钥保管库和密钥以在此缓存的磁盘上加密数据。

   如果 Azure Key Vault 未显示在列表中，请检查以下要求：

   * 缓存是否在与密钥保管库使用相同的订阅？
   * 缓存是否在与密钥保管库位于相同的区域？
   * Azure 门户与密钥保管库之间是否存在网络连接？

1. 选择保管库后，从可用选项中选择单个密钥，或创建新密钥。 密钥必须是 2048 位的 RSA 密钥。

1. 指定所选密钥的版本。 在 [Azure Key Vault 文档](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)中了解有关版本控制的详细信息。

继续完成剩余的规定并按照[创建 AZURE HPC 缓存](hpc-cache-create.md)中所述创建缓存。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 通过缓存对 Azure Key Vault 加密进行授权
<!-- header is linked from create article, update if changed -->

几分钟后，新的 Azure HPC 缓存会出现在 Azure 门户中。 转到“概述”页，授权其访问 Azure Key Vault，并启用客户管理的密钥加密。

> [!TIP]
> 现在“部署正在进行”消息消失之前，缓存可能出现在资源列表中。 一两分钟后检查资源列表，而不是等待成功通知。

这个两步过程是必需的，因为 Azure HPC 缓存实例需要标识才能传递到 Azure Key Vault 进行授权。 缓存标识直到其初始创建步骤完成后才会存在。

> [!NOTE]
> 创建缓存后，你必须在 90 分钟内对加密进行授权。 如果没有完成此步骤，缓存将超时并失败。 必须重新创建失败的缓存，无法对其进行修复。

缓存将显示状态“正在等待密钥”。 单击页面顶部的“启用加密”按钮，授权缓存访问指定的密钥保管库。

![门户中缓存概述页的屏幕截图，突出显示了“启用加密”按钮（最上面的部行）和状态“正在等待密钥”](media/waiting-for-key.png)

 单击“启用加密”，然后单击“是”按钮，授权缓存使用加密密钥。 此操作还在密钥保管库中启用软删除和清除保护（如果尚未启用）。

![门户中缓存概述页的屏幕截图，顶部有一个横幅消息要求用户通过单击“是”来启用加密](media/enable-keyvault.png)

缓存请求访问密钥保管库后，它就可以创建并加密存储缓存数据的磁盘。

授权加密之后，Azure HPC 缓存再花几分钟进行设置，以创建加密磁盘和相关的基础结构。

## <a name="update-key-settings"></a>更新密钥设置

可以从 Azure 门户更改缓存的密钥保管库、密钥或密钥版本。  单击缓存的“加密”设置链接，打开“客户密钥设置”页。

不能在客户管理的密钥和系统管理的密钥之间更改缓存。

![“客户密钥设置”页的屏幕截图，单击 Azure 门户中缓存页上的“设置”>“加密”可转到该页面](media/change-key-click.png)

 单击“更改密钥”链接，然后单击“更改密钥保管库、密钥或版本”以打开密钥选择器。

![“从 Azure Key Vault 中选择密钥”页面的屏幕截图，其中有三个下拉选择器用于选择密钥保管库、密钥和版本](media/select-new-key.png)

与此缓存位于同一订阅和区域中的密钥保管库显示在列表中。

选择新的加密密钥值后单击“选择”。 将显示具有新值的确认页。 单击“保存”以完成选择。

![确认页面的屏幕截图，左上角是“保存”按钮](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>阅读 Azure 中客户管理的密钥的详细信息

这些文章详细介绍了如何使用 Azure Key Vault 和客户管理的密钥来加密 Azure 中的数据：

* [Azure 存储加密概述](../storage/common/storage-service-encryption.md)
* [使用客户管理的密钥进行磁盘加密](../virtual-machines/disk-encryption.md#customer-managed-keys) - 本文介绍如何将 Azure Key Vault 与托管磁盘配合使用，这是 Azure HPC 缓存类似

## <a name="next-steps"></a>后续步骤

创建 Azure HPC 缓存并授权基于 Key Vault 的加密后，通过向缓存授予对数据源的访问权限来继续设置缓存。

* [添加存储目标](hpc-cache-add-storage.md)
