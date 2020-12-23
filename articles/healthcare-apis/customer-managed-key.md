---
title: 为 Azure API for FHIR 配置客户管理的密钥
description: Azure API for FHIR 中通过 Cosmos DB 支持的自带密钥功能
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398175"
---
# <a name="configure-customer-managed-keys"></a>配置客户管理的密钥

创建新的 Azure API for FHIR 帐户时，默认使用 Microsoft 托管密钥对数据进行加密。 现在，可以使用自己选择和管理的密钥为数据添加第二层加密。

在 Azure 中，通常使用客户的 Azure Key Vault (AKV) 中的加密密钥来完成此操作。 例如，目前 Azure SQL、Azure 存储和 Cosmos DB 等均提供此功能。 Azure API for FHIR 利用 Cosmos DB 对此功能的支持。 创建帐户时，可以选择指定 AKV 密钥 URI。 预配 DB 帐户后，我们会将此密钥传递给 Cosmos DB。 发出 FHIR 请求后，Cosmos DB 会提取你的密钥，并使用它来加密/解密数据。 若要开始使用，可以参考以下链接：

- [为 Azure 订阅注册 Azure Cosmos DB 资源提供程序](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [配置 AKV 实例](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [将访问策略添加到 AKV 实例](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [在 AKV 中生成密钥](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

在 Azure 门户中创建 Azure API for FHIR 帐户之后，可以在“其他设置”选项卡上的“数据库设置”下看到“数据加密”配置选项。默认情况下，将选择“服务托管密钥”选项。 你可以在此处通过选择“客户管理的密钥”选项来指定 AKV 密钥。 可以在此处输入复制的密钥 URI。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="创建 Azure API for FHIR":::

或者，可以从 KeyPicker 中选择密钥：

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

对于现有的 FHIR 帐户，可以在“数据库”边栏选项卡中查看密钥加密选项（服务托管密钥或客户管理的密钥），如下所示。 选择配置选项后，将无法对其进行修改。 但是，可以修改和更新密钥。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

此外，你还可以创建指定密钥的新版本，此后使用该新版本加密数据就不会发生任何服务中断。 也可以删除对密钥的访问权限，从而删除对数据的访问权限。