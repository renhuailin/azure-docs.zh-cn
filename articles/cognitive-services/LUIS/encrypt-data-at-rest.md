---
title: 语言理解服务静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍语言理解 (LUIS) 的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524400"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>语言理解服务静态数据加密

语言理解服务在将数据保存到云时会自动加密数据。 语言理解服务加密可保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 的 [256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密方法进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 还有一个选项可用于通过你自己的密钥（称为“客户管理的密钥”(CMK)）来管理订阅。 CMK 在创建、轮换、禁用和撤销访问控制方面具有更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

你还可以选择使用自己的密钥来管理订阅。 客户管理的密钥 (CMK)（也称为创建自己的密钥 (BYOK)）在创建、轮换、禁用和撤销访问控制方面具有更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须在同一个区域和同一个 Azure Active Directory (Azure AD) 租户中，但可以在不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/general/overview.md)。

### <a name="customer-managed-keys-for-language-understanding"></a>用于语言理解的客户管理的密钥

若要请求使用客户管理的密钥的能力，请填写并提交  [LUIS 服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 在被批准将 CMK 与 LUIS 配合使用后，需要从 Azure 门户创建新的语言理解资源并选择 E0 作为定价层。 新 SKU 的功能与已提供的 F0 SKU 功能相同（CMK 除外）。 用户将无法从 F0 升级到新的 E0 SKU。

![LUIS 订阅图像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>限制

将 E0 层用于现有的/以前创建的应用程序时，存在一些限制：

* 向 E0 资源的迁移将被阻止。 用户只能将其应用迁移到 F0 资源。 将现有资源迁移到 F0 后，可以在 E0 层中创建新的资源。 在此处详细了解[迁移](./luis-migration-authoring.md)。  
* 向/从 E0 资源移动应用程序将被阻止。 若要避开此限制，请导出现有的应用程序，并将其作为 E0 资源导入。
* 不支持必应拼写检查功能。
* 如果你的应用程序是 E0，则会禁用记录最终用户流量。
* 对于 E0 层中的应用程序，Azure 机器人服务中的语音启动功能不受支持。 此功能通过 Azure 机器人服务提供，该服务不支持 CMK。
* 门户中的语音启动功能需要 Azure Blob 存储。 有关详细信息，请参阅[自带存储](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

新的认知服务资源始终会使用 Microsoft 管理的密钥进行加密。 当创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，必须使用访问策略对密钥保管库进行预配，这些策略将密钥权限授予与认知服务资源关联的托管标识。 只有在使用 CMK 的定价层创建资源后，托管标识才可用。

若要了解如何通过 Azure Key Vault 使用客户管理的密钥来进行认知服务加密，请参阅：

- [从 Azure 门户通过 Key Vault 配置客户管理的密钥以进行认知服务加密](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户管理的密钥还会启用系统分配的托管标识，这是 Azure AD 的一项功能。 启用系统分配的托管标识后，此资源将注册到 Azure Active Directory。 注册后，将向托管标识授予在设置客户管理的密钥期间选择的 Key Vault 的访问权限。 你可以详细了解[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则会删除对密钥保管库的访问权限，而使用客户密钥加密的任何数据都将不再可供访问。 任何依赖于此数据的功能都会失效。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，系统会在幕后自动分配一个托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与资源关联的托管标识不会转移到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性 。

认知服务加密只支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/general/about-keys-secrets-certificates.md)中的“Key Vault 密钥”。

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 轮换密钥后，必须更新认知服务资源才能使用新的密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅[使用 Azure 门户配置认知服务的客户管理的密钥](../Encryption/cognitive-services-encryption-keys-portal.md)中标题为“更新密钥版本”的部分。

轮换密钥不会触发资源中数据的重新加密。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤销访问权限实际上会阻止对认知服务资源中所有数据的访问，因为认知服务无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

* [LUIS 服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)
