---
title: 快速入门 - 使用 Azure PowerShell 创建 Azure Key Vault
description: 介绍如何使用 Azure PowerShell 创建 Azure Key Vault 的快速入门
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070174"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>快速入门：使用 PowerShell 创建密钥保管库

Azure Key Vault 是一项云服务，它为[密钥](../keys/index.yml)、[机密](../secrets/index.yml)和[证书](../certificates/index.yml)提供了安全的存储。 有关 Key Vault 的详细信息，请参阅[关于 Azure Key Vault](overview.md)；有关密钥保管库中可存储的内容的详细信息，请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本快速入门中，你将使用 [Azure PowerShell](/powershell/azure/) 创建一个密钥保管库。 如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Azure PowerShell 创建了 Key Vault。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](overview.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/) 参考
- 请参阅 [Azure Key Vault 安全性概述](security-overview.md)

