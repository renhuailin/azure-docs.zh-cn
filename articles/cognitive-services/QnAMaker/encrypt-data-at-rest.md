---
title: 静态数据加密 QnA Maker
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍了 QnA Maker 静态的数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524394"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>静态数据加密 QnA Maker

QnA Maker 在将数据保存到云时，会自动对其进行加密，从而有助于满足组织的安全性和符合性目标。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 此外，还可以选择用自己的密钥来管理你的订阅，名为客户管理的密钥 (CMK) 。 CMK 提供更大的灵活性来创建、轮换、禁用和撤消访问控制。 此外，你还可以审核用于保护数据的加密密钥。 如果为你的订阅配置了 CMK，则会提供双加密，这提供了另一层保护，同时允许你通过 Azure Key Vault 控制加密密钥。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 使用 Azure 搜索中的 CMK 支持。 [使用 Azure Key Vault 在 Azure 搜索中配置 CMK](../../search/search-security-manage-encryption-keys.md)。 此 Azure 实例应该与 QnA Maker 服务相关联，以使其 CMK 启用。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

QnA Maker 使用 [azure 搜索中的 CMK 支持](../../search/search-security-manage-encryption-keys.md)，并自动关联提供的 CMK 以加密存储在 Azure 搜索索引中的数据。

---

> [!IMPORTANT]
> 你的 Azure 搜索服务资源必须在2019年1月之后创建，且不能处于免费 (共享) 层。 不支持在 Azure 门户中配置客户管理的密钥。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

QnA Maker 服务使用 Azure 搜索服务中的 CMK。 请按照以下步骤启用 Cmk：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

1. 创建新的 Azure 搜索实例，并启用 [适用于 Azure 认知搜索的客户托管密钥先决条件](../../search/search-security-manage-encryption-keys.md#prerequisites)中所述的先决条件。

   ![查看加密设置1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 创建 QnA Maker 资源时，它会自动与 Azure 搜索实例相关联。 此实例不能与 CMK 一起使用。 若要使用 CMK，需要将在步骤1中创建的新创建的 Azure 搜索实例关联起来。 具体而言，需要 `AzureSearchAdminKey` `AzureSearchName` 在 QnA Maker 资源中更新和。

   ![查看加密设置2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下来，创建新的应用程序设置：
   * **名称**：设置为 `CustomerManagedEncryptionKeyUrl`
   * **值**：在创建 Azure 搜索实例时使用在步骤1中获得的值。

   ![查看加密设置3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成后，重新启动运行时。 现在，QnA Maker 服务已启用 CMK。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

1.  中转到 QnA Maker 托管 (预览版) 服务的 " **加密** " 选项卡。
2.  选择 " **客户托管密钥** " 选项。 提供 [客户管理的密钥](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) 的详细信息，并单击 " **保存**"。

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker managed (Preview) CMK 设置" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  成功保存后，CMK 将用于加密 Azure 搜索索引中存储的数据。

> [!IMPORTANT]
> 建议在创建任何知识库之前，在全新的 Azure 认知搜索服务中设置 CMK。 如果使用现有的知识库在 QnA Maker 服务中设置 CMK，则可能会失去对它们的访问权限。 阅读有关在 Azure 认知搜索中使用 [加密内容](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) 的详细信息。

> [!NOTE]
> 若要请求使用客户管理的密钥的功能，请填写并提交 [认知服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)。

---

## <a name="regional-availability"></a>区域可用性

客户托管的密钥可用于所有 Azure 搜索区域。

## <a name="encryption-of-data-in-transit"></a>传输中的数据加密

QnA Maker 门户在用户的浏览器中运行。 每个操作都会触发直接调用各自的认知服务 API。 因此，QnA Maker 符合传输中的数据。
但是，因为 QnA Maker 门户服务托管在美国西部，所以对于非美国客户，这仍不是理想之选。 

## <a name="next-steps"></a>后续步骤

* [在 Azure Key Vault 中使用 Cmk 在 Azure 搜索中进行加密](../../search/search-security-manage-encryption-keys.md)
* [静态数据加密](../../security/fundamentals/encryption-atrest.md)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)