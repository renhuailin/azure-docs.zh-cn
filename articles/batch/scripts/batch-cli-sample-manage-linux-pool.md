---
title: Azure CLI 脚本示例 - Batch 中的 Linux 池 | Microsoft Docs
description: 了解 Azure CLI 中一些可用于在 Azure Batch 中创建和管理 Linux 计算节点池的命令。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: linux, azure cli 示例, azure cli 代码示例, azure cli 脚本代码
ms.openlocfilehash: 0c56a5baaaf107b82172b741a5407437cab777a6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677770"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Linux 池

此脚本演示了 Azure CLI 中一些可用于在 Azure Batch 中创建和管理 Linux 计算节点池的命令。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | 创建计算节点池。  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | 显示池的属性。  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | 从指定的池中删除列出的节点。  |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
