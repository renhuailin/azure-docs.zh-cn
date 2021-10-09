---
title: 快速入门 - 在 Azure Key Vault 中设置和检索机密
description: 快速入门介绍如何使用 Azure CLI 在 Azure Key Vault 中设置和检索机密
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cae19bc96cc689a3cb10f1c9820f95e13c9b4e4a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593701"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure Key Vault 中设置和检索机密

在本快速入门中，你将使用 Azure CLI 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure CLI 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，即可存储机密。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

本快速入门需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

只需再执行几个步骤即可向保管库添加机密。 此密码可供应用程序使用。 此密码将名为 **ExamplePassword**，将在其中存储的值为 **hVFkk965BuUv**。

使用以下 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 命令在密钥保管库中创建一个名为 **ExamplePassword** 的机密，该机密将存储值 **hVFkk965BuUv**：

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>从 Key Vault 检索机密

现在，可以通过使用密码的 URI，引用已添加到 Azure Key Vault 的此密码。 使用 **`https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword`** 获取当前版本。

若要以纯文本格式查看机密中包含的值，请使用 Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) 命令：

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

现在，你已创建 Key Vault 并存储和检索了机密。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Key Vault 并在其中存储了一个机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 了解如何[在密钥保管库中存储多行机密](multiline-secrets.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/azure/keyvault)参考
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
