---
title: 快速入门 - 使用 Azure CLI 创建 Azure Key Vault
description: 介绍如何使用 Azure CLI 创建 Azure Key Vault 的快速入门
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f7f6f5d82c5fda7101e80ddcb8b17dc6bdef6532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070242"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建密钥保管库

Azure Key Vault 是一项云服务，它为[密钥](../keys/index.yml)、[机密](../secrets/index.yml)和[证书](../certificates/index.yml)提供了安全的存储。 有关 Key Vault 的详细信息，请参阅[关于 Azure Key Vault](overview.md)；有关密钥保管库中可存储的内容的详细信息，请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本快速入门需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault 并将其删除。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](overview.md)
- 请参阅 [Azure Key Vault 安全性概述](security-overview.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/azure/keyvault)参考

