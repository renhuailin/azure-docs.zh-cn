---
title: 使用 Azure CLI 创建无服务器函数应用
description: 使用 Azure CLI 在 Azure 中创建可无服务器执行的函数应用。
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1d4508572d284347af9df961b324f44ec1518f88
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786110"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>创建适合无服务器代码执行的函数应用 

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 将使用[消耗计划](../consumption-plan.md)创建最适合事件驱动无服务器工作负荷的函数应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../consumption-plan.md)创建 Azure 函数应用。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 创建 Azure 存储帐户。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | 创建 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
