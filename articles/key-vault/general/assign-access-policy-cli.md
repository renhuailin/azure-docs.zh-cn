---
title: 分配 Azure Key Vault 访问策略 (CLI)
description: 如何使用 Azure CLI 将 Key Vault 访问策略分配到安全主体或应用程序标识。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: aec0feb938841e9d6bba6cf876577c67bcaad441
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136465"
---
# <a name="assign-a-key-vault-access-policy"></a>分配 Key Vault 访问策略

Key Vault 访问策略确定给定的安全主体（即用户、应用程序或用户组）是否可以对 Key Vault [机密](../secrets/index.yml)、[密钥](../keys/index.yml)和[证书](../certificates/index.yml)执行不同的操作。 可以使用 [Azure 门户](assign-access-policy-portal.md)、Azure CLI（本文）或 [Azure PowerShell](assign-access-policy-powershell.md) 来分配访问策略。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

若要详细了解如何使用 Azure CLI 在 Azure Active Directory 中创建组，请参阅 [az ad group create](/cli/azure/ad/group#az_ad_group_create) 和 [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add)。

## <a name="configure-the-azure-cli-and-sign-in"></a>配置 Azure CLI 并登录

1. 若要在本地运行 Azure CLI 命令，请安装 [Azure CLI](/cli/azure/install-azure-cli)。
 
    要直接在云中运行命令，请使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

1. 仅限本地 CLI：使用 `az login` 登录到 Azure：

    ```bash
    az login
    ```

    `az login` 命令会打开浏览器窗口来收集凭据（如果需要）。

## <a name="acquire-the-object-id"></a>获取对象 ID

确定要为其分配访问策略的应用程序、组或用户的对象 ID：

- 应用程序和其他服务主体：请使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 命令来检索服务主体。 请检查命令的输出，以确定要为其分配访问策略的安全主体的对象 ID。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 组：请使用 [az ad group list](/cli/azure/ad/group#az_ad_group_list) 命令，并通过 `--display-name` 参数筛选结果：

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 用户：请使用 [az ad user show](/cli/azure/ad/user#az_ad_user_show) 命令，并在 `--id` 参数中传递用户的电子邮件地址：

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>分配访问策略
    
使用 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 命令来分配所需的权限：

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

将 `<object-id>` 替换为安全主体的对象 ID。

为这些特定类型分配权限时，只需包括 `--secret-permissions`、`--key-permissions` 和 `--certificate-permissions`。 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 文档中提供了 `<secret-permissions>`、`<key-permissions>` 和 `<certificate-permissions>` 的允许值。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性](security-features.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
