---
title: 托管磁盘加密选项概述
description: 托管磁盘加密选项概述
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: efc0626c0c5f2c9333e5459d7fb7ce6aa2865cc4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105178"
---
# <a name="overview-of-managed-disk-encryption-options"></a>托管磁盘加密选项概述

托管磁盘可以使用多种加密类型，包括 Azure 磁盘加密 (ADE)、服务器端加密 (SSE) 和主机加密。

- **Azure 磁盘加密** 有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 ADE 可通过使用 Linux 的 [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) 功能或 Windows 的 [BitLocker](https://wikipedia.org/wiki/BitLocker) 功能，为 Azure 虚拟机 (VM) 的操作系统和数据磁盘提供卷加密。 ADE 与 Azure Key Vault 集成，用于控制和管理磁盘加密密钥与机密。  有关完整详细信息，请参阅[适用于 Linux VM 的 Azure 磁盘加密](./linux/disk-encryption-overview.md)或[适用于 Windows VM 的 Azure 磁盘加密](./windows/disk-encryption-overview.md)。

- 将 Azure 托管磁盘（操作系统和数据磁盘）上存储的数据保存到云时，服务器端加密（也称为静态加密或 Azure 存储加密）会自动对这些数据进行加密。  有关完整详细信息，请参阅 [Azure 磁盘存储的服务器端加密](./disk-encryption.md)。

- 主机加密可确保将存储在 VM 主机上的数据静态加密，并以加密形式将其传送到存储服务。 启用主机加密的磁盘不能通过 SSE 进行加密；托管 VM 的服务器会为数据加密，并且该加密数据将传送到 Azure 存储。有关完整详细信息，请参阅[主机加密 - VM 数据的端到端加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="comparison"></a>比较

下面是 SSE、ADE 和主机加密的比较结果。

| | 静态加密（操作系统和数据磁盘） | 临时磁盘加密 | 缓存加密 | 在计算和存储之间加密的数据流 | 客户控制密钥 | Azure 安全中心磁盘加密状态 |
|--|--|--|--|--|--|--|
| 使用平台管理的密钥进行静态加密 (SSE+PMK) | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | 不正常，如果忽略，则不适用 |
| 使用客户管理的密钥进行静态加密 (SSE+CMK) | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | 不正常，如果忽略，则不适用 |
| **Azure 磁盘加密** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | 正常 |
| 主机加密  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | 不正常，如果忽略，则不适用 |

> [!Important]
> 对于主机加密，Azure 安全中心检测不到加密状态。

## <a name="next-steps"></a>后续步骤

- [适用于 Linux VM 的 Azure 磁盘加密](./linux/disk-encryption-overview.md)
- [适用于 Windows VM 的 Azure 磁盘加密](./windows/disk-encryption-overview.md)
- [Azure 磁盘存储的服务器端加密](./disk-encryption.md)
- [主机加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 安全基础 - Azure 加密概述](../security/fundamentals/encryption-overview.md)
