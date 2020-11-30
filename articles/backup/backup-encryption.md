---
title: Azure 备份中的加密
description: 了解 Azure 备份中的加密功能如何帮助你保护备份数据并满足企业的安全需求。
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325667"
---
# <a name="encryption-in-azure-backup"></a>Azure 备份中的加密

将所有备份数据存储在云中时，会使用 Azure 存储加密自动对其进行加密，这有助于履行安全性与合规性承诺。 此静态数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密，并符合 FIPS 140-2 规范。 除了静态加密以外，所有传输中的备份数据将通过 HTTPS 传输。 这些数据始终保留在 Azure 主干网络上。

## <a name="levels-of-encryption-in-azure-backup"></a>Azure 备份中的加密级别

Azure 备份提供两个级别的加密：

- **恢复服务保管库中的数据加密**
  - **使用平台管理的密钥**：默认情况下，所有数据将使用平台托管的密钥进行加密。 无需从你的终端执行任何明确操作即可实现此加密。 这种加密适用于要备份到恢复服务保管库的所有工作负荷。
  - **使用客户管理的密钥**：备份 Azure 虚拟机时，可以选择使用所拥有和管理的加密密钥来加密数据。 Azure 备份允许你使用存储在 Azure 密钥保管库中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据受到基于 AES 256 的数据加密密钥 (DEK) 的保护，而 DEK 又受到你的密钥的保护。 这使你可以完全控制数据和密钥。 若要允许加密，需要向恢复服务保管库授予对 Azure Key Vault 中的加密密钥的访问权限。 可以根据需要禁用密钥或撤销访问权限。 但是，在你尝试保护保管库中的任何项目之前，必须先使用你的密钥启用加密。 [在此处了解更多信息](encryption-at-rest-with-cmk.md)。
  - **基础结构级别的加密**：除了使用客户管理的密钥对恢复服务保管库中的数据进行加密外，还可以选择在存储基础结构上配置额外的加密层。 此基础结构加密由平台管理。 使用客户管理的密钥与静态加密一起使用，可以对备份数据进行两层加密。 只有在您首次选择使用自己的密钥进行静态加密时，才能配置基础结构加密。 基础结构加密使用平台托管密钥来加密数据。
- **特定于要备份的工作负荷的加密**  
  - **Azure 虚拟机备份**：Azure 备份支持对特定 VM 进行备份，这些 VM 包含的磁盘使用 [平台管理的密钥](../virtual-machines/disk-encryption.md#platform-managed-keys)以及你拥有和管理的 [客户管理的密钥](../virtual-machines/disk-encryption.md#customer-managed-keys)进行加密。 此外，还可以备份已使用 [Azure 磁盘加密](backup-azure-vms-encryption.md#encryption-support-using-ade)将其 OS 磁盘或数据磁盘加密的 Azure 虚拟机。 ADE 使用适用于 Windows VM 的 BitLocker 以及适用于 Linux VM 的 DM-Crypt 来执行来宾内部加密。

>[!NOTE]
>基础结构加密目前处于有限预览版中，仅在美国东部、美国 West2、美国中南部、US Gov 亚利桑那州和美国 GOV 弗吉尼亚地区提供。 如果要在这些区域中使用此功能，请填写 [此表格](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) ，并通过电子邮件发送电子邮件 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
- [Azure 备份常见问题解答](backup-azure-backup-faq.md#encryption)，解答有关加密的任何问题