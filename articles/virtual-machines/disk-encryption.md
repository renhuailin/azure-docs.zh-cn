---
title: Azure 托管磁盘的服务器端加密
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 可以使用客户管理的密钥通过自己的密钥来管理加密，也可以依赖 Microsoft 托管的密钥来加密你的托管磁盘。
author: roygara
ms.date: 09/03/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 98bbc2f84b0f56c4e50e98e99e7bcaf86588fb48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803972"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure 磁盘存储的服务器端加密

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

大多数 Azure 托管磁盘使用 Azure 存储加密进行加密，后者使用服务器端加密 (SSE) 来保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 将存储在 Azure 托管磁盘（OS 和数据磁盘）上的数据保存到云时，Azure 存储加密在默认情况下会自动对这些数据进行静态加密。 但是，启用了主机加密的磁盘不会通过 Azure 存储进行加密。 对于启用了主机加密的磁盘，托管 VM 的服务器会为数据提供加密，该加密数据会流入 Azure 存储中。

Azure 托管磁盘中的数据使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大分组加密之一）以透明方式加密，且符合 FIPS 140-2 规范。 有关加密模块基础 Azure 托管磁盘的详细信息，请参阅[加密 API：下一代](/windows/desktop/seccng/cng-portal)

Azure 存储加密不会影响托管磁盘的性能，并且不会产生额外的费用。 有关 Azure 存储加密的详细信息，请参阅 [Azure 存储加密](../storage/common/storage-service-encryption.md)。

> [!NOTE]
> 临时磁盘不是托管磁盘，不会由 SSE 加密，除非在主机上启用了加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

可以依赖于平台托管的密钥来加密托管磁盘，也可以使用自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，可以指定一个客户托管密钥，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了密钥管理的每个选项。

### <a name="platform-managed-keys"></a>平台托管的密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 所有写入现有托管磁盘的托管磁盘、快照、映像和数据都会自动使用平台托管密钥进行静态加密。

### <a name="customer-managed-keys"></a>客户管理的密钥

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，必须使用 [Azure PowerShell 模块](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)或 [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 将所有数据复制到完全不同的托管磁盘（未使用客户管理的密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>支持的区域

可在提供托管磁盘的所有区域中使用客户管理的密钥。

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

若要为托管磁盘启用客户管理的密钥，请参阅我们的相关文章，了解如何使用 [Azure PowerShell 模块](windows/disks-enable-customer-managed-keys-powershell.md)、[Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 或 [Azure 门户](disks-enable-customer-managed-keys-portal.md)来启用它。 

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>主机加密 - VM 数据的端到端加密

启用主机加密时，将在 VM 主机本身（VM 分配到的 Azure 服务器）上启动加密。 临时磁盘和 OS/数据磁盘缓存的数据存储在该 VM 主机上。 启用主机加密后，所有这些数据都会静态加密，且流将加密到用于保存数据的存储服务。 实质上，主机加密会从端到端加密数据。 主机加密不会使用 VM 的 CPU，并且不会影响 VM 的性能。 

启用端对端加密后，会使用平台管理的密钥对临时磁盘和临时 OS 磁盘进行静态加密。 OS 和数据磁盘缓存使用客户管理的密钥或平台管理的密钥进行静态加密，具体取决于所选磁盘加密类型。 例如，如果使用客户管理的密钥对磁盘进行加密，则使用客户管理的密钥对磁盘的缓存进行加密，如果使用平台管理的密钥对磁盘进行加密，则使用平台管理的密钥对磁盘的缓存进行加密。

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

你还可以以编程方式找到 VM 大小。 若要了解如何以编程方式进行检索，请参阅 [Azure PowerShell 模块](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes)或 [Azure CLI](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes) 一文的“查找支持的 VM 大小”部分。

若要使用主机加密启用端对端加密，请参阅我们的相关文章，了解如何使用 [Azure PowerShell 模块](windows/disks-enable-host-based-encryption-powershell.md)、[Azure CLI](linux/disks-enable-host-based-encryption-cli.md) 或 [Azure 门户](disks-enable-host-based-encryption-portal.md)来启用它。

## <a name="double-encryption-at-rest"></a>静态双重加密

对安全性高度敏感的客户如果担心出现与任何特定加密算法、实现或密钥泄露相关的风险，现在可以选择在使用平台管理的加密密钥的基础结构层上启用使用其他加密算法/模式的额外加密层。 这个新层可以应用于持久性 OS 和数据磁盘、快照和映像，所有这些都将通过双重加密进行静态加密。

### <a name="supported-regions"></a>支持的区域

双重加密适用于托管磁盘可用的所有区域。

若要为托管磁盘启用静态双重加密，请参阅我们的相关文章，了解如何使用 [Azure PowerShell 模块](windows/disks-enable-double-encryption-at-rest-powershell.md)、[Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 或 [Azure 门户](disks-enable-double-encryption-at-rest-portal.md)来启用它。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能或 Windows 的 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 功能，在来宾 VM 中使用客户管理的密钥来加密托管磁盘。  使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。
> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户托管密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="frequently-asked-questions"></a>常见问题

问：创建托管磁盘时，是否会默认启用服务器端加密？

**答:** 是的。 托管磁盘通过使用平台托管密钥和平台管理的密钥进行加密。

问：默认情况下，托管磁盘上的引导卷是否会加密？

**答:** 是的。 默认情况下，所有托管磁盘均已加密，包括 OS 磁盘。

**问：加密密钥由谁管理？**

答：平台托管密钥由 Microsoft 管理。 你还可以使用和管理 Azure 密钥保管库中存储的你自己的密钥。

问：是否可以为托管磁盘禁用服务器端加密？

**答:** 不是。

问：服务器端加密是否仅适用于特定区域？

**答:** 不是。 在可以使用 Azure 托管磁盘的所有区域中，均可使用使用平台管理的密钥和客户管理的密钥的服务器端加密。

问：对于本地到 Azure 和 Azure 到 Azure 灾难恢复方案，Azure Site Recovery 是否支持使用客户托管密钥的服务器端加密？

**答:** 是的。

问：能否使用 Azure 备份服务来备份由使用客户管理的密钥的服务器端加密加密的托管磁盘？

**答:** 是的。

问：是否已加密托管快照和映像？

**答:** 是的。 所有托管快照和映像均会自动加密。

是否可以将 VM 的位于存储帐户且现在或以前已加密的非托管磁盘转换为托管磁盘？

**答:** 是的。

问：是否同时会加密从托管磁盘或快照导出的 VHD？

**答:** 不是。 但如果将 VHD 从加密托管磁盘或快照导出到加密存储帐户，则会对其进行加密。

## <a name="next-steps"></a>后续步骤

- 通过 [Azure PowerShell 模块](windows/disks-enable-host-based-encryption-powershell.md)、[Azure CLI](linux/disks-enable-host-based-encryption-cli.md) 或 [Azure 门户](disks-enable-host-based-encryption-portal.md)，使用主机加密启用端到端加密。
- 通过 [Azure PowerShell 模块](windows/disks-enable-double-encryption-at-rest-powershell.md)、[Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 或 [Azure 门户](disks-enable-double-encryption-at-rest-portal.md)，为托管磁盘启用静态双重加密。
- 通过 [Azure PowerShell 模块](windows/disks-enable-customer-managed-keys-powershell.md)、[Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 或 [Azure 门户](disks-enable-customer-managed-keys-portal.md)，为托管磁盘启用客户管理的密钥。
- [探索 Azure 资源管理器模板以使用客户管理密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure 密钥保管库？](../key-vault/general/overview.md)
