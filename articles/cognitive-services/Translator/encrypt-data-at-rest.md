---
title: 翻译器的静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 允许你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍翻译器的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ff7b9f86cebc3c2479105d2a52aa92a265f8a1b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524392"
---
# <a name="translator-encryption-of-data-at-rest"></a>翻译器的静态数据加密

将上传的数据持久保存到云中时，翻译器会自动对其加密，以构建自定义的翻译模型，这有助于实现组织的安全性和合规性目标。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 的 [256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密方法进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 如果使用支持客户管理的密钥的定价层，则可在 [Azure 门户](https://portal.azure.com)的“加密”部分看到资源的加密设置，如下图所示。

![查看加密设置](../media/cognitive-services-encryption/encryptionblade.png)

对于只支持 Microsoft 管理的加密密钥的订阅，你不会有“加密”部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

默认情况下，订阅使用 Microsoft 托管的加密密钥。 还有一个选项可用于通过你自己的密钥（称为“客户管理的密钥”(CMK)）来管理订阅。 CMK 在创建、轮换、禁用和撤销访问控制方面具有更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。 如果为订阅配置 CMK，则会获得双重加密，这样就会在可以通过 Azure Key Vault 来控制加密密钥的同时提供第二层保护。

> [!IMPORTANT]
> 客户管理的密钥适用于 Translator 服务的所有定价层。 若要请求使用客户管理的密钥的权限，请填写[翻译器客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)并提交。你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 在获允将 CMK 与 Translator 服务配合使用后，你需要创建新的翻译器资源。 在创建了翻译器资源后，可以使用 Azure Key Vault 来设置托管标识。

请按照以下步骤为翻译器启用客户管理的密钥：

1. 创建新的区域翻译器或区域认知服务资源。 此操作不适用于全局资源。
2. 在 Azure 门户中启用托管标识，并添加客户管理的密钥的信息。
3. 在自定义翻译器中创建新工作区，并关联此订阅信息。

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须在同一个区域和同一个 Azure Active Directory (Azure AD) 租户中，但可以在不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/general/overview.md)。

新的认知服务资源始终会使用 Microsoft 管理的密钥进行加密。 当创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，必须使用访问策略对密钥保管库进行预配，这些策略将密钥权限授予与认知服务资源关联的托管标识。 在创建该资源后，托管标识会立即可用。

若要了解如何将客户管理的密钥与 Azure Key Vault 配合使用来进行认知服务加密，请参阅：

- [从 Azure 门户通过 Key Vault 配置客户管理的密钥以进行认知服务加密](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户管理的密钥还会启用系统分配的托管标识，这是 Azure AD 的一项功能。 启用系统分配的托管标识后，此资源将注册到 Azure Active Directory。 注册后，将向托管标识授予在设置客户管理的密钥期间选择的 Key Vault 的访问权限。 你可以详细了解[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则会删除对密钥保管库的访问权限，而使用客户密钥加密的任何数据都将不再可供访问。 任何依赖于此数据的功能都会失效。 还将取消部署任何已经部署的模型。 所有上传的数据都将从自定义翻译器中删除。 如果重新启用托管标识，我们不会自动为你重新部署模型。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，系统会在幕后自动分配一个托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与资源关联的托管标识不会转移到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性 。

认知服务加密只支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/general/about-keys-secrets-certificates.md)中的“Key Vault 密钥”。

> [!NOTE]
> 如果删除整个密钥保管库，则不会再显示你的数据，并会取消部署你的所有模型。 所有上传的数据都将从自定义翻译器中删除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤销访问权限实际上会阻止访问认知服务资源中的所有数据，并且会取消部署你的模型，因为认知服务无法访问加密密钥。 所有上传的数据也都会从自定义翻译器中删除。


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)
