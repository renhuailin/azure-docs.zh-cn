---
title: 使用 Azure CLI 为容器或 blob 创建用户委托 SAS
titleSuffix: Azure Storage
description: 了解如何使用 Azure CLI 通过 Azure Active Directory 凭据创建用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 536cd01fbcf2c5d18a8c12030b709427d9bb91b1
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703600"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>使用 Azure CLI 为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure CLI 通过 Azure Active Directory (Azure AD) 凭据为容器或 Blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>安装最新版本的 Azure CLI

若要使用 Azure CLI 通过 Azure AD 凭据来保护 SAS，请首先确保你已安装最新版本的 Azure CLI。 若要详细了解如何安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要使用 Azure CLI 创建用户委托 SAS，请确保已安装 2.0.78 或更高版本。 若要检查已安装的版本，请使用 `az --version` 命令。

## <a name="sign-in-with-azure-ad-credentials"></a>使用 Azure AD 凭据登录

使用你的 Azure AD 凭据登录到 Azure CLI。 有关详细信息，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="assign-permissions-with-azure-rbac"></a>通过 Azure RBAC 分配权限

若要通过 Azure PowerShell 创建用户委托 SAS，必须为用来登录到 Azure CLI 的 Azure AD 帐户分配一个包含 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** 操作的角色。 此权限允许 Azure AD 帐户请求用户委托密钥。 用户委托密钥用来对用户委托 SAS 进行签名。 提供 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** 操作的角色必须在存储帐户、资源组或订阅级别进行分配。

如果你没有足够的权限将 Azure 角色分配给 Azure AD 安全主体，则可能需要请求帐户所有者或管理员来分配必要的权限。

下面的示例将分配 **存储 Blob 数据参与者** 角色，该角色包含 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** 操作。 该角色的作用域为存储帐户级别。

请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有关包含 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** 操作的内置角色的详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 凭据来保护 SAS

当你通过 Azure CLI 创建用户委托 SAS 时，系统会为你隐式创建用于对 SAS 进行签名的用户委托密钥。 为 SAS 指定的开始时间和到期时间也用作用户委托密钥的开始时间和到期时间。

由于用户委托密钥的最大时间间隔是从开始日期算起的 7 天内有效，因此，你应为 SAS 指定一个从开始时间算起 7 天内的到期时间。 此 SA 在用户委托密钥过期后无效，因此，到期时间超过 7 天的 SAS 的有效期仍然只有 7 天。

创建用户委托 SAS 时，`--auth-mode login` 和 `--as-user parameters` 是必需的。 为 `--auth-mode` 参数指定 login，以便使用你的 Azure AD 凭据为向 Azure 存储发出的请求授权。 指定 `--as-user` 参数，表示返回的 SAS 应当是一个用户委托 SAS。

### <a name="create-a-user-delegation-sas-for-a-container"></a>为容器创建用户委托 SAS

若要通过 Azure CLI 为容器创建用户委托 SAS，请调用 [az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) 命令。

容器上的用户委托 SAS 支持的权限包括添加、创建、删除、列出、读取和写入。 权限可以单独指定，也可以通过组合方式指定。 有关这些权限的详细信息，请参阅[创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)。

以下示例返回容器的用户委托 SAS 令牌。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

返回的用户委托 SAS 令牌将类似于：

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>为 blob 创建用户委托 SAS

若要通过 Azure CLI 为 blob 创建用户委托 SAS，请调用 [az storage blob generate-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) 命令。

Blob 上的用户委托 SAS 支持的权限包括添加、创建、删除、读取和写入。 权限可以单独指定，也可以通过组合方式指定。 有关这些权限的详细信息，请参阅[创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)。

以下语法返回 blob 的用户委托 SAS。 此示例指定了 `--full-uri` 参数，该参数返回追加有 SAS 令牌的 blob URI。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

返回的用户委托 SAS URI 将类似于：

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 用户委托 SAS 不支持使用存储访问策略来定义权限。

## <a name="revoke-a-user-delegation-sas"></a>撤销用户委托 SAS

若要通过 Azure CLI 撤销用户委托 SAS，请调用 [az storage account revoke-delegation-keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) 命令。 此命令撤销与指定的存储帐户关联的所有用户委托密钥。 与这些密钥关联的所有共享访问签名都会失效。

请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> 用户委托密钥和 Azure 角色分配都是由 Azure 存储缓存的，因此，在启动撤销过程后，可能需要过一段时间现有用户委托 SAS 才会变得无效。

## <a name="next-steps"></a>后续步骤

- [创建用户委托 SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [获取用户委托密钥操作](/rest/api/storageservices/get-user-delegation-key)
