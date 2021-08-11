---
title: 托管 HSM 数据平面角色管理 - Azure Key Vault | Microsoft Docs
description: 使用本文管理托管 HSM 的角色分配
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7a4179d35faffbf04a70a63aafead120259802f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471256"
---
# <a name="managed-hsm-role-management"></a>托管 HSM 角色管理

> [!NOTE]
> Key Vault 支持两种类型的资源：保管库和托管 HSM。 本文介绍托管 HSM。 若要了解如何管理保管库，请参阅[使用 Azure CLI 管理密钥保管库](../general/manage-with-cli2.md)。

有关托管 HSM 的概述，请参阅[什么是托管 HSM？](overview.md)。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本文介绍如何管理托管 HSM 数据平面的角色。 若要了解托管 HSM 访问控制模型，请参阅[托管 HSM 访问控制](access-control.md)。

若要允许安全主体（例如用户、服务主体、组或托管标识）执行托管 HSM 数据平面操作，需要为它们分配允许执行这些操作的角色。 例如，如果要允许应用程序使用密钥执行签名操作，则必须为其分配一个角色，该角色包含“Microsoft.KeyVault/managedHSM/keys/sign/action”作为数据操作之一。 可以在特定范围内分配角色。 托管 HSM 本地 RBAC 支持两种范围，HSM 范围（`/` 或 `/keys`）和按密钥 (`/keys/<keyname>`)。

有关所有托管 HSM 内置角色及其允许的操作的列表，请参阅[托管 HSM 内置角色](built-in-roles.md)。

## <a name="prerequisites"></a>先决条件

若要使用本文中的 Azure CLI 命令，必须准备好以下各项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 版本 2.25.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 订阅中的托管 HSM。 请参阅[快速入门：使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)，预配和激活托管 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)

## <a name="create-a-new-role-assignment"></a>创建新的角色分配

### <a name="assign-roles-for-all-keys"></a>为所有密钥分配角色

使用 `az keyvault role assignment create` 命令为通过适用于 ContosoHSM 中所有密钥（范围为 `/keys`）的用户主体名称 user2\@contoso.com 标识的用户分配托管 HSM 加密管理人员角色  。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>为特定密钥分配角色

使用 `az keyvault role assignment create` 命令为由名为 myrsakey 的特定密钥的用户主体名称 user2\@contoso.com 标识的用户分配托管 HSM 加密管理人员角色  。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>列出现有的角色分配

使用 `az keyvault role assignment list` 列出角色分配。

在作用域/（未指定 --scope 时则为默认作用域）中为所有用户（未指定 --assignee 时则为默认用户）执行的所有角色分配

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

特定用户 user1@contoso.com 在 HSM 级别的所有角色分配。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> 当范围为 /（或 /keys）时，list 命令仅列出顶级的所有角色分配，而不会显示各个密钥级别的角色分配。

针对特定密钥 myrsakey 的特定用户 user2@contoso.com 的所有角色分配 。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

为特定用户 user2@contoso.com 分配适用于特定密钥 myrsakey 的“托管 HSM 加密管理人员”这一特定角色  


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>删除角色分配

使用 `az keyvault role assignment delete` 命令删除分配给用户 user2\@contoso.com 的适用于密钥 myrsakey2 的“托管 HSM 加密管理人员”角色  。

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>列出所有可用的角色定义

使用 `az keyvault role definition list` 命令列出所有角色定义。

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>创建新的角色定义

托管 HSM 具有几个内置（预定义）角色，适用于大多数常见使用方案。 您可以定义自己的角色，其中包含允许角色执行的特定操作的列表。 然后，您可以将此角色分配给主体，以向其授予对指定操作的权限。 

对使用 JSON 字符串的名为 **我的自定义角色** 的角色使用 `az keyvault role definition create` 命令。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

对名为 **my-custom-role-definition.jso** 的文件中的角色使用 `az keyvault role definition create` 命令，文件中含有角色定义的 JASON 字符串。 请参看上述示例。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>显示角色定义的详细信息

使用 `az keyvault role definition show` 命令查看使用名称 (GUID) 的特定角色定义的详细信息。

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>更新自定义角色定义

使用 `az keyvault role definition update` 命令来更新使用 JSON 字符串的名为 **我的自定义角色** 的角色。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>删除自定义角色定义

使用 `az keyvault role definition delete` 命令查看使用名称 (GUID) 的特定角色定义的详细信息。 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> 无法删除内置角色。 删除自定义角色后，所有使用该自定义角色的角色分配都将失效。


## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 的概述。
- 请参阅有关[托管 HSM 角色管理](role-management.md)的教程
- 详细了解[托管 HSM 访问控制模型](access-control.md)
- 查看[托管 HSM 本地 RBAC 的所有内置角色](built-in-roles.md)
