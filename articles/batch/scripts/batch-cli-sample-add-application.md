---
title: Azure CLI 脚本示例 - 在 Batch 中添加应用程序 | Microsoft Docs
description: 了解如何使用 Azure CLI 添加应用程序以用于 Azure Batch 池或任务。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, azure cli 示例, azure cli 代码示例, azure cli 脚本代码
ms.openlocfilehash: 1c20adef86e30df79a5de2d6229ca099875c98f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595192"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 示例：向 Azure Batch 帐户添加应用程序

此脚本演示如何添加要与 Azure Batch 池或任务配合使用的应用程序。 若要设置添加到 Batch 帐户的应用程序，请将可执行文件与所有依赖文件一起打包为 zip 文件。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0.20 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。
表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 创建存储帐户。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | 创建批处理帐户。 |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | 创建应用程序。  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | 将应用程序包添加到指定的应用程序。  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | 更新应用程序的属性。  |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
