---
title: Azure CLI 脚本示例 - 运行 Batch 作业 | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Batch 作业，并将一系列任务添加到作业。 本文还演示如何监视作业及其任务。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, batch 作业, 监视作业, azure cli 示例, azure cli 代码示例, azure cli 脚本代码
ms.openlocfilehash: 293e517a528b6a1630ff8597f4fa17c87008a78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595165"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI 示例：使用 Azure Batch 运行作业和任务

此脚本将创建一个批处理作业，并将一系列任务添加到该作业。 它还演示了如何监视作业及其任务。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | 创建计算节点池。  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | 创建批处理作业。  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | 将任务添加到指定的批处理作业。  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | 更新批处理作业的属性。  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | 检索指定批处理作业的详细信息。  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | 从指定的批处理作业中检索任务的详细信息。  |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
