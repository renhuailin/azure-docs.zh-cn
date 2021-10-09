---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - Batch 服务 | Microsoft Docs
description: 通过这个 Azure CLI 脚本示例，了解如何在 Batch 服务模式下创建 Batch 帐户。 此脚本还演示如何查询或更新帐户的各种属性。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, azure cli 示例, azure cli 代码示例, azure cli 脚本代码
ms.openlocfilehash: 0e909f644fddae91a664b11abdbba0ae8c71b2ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548382"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI 示例：在 Batch 服务模式下创建 Batch 帐户

此脚本在 Batch 服务模式下创建 Azure Batch 帐户，并演示如何查询或更新该帐户的各个属性。 在默认 Batch 服务模式下创建 Batch 帐户时，其计算节点由 Batch 服务在内部分配。 分配的计算节点受到单独的 vCPU（核心）配额的限制。可以通过共享密钥凭据或 Azure Active Directory 令牌对帐户进行身份验证。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | 创建批处理帐户。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 创建存储帐户。 |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | 更新批处理帐户的属性。  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | 检索指定批处理帐户的详细信息。  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | 检索指定批处理帐户的访问密钥。  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
