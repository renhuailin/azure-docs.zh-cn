---
title: Azure CLI 脚本示例 - Batch 中的 Windows 池 | Microsoft Docs
description: 了解 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Windows 计算节点池的命令。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: windows 池, azure cli 示例, azure cli 代码示例, azure cli 脚本代码
ms.openlocfilehash: 6c98d5ece314f6aa8603db73a0a5c0c9a420efde
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595154"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Windows 池

此脚本演示了 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Windows 计算节点池的命令。 可通过两种方式配置 Windows 池：使用云服务配置，或使用虚拟机配置。 此示例介绍如何使用云服务配置来创建 Windows 池。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。 |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | 创建计算节点池。  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | 更新池的属性。  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | 显示池的属性。  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | 对池禁用自动缩放。 |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
