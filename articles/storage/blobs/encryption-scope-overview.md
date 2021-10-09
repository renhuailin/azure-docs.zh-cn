---
title: Blob 存储的加密范围
description: 可以通过加密范围在容器或单个 Blob 级别管理加密。 可以使用加密范围在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。
services: storage
author: tamram
ms.service: storage
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 7cb5d96c7651713f02d606be1038f55cd430162f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599233"
---
# <a name="encryption-scopes-for-blob-storage"></a>Blob 存储的加密范围

通过使用加密范围，可以使用范围限定为容器或单个 blob 的密钥来管理加密。 可以使用加密范围在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。

有关使用加密范围的详细信息，请参阅[创建和管理加密范围](encryption-scope-manage.md)。

## <a name="how-encryption-scopes-work"></a>加密范围的工作原理

默认情况下，使用作用域为整个存储帐户的密钥对存储帐户进行加密。 定义加密范围时，需要指定一个可以将范围限定为容器或单个 blob 的密钥。 将加密范围应用于 blob 后，将使用该密钥对 blob 进行加密。 将加密范围应用于容器时，它将充当该容器中 blob 的默认范围，以便可以使用相同的密钥对上传到该容器的所有 blob 进行加密。 可以将容器配置为对容器中的所有 blob 强制实施默认加密范围，或者允许将单个 blob 上传到具有非默认加密范围的容器。

只要未禁用加密范围，针对使用加密范围创建的 blob 的读取操作将以透明方式执行。

### <a name="key-management"></a>密钥管理

定义加密范围时，可以指定是使用 Microsoft 管理的密钥还是使用存储在 Azure Key Vault 中的客户管理的密钥来保护该范围。 同一存储帐户上的不同加密范围可以使用 Microsoft 管理的密钥或客户管理的密钥。 你还可以随时将用于保护加密范围的密钥类型从客户管理的密钥切换到 Microsoft 管理的密钥，反之亦然。 若要详细了解客户管理的密钥，请参阅[用于 Azure 存储加密的客户管理的密钥](../common/customer-managed-keys-overview.md)。 有关 Microsoft 管理的密钥的详细信息，请参阅[关于加密密钥管理](../common/storage-service-encryption.md#about-encryption-key-management)。

如果使用客户管理的密钥定义加密范围，则可以选择自动或手动更新密钥版本。 如果选择自动更新密钥版本，Azure 存储会每天检查密钥保管库或托管 HSM 以查找客户管理的密钥的新版本，并将密钥自动更新到最新版本。 若要详细了解如何更新客户管理的密钥的密钥版本，请参阅[更新密钥版本](../common/customer-managed-keys-overview.md#update-the-key-version)。

Azure Policy 提供了一个内置策略，要求加密范围使用客户管理的密钥。 有关详细信息，请参阅 [Azure Policy 内置策略定义](../../governance/policy/samples/built-in-policies.md#storage)中的“存储”部分。

一个存储帐户最多可以有 10,000 个加密范围由其密钥版本自动更新的客户管理密钥进行保护。 如果你的存储帐户已经有 10,000 个加密范围受自动更新的客户管理密钥的保护，则对于受客户管理密钥保护的其他加密范围，必须手动更新其密钥版本。

### <a name="infrastructure-encryption"></a>基础结构加密

Azure 存储中的基础结构加密支持对数据进行双重加密。 启用基础结构加密之后，将使用两种不同的加密算法和两个不同的密钥对数据进行两次加密（一次在服务级别进行，一次在基础架构级别进行）。

加密范围以及存储帐户级别支持基础结构加密。 如果为帐户启用了基础架构加密，则在该帐户上创建的任何加密范围都会自动使用基础结构加密。 如果未在帐户级别启用基础结构加密，则可以选择在创建加密范围时为该范围启用它。 创建范围后，无法更改加密范围的基础结构加密设置。

有关基础结构加密的详细信息，请参阅[启用基础结构加密，对数据进行双重加密](../common/infrastructure-encryption-enable.md)。

### <a name="encryption-scopes-for-containers-and-blobs"></a>容器和 blob 的加密范围

创建容器时，可以为随后上传到该容器的 blob 指定默认加密范围。 指定容器的默认加密范围时，可以决定如何强制实施默认加密范围：

- 你可以要求上传到容器的所有 blob 都使用默认的加密范围。 在这种情况下，容器中的每个 blob 都用相同的密钥进行加密。
- 你可以允许客户端替代容器的默认加密范围，以便可以使用默认范围以外的加密范围上传 blob。 在这种情况下，可以用不同的密钥加密容器中的 blob。

下表汇总了 blob 上传操作的行为，具体取决于为容器配置默认加密范围的方式：

| 容器上定义的加密范围为… | 使用默认加密范围上传 blob… | 使用默认范围以外的加密范围上传 blob… |
|--|--|--|
| 允许进行替代的默认加密范围 | 成功 | 成功 |
| 禁止替代的默认加密范围 | 成功 | 失败 |

必须在创建容器时为容器指定默认加密范围。

如果没有为容器指定默认加密范围，则可以使用为存储帐户定义的任何加密范围上传 blob。 必须在上传 blob 时指定加密范围。

## <a name="disabling-an-encryption-scope"></a>禁用加密范围

禁用加密范围时，使用该加密范围进行的任何后续读取或写入操作都将失败，并显示 HTTP 错误代码 403（已禁止）。 如果重新启用加密范围，读取和写入操作将再次正常进行。

禁用加密范围后，将不再为此付费。 禁用不需要的任何加密范围以避免不必要的费用。

如果你的加密范围受客户管理的密钥保护，而你撤销了密钥保管库中的密钥，将无法访问数据。 在撤销密钥保管库中的密钥之前，请务必禁用加密范围，以避免对加密范围收费。

请记住，客户管理的密钥受到密钥保管库中的软删除和清除保护功能的保护，删除的密钥受到为这些属性定义的行为的约束。 有关详细信息，请参阅 Azure Key Vault 文档中的以下主题之一：

- [如何在 PowerShell 中使用软删除](../../key-vault/general/key-vault-recovery.md)
- [如何在 CLI 中使用软删除](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> 不能删除加密范围。

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) |
| 高级块 blob          | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
- [创建和管理加密范围](encryption-scope-manage.md)
- [用于 Azure 存储加密的客户管理的密钥](../common/customer-managed-keys-overview.md)
- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
