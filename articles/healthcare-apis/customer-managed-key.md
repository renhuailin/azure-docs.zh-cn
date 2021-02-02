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
ms.openlocfilehash: 3879280f56a4b99d8e6e08a9c9ed852ef2cafa68
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747317"
---
# <a name="configure-customer-managed-keys-at-rest"></a>配置客户管理的静态密钥

创建新的 Azure API for FHIR 帐户时，默认使用 Microsoft 托管密钥对数据进行加密。 现在，可以使用自己选择和管理的密钥为数据添加第二层加密。

在 Azure 中，通常使用客户的 Azure Key Vault 中的加密密钥来完成此操作。 例如，目前 Azure SQL、Azure 存储和 Cosmos DB 等均提供此功能。 Azure API for FHIR 利用 Cosmos DB 对此功能的支持。 创建帐户时，可以选择指定 Azure Key Vault 密钥 URI。 预配 DB 帐户后，此密钥将被传递给 Cosmos DB。 发出 FHIR 请求后，Cosmos DB 会提取你的密钥，并使用它来加密/解密数据。 若要开始使用，可以参考以下链接：

- [为 Azure 订阅注册 Azure Cosmos DB 资源提供程序](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [配置 Azure Key Vault 实例](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [将访问策略添加到 Azure Key Vault 实例](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [在 Azure Key Vault 中生成密钥](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>指定 Azure Key Vault 密钥

在 Azure 门户中创建 Azure API for FHIR 帐户时，可以在“其他设置”选项卡上的“数据库设置”下看到“数据加密”配置选项。默认情况下，将选择“服务托管密钥”选项。 

你可以从 KeyPicker 中选择密钥：

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

也可在此处选择“客户管理的密钥”选项，以指定 Azure Key Vault 密钥。 你可以在此处输入密钥 URI：

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="创建 Azure API for FHIR":::

对于现有的 FHIR 帐户，可以在“数据库”边栏选项卡中查看密钥加密选项（服务托管密钥或客户管理的密钥），如下所示。 选择配置选项后，将无法对其进行修改。 但是，可以修改和更新密钥。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

此外，你还可以创建指定密钥的新版本，此后使用该新版本加密数据就不会发生任何服务中断。 也可以删除对密钥的访问权限，从而删除对数据的访问权限。 禁用该密钥后，查询将导致错误。 如果重新启用该密钥，查询将再次成功。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何配置客户管理的静态密钥。 接下来，可以查看 Azure Cosmos DB 常见问题解答部分： 
 
>[!div class="nextstepaction"]
>[Cosmos DB：如何设置 CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
