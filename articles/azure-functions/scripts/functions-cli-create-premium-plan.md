---
title: 在高级计划中创建函数应用 - Azure CLI
description: 使用 Azure CLI 在 Azure 中的可缩放高级计划中创建函数应用
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d9f72fa433364f8d71ba44207d570bb827cd243
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786173"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>在高级计划中创建函数应用 - Azure CLI

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 所创建的函数应用使用[可缩放的高级计划](../functions-premium-plan.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

此脚本会创建一个使用[高级计划](../functions-premium-plan.md)的函数应用。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 创建 Azure 存储帐户。 |
| [az functionapp plan create](/cli/azure/functionapp/plan#az_functionapp_plan_create) | 在[特定 SKU](../functions-premium-plan.md#available-instance-skus) 中创建高级计划。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | 在应用服务计划中创建函数应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
