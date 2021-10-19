---
title: 选择如何使用 Azure CLI 授权 Blob 数据访问
titleSuffix: Azure Storage
description: 指定如何使用 Azure CLI 授权针对 Blob 数据的数据操作。 可以使用 Azure AD 凭据、帐户访问密钥或共享访问签名 (SAS) 令牌授权数据操作。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/12/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 537259315a766da179a47defc277da5abecc4779
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859145"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>选择如何使用 Azure CLI 授权 Blob 数据访问

Azure 存储提供适用于 Azure CLI 的扩展，使你能够指定如何授权针对 Blob 数据的操作。 可通过以下方式授权数据操作：

- 使用 Azure Active Directory (Azure AD) 安全主体。 Microsoft 建议使用 Azure AD 凭据来实现优异的安全性和易用性。
- 使用帐户访问密钥或共享访问签名 (SAS) 令牌。

## <a name="specify-how-data-operations-are-authorized"></a>指定数据操作的授权方式

用于读取和写入 Blob 数据的 Azure CLI 命令包括可选的 `--auth-mode` 参数。 指定此参数可以指示数据操作的授权方式：

- 将 `--auth-mode` 参数设置为 `login` 可以使用 Azure AD 安全主体登录（建议）。
- 将 `--auth-mode` 参数设置为传统 `key` 值可以尝试检索用于授权的帐户访问密钥。 如果省略 `--auth-mode` 参数，则 Azure CLI 也会尝试检索访问密钥。

若要使用 `--auth-mode` 参数，请确保已安装 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 以查看已安装版本。

> [!NOTE]
> 在使用 Azure 资源管理器 ReadOnly 锁锁定了某个存储帐户时，不允许对该存储帐户执行[列出密钥](/rest/api/storagerp/storageaccounts/listkeys)操作。 “列出密钥”是 POST 操作，并且在为该帐户配置了 ReadOnly 锁时，所有的 POST 操作都会被阻止 。 出于此原因，在使用 ReadOnly 锁锁定了帐户时，尚没有帐户密钥的用户必须使用 Azure AD 凭据来访问 blob 数据。

> [!IMPORTANT]
> 如果省略 `--auth-mode` 参数或将其设置为 `key`，则 Azure CLI 会尝试使用帐户访问密钥进行授权。 在这种情况下，Microsoft 建议在命令或 AZURE_STORAGE_KEY 环境变量中提供访问密钥。 有关环境变量的详细信息，请参阅标题为[为授权参数设置环境变量](#set-environment-variables-for-authorization-parameters)的部分。
>
> 如果不提供访问密钥，则 Azure CLI 会尝试调用 Azure 存储资源提供程序来检索每个操作的访问密钥。 执行多个需要调用资源提供程序的数据操作可能会导致发生限制。 有关资源提供程序限制的详细信息，请参阅 [Azure 存储资源提供程序的可伸缩性和性能目标](../common/scalability-targets-resource-provider.md)。

## <a name="authorize-with-azure-ad-credentials"></a>使用 Azure AD 凭据授权

使用 Azure AD 凭据登录 Azure CLI 时，会返回 OAuth 2.0 访问令牌。 Azure CLI 自动使用该令牌针对 Blob 或队列存储进行后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可通过 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure AD 安全主体分配对 blob 数据的权限。 有关 Azure 存储中 Azure 角色的详细信息，请参阅[分配用于访问 Blob 数据的 Azure 角色](assign-azure-role-data-access.md)。

### <a name="permissions-for-calling-data-operations"></a>用于调用数据操作的权限

Azure 存储扩展支持针对 blob 数据的操作。 可调用的操作取决于向 Azure AD 安全主体授予的权限，此安全主体用于登录 Azure CLI。 对 Azure 存储容器的权限通过 Azure RBAC 进行分配。 例如，如果为你分配了“存储 Blob 数据读取者”角色，你可以运行从容器读取数据的脚本命令。 如果为你分配了“存储 Blob 数据参与者”角色，你可以运行脚本命令来读取、写入或删除容器或其所包含的数据。

若要详细了解针对容器的每个 Azure 存储操作所需的权限，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>示例：授权操作使用 Azure AD 凭据创建容器

以下示例演示如何在 Azure CLI 中使用 Azure AD 凭据创建容器。 若要创建容器，需要登录到 Azure CLI，并需要一个资源组和存储帐户。 若要了解如何创建这些资源，请参阅[快速入门：使用 Azure CLI 创建、下载和列出 Blob](../blobs/storage-quickstart-blobs-cli.md)。

1. 创建容器之前，请向自己分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 有关分配 Azure 角色的详细信息，请参阅[分配用于访问 Blob 数据的 Azure 角色](assign-azure-role-data-access.md)。

    > [!IMPORTANT]
    > 传播 Azure 角色分配可能需要几分钟的时间。

1. 在将 `--auth-mode` 参数设置为 `login` 的情况下，调用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令以使用 Azure AD 凭据创建容器。 请务必将尖括号中的占位符值替换为你自己的值：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>使用帐户访问密钥授权

如果你拥有帐户密钥，则可以调用任何 Azure 存储数据操作。 一般情况下，使用帐户密钥的安全性较低。 如果帐户密钥已透露，帐户中的所有数据也可能会透露。

以下示例演示如何使用帐户访问密钥创建容器。 指定帐户密钥，并提供值为 `key` 的 `--auth-mode` 参数：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> 在使用 Azure 资源管理器 ReadOnly 锁锁定了某个存储帐户时，不允许对该存储帐户执行[列出密钥](/rest/api/storagerp/storageaccounts/listkeys)操作。 “列出密钥”是 POST 操作，并且在为该帐户配置了 ReadOnly 锁时，所有的 POST 操作都会被阻止 。 出于此原因，在使用 ReadOnly 锁锁定了帐户时，用户必须使用 Azure AD 凭据访问数据。

## <a name="authorize-with-a-sas-token"></a>使用 SAS 令牌授权

如果你拥有 SAS 令牌，则可以调用 SAS 允许的数据操作。 以下示例演示如何使用 SAS 令牌创建容器：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>为授权参数设置环境变量

可以在环境变量中指定授权参数，以免每次调用 Azure 存储数据操作时都要包含这些参数。 下表描述了可用的环境变量。

| 环境变量                  | 说明                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    存储帐户名称。 此变量应与存储帐户密钥或 SAS 令牌结合使用。 如果该密钥和令牌都不存在，则 Azure CLI 会尝试使用经过身份验证的 Azure AD 帐户检索存储帐户访问密钥。 如果一次性执行大量的命令，可能会达到 Azure 存储资源提供程序的限制。 有关资源提供程序限制的详细信息，请参阅 [Azure 存储资源提供程序的可伸缩性和性能目标](../common/scalability-targets-resource-provider.md)。             |
|    AZURE_STORAGE_KEY                  |    存储帐户密钥。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    一个包含存储帐户密钥或 SAS 令牌的连接字符串。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    共享访问签名 (SAS) 令牌。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    用于运行命令的授权模式。 允许的值为 `login`（建议）或 `key`。 如果指定 `login`，Azure CLI 将使用 Azure AD 凭据来授权数据操作。 如果指定传统 `key` 模式，Azure CLI 会尝试查询帐户访问密钥，并使用该密钥为命令授权。    |

## <a name="next-steps"></a>后续步骤

- [分配 Azure 角色以访问 blob 数据](assign-azure-role-data-access.md)
- [使用 Azure 资源托管标识授予对 Blob 数据的访问权限](authorize-managed-identity.md)
