---
title: Azure 备份中的加密
description: 了解 Azure 备份中的加密功能如何帮助你保护备份数据并满足企业的安全需求。
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 7870a84d374bce90a9d41896a7c8704b42629d38
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291371"
---
# <a name="encryption-in-azure-backup"></a>Azure 备份中的加密

将所有备份数据存储在云中时，会使用 Azure 存储加密自动对其进行加密，这有助于履行安全性与合规性承诺。 此静态数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密，并符合 FIPS 140-2 规范。 除了静态加密以外，所有传输中的备份数据将通过 HTTPS 传输。 这些数据始终保留在 Azure 主干网络上。

## <a name="levels-of-encryption-in-azure-backup"></a>Azure 备份中的加密级别

Azure 备份提供两个级别的加密：

- **恢复服务保管库中的数据加密**
  - **使用平台管理的密钥**：默认情况下，所有数据将使用平台托管的密钥进行加密。 无需从你的终端执行任何明确操作即可实现此加密。 这种加密适用于要备份到恢复服务保管库的所有工作负荷。
  - 使用客户管理的密钥：备份 Azure 虚拟机时，你可以选择使用自己拥有和管理的密钥来加密数据。 Azure 备份允许你使用存储在 Azure 密钥保管库中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据受到基于 AES 256 的数据加密密钥 (DEK) 的保护，而 DEK 又受到你的密钥的保护。 这使你可以完全控制数据和密钥。 若要允许加密，需要向恢复服务保管库授予对 Azure Key Vault 中加密密钥的访问权限。 可以根据需要禁用密钥或撤销访问权限。 但是，在你尝试保护保管库中的任何项目之前，必须先使用你的密钥启用加密。 [在此处了解更多信息](encryption-at-rest-with-cmk.md)。
  - 基础结构级别加密：除了使用客户管理的密钥对恢复服务保管库中的数据进行加密外，还可以选择在存储基础结构上配置额外的一层加密。 此基础结构加密由平台管理。 结合使用客户管理的密钥进行的静态加密，它允许对备份数据进行双层加密。 仅当你首先选择使用自己的密钥进行静态加密时，才能配置基础结构加密。 基础结构加密使用平台管理的密钥来加密数据。
- **特定于要备份的工作负荷的加密**  
  - **Azure 虚拟机备份**：Azure 备份支持对特定 VM 进行备份，这些 VM 包含的磁盘使用 [平台管理的密钥](../virtual-machines/disk-encryption.md#platform-managed-keys)以及你拥有和管理的 [客户管理的密钥](../virtual-machines/disk-encryption.md#customer-managed-keys)进行加密。 此外，还可以备份已使用 [Azure 磁盘加密](backup-azure-vms-encryption.md#encryption-support-using-ade)将其 OS 磁盘或数据磁盘加密的 Azure 虚拟机。 ADE 使用适用于 Windows VM 的 BitLocker 以及适用于 Linux VM 的 DM-Crypt 来执行来宾内部加密。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
- [Azure 备份常见问题解答](/azure/backup/backup-azure-backup-faq#encryption)，解答有关加密的任何问题