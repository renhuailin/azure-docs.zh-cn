---
title: QnA Maker 静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍 QnA Maker 的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 84f95e4c1d24ee100efbd0572dfe0dc88aca22d3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369470"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker 静态数据加密

将数据持久保存到云中时，QnA Maker 可自动对其加密，帮助满足组织的安全性和合规性目标。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 此外，还可以选择使用自己的密钥（称为“客户管理的密钥 (CMK)“）来管理订阅。 使用 CMK，可更加灵活地创建、轮换、禁用和撤销访问控制。 此外，你还可以审核用于保护数据的加密密钥。 如果为订阅配置 CMK，则会获得双重加密，提供第二层保护，同时还可以通过 Azure Key Vault 来控制加密密钥。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 使用 Azure 搜索中的 CMK 支持。 [使用 Azure Key Vault 在 Azure 搜索中配置 CMK](../../search/search-security-manage-encryption-keys.md)。 此 Azure 实例必须与 QnA Maker 服务相关联，才能对其启用 CMK。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

自定义问答使用 [Azure 搜索中的 CMK 支持](../../search/search-security-manage-encryption-keys.md)，并自动关联提供的 CMK，以便对 Azure 搜索索引中存储的数据加密。

---

> [!IMPORTANT]
> 你的 Azure 搜索服务资源必须创建于 2019 年 1 月之后，且不能位于免费（共享）层。 当前不支持在 Azure 门户中配置客户管理的密钥。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

QnA Maker 服务使用 Azure 搜索服务中的 CMK。 请按照下列步骤启用 CMK：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

1. 创建一个新的 Azure 搜索实例，并实现[适用于 Azure 认知搜索的客户管理密钥先决条件](../../search/search-security-manage-encryption-keys.md#prerequisites)中所述的先决条件。

   ![查看加密设置 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 创建 QnA Maker 资源后，它会自动与 Azure 搜索实例相关联。 此实例不能与 CMK 一起使用。 要使用 CMK，需要关联步骤 1 中创建的 Azure 搜索的新建实例。 具体而言，需要更新 QnA Maker 资源中的 `AzureSearchAdminKey` 和 `AzureSearchName`。

   ![查看加密设置 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下来，创建一个新的应用程序设置：
   * **名称**：设置为 `CustomerManagedEncryptionKeyUrl`
   * **值**：使用步骤 1 中创建 Azure 搜索实例时获得的值。

   ![查看加密设置 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成后，重新启动运行时。 现在，你的 QnA Maker 服务则已启用 CMK。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

1.  在启用了自定义问答（预览版）功能的情况下，转到文本分析服务的“加密”选项卡。
2.  选择“客户管理的密钥”选项。 提供[客户管理的密钥](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal)的详细信息，然后单击“保存”。

> [!div class="mx-imgBorder"]
> ![问答 CMK](media/question-answering-cmk.png)
   
3.  成功保存后，系统将使用 CMK 来加密 Azure 搜索索引中存储的数据。

> [!IMPORTANT]
> 建议你在创建任何知识库之前，在全新的 Azure 认知搜索服务中设置 CMK。 如果在已有知识库的文本分析服务中设置 CMK，你可能会失去对它们的访问权限。 了解有关在 Azure 认知搜索中[使用加密内容](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content)的详细信息。

> [!NOTE]
> 要申请使用客户管理的密钥的权限，请填写并提交[认知服务客户管理的密钥申请表](https://aka.ms/cogsvc-cmk)。

---

## <a name="regional-availability"></a>区域可用性

所有 Azure 搜索区域均可使用客户管理的密钥。

## <a name="encryption-of-data-in-transit"></a>传输中的数据加密

QnA Maker 门户在用户的浏览器中运行。 每个操作都会触发直接调用相应的认知服务 API。 因此，QnA Maker 适合用于传输中的数据。
不过，由于 QnA Maker 门户服务托管在美国西部，所以对于非美国客户，这并非理想之选。 

## <a name="next-steps"></a>后续步骤

* [在 Azure Key Vault 中使用 CMK 对 Azure 搜索加密](../../search/search-security-manage-encryption-keys.md)
* [静态数据加密](../../security/fundamentals/encryption-atrest.md)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)
