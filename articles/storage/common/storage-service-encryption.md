---
title: 静态数据的 Azure 存储加密
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户中的数据，也可以使用你自己的密钥来管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 05/11/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: efc1d1567da8a181ca8de336bf26729e3717729b
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790768"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

在将数据保存到云时，Azure 存储会使用服务器端加密 (SSE) 自动对数据加密。 Azure 存储加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-azure-storage-encryption"></a>关于 Azure 存储加密

Azure 存储中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

已为所有存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

不管存储帐户的性能层级（标准或高级）、访问层级（热访问层或冷访问层）或部署模型（Azure 资源管理器或经典）如何，都会将存储帐户中的数据加密。 存档层级中的所有 blob 也都是加密的。 所有 Azure 存储冗余选项都支持加密，当启用了异地复制时，会对主要区域和次要区域中的所有数据进行加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。 Azure 存储加密不会产生额外的费用。

2017 年 10 月 20 日后写入 Azure 存储的每个块 Blob、追加 Blob 或页 Blob 均已加密。 在此日期之前创建的 Blob 继续由后台进程加密。 若要强制对 2017 年 10 月 20 日之前创建的 Blob 进行加密，可以重写 Blob。 若要了解如何检查 Blob 的加密状态，请参阅 [检查 Blob 的加密状态](../blobs/storage-blob-encryption-status.md)。

有关 Azure 存储加密的底层加密模块的详细信息，请参见[加密 API：下一代](/windows/desktop/seccng/cng-portal)。

有关 Azure 托管磁盘的加密和密钥管理的信息，请参阅 [Azure 托管磁盘的服务器端加密](../../virtual-machines/disk-encryption.md)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，新存储帐户中的数据使用 Microsoft 管理的密钥进行加密。 你可以继续依赖于使用 Microsoft 管理的密钥来加密数据，也可以使用你自己的密钥来管理加密。 如果你选择使用自己的密钥来管理加密，则有两种选择。 可以使用任何一种类型的密钥管理，或者使用这两种类型：

- 可以指定客户管理的密钥，用于对 Blob 存储和 Azure 文件存储中的数据进行加密和解密。<sup>1,2</sup> 客户管理的密钥必须存储在 Azure Key Vault 或 Azure Key Vault 托管硬件安全模型 (HSM)（预览版）中。 若要详细了解客户管理的密钥，请参阅[使用客户管理的密钥进行 Azure 存储加密](./customer-managed-keys-overview.md)。
- 可以在 Blob 存储操作中指定客户提供的密钥。 对 Blob 存储发出读取或写入请求的客户端可以在请求中包含加密密钥，以便精细控制 Blob 数据的加密和解密方式。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥](../blobs/encryption-customer-provided-keys.md)。

下表比较了 Azure 存储加密的密钥管理选项。

| 密钥管理参数 | Microsoft 管理的密钥 | 客户管理的密钥 | 客户提供的密钥 |
|--|--|--|--|
| 加密/解密操作 | Azure | Azure | Azure |
| 支持的 Azure 存储服务 | 全部 | Blob 存储、Azure 文件存储<sup>1,2</sup> | Blob 存储 |
| 密钥存储 | Microsoft 密钥存储 | Azure Key Vault 或 Key Vault HSM | 客户自己的密钥存储 |
| 密钥轮换责任 | Microsoft | 客户 | 客户 |
| 密钥控制 | Microsoft | 客户 | 客户 |

<sup>1</sup> 若要了解如何创建支持在队列存储中使用客户管理的密钥的帐户，请参阅[为队列创建支持客户管理的密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>1</sup> 若要了解如何创建支持在表存储中使用客户管理的密钥的帐户，请参阅[为表创建支持客户管理的密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

> [!NOTE]
> Microsoft 管理的密钥会根据合规性要求进行适当的轮换。 如果有特定密钥轮换要求，Microsoft 建议你改为使用客户管理的密钥，以便自行管理和审核轮换。

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>通过基础结构加密对数据进行双重加密

如果客户要求较高级别的数据安全保证，则客户还可以在 Azure 存储基础结构级别启用 256 位 AES 加密。 启用基础结构加密后，将对存储帐户中的数据进行两次加密 &mdash; 分别在服务级别和基础架构级别 &mdash; 使用两种不同的加密算法和两个不同的密钥。 Azure 存储数据的双重加密可以在其中一种加密算法或密钥可能泄露的情况下提供保护。 在此方案中，附加的一层加密会继续保护你的数据。

服务级别加密支持将 Microsoft 管理的密钥或客户管理的密钥与 Azure Key Vault 配合使用。 基础结构级别的加密依赖于 Microsoft 管理的密钥并始终使用单独的密钥。

若要详细了解如何创建启用基础结构加密的存储帐户，请参阅[创建启用了基础结构加密的存储帐户以便对数据进行双重加密](infrastructure-encryption-enable.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
- [用于 Azure 存储加密的客户管理的密钥](customer-managed-keys-overview.md)
- [Blob 存储的加密范围](../blobs/encryption-scope-overview.md)
- [在对 Blob 存储的请求中提供加密密钥](../blobs/encryption-customer-provided-keys.md)
