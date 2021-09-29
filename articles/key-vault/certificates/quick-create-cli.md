---
title: 快速入门 - 使用 Azure CLI 设置和查看 Azure Key Vault 证书
description: 以快速入门的方式介绍如何使用 Azure CLI 在 Azure Key Vault 中设置和检索证书
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 265a66b6aa80dddb6ca4d8da8567ded8c7a380ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663775"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure Key Vault 中设置和检索证书

在本快速入门中，你将使用 Azure CLI 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure CLI 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，你将存储证书。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本快速入门需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>将证书添加到 Key Vault

若要向保管库中添加证书，只需再执行几个步骤即可。 此证书可供应用程序使用。 

键入以下命令，使用名为“ExampleCertificate”  的默认策略创建自签名证书：

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

现在，可以通过 URI 来引用已添加到 Azure Key Vault 的此证书。 使用 **`https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate`** 获取当前版本。 

若要查看以前存储的证书，请使用以下命令：

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

现在，你已创建了一个密钥保管库，存储了一个证书，并检索了该证书。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/azure/keyvault)参考
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
