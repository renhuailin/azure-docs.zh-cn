---
title: 创建连接到 Azure Cosmos DB 的函数应用 - Azure CLI
description: Azure CLI 脚本示例 - 创建用于连接到 Azure Cosmos DB 的 Azure Function
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786278"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>创建用于连接到 Azure Cosmos DB 的 Azure Function

此 Azure Functions 示例脚本先创建一个函数应用，然后将该函数连接到 Azure Cosmos DB 数据库。 创建的应用设置（包含连接）可以与 [Azure Cosmos DB 触发器或绑定](../functions-bindings-cosmosdb.md)配合使用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将 Cosmos DB 终结点和访问密钥添加到应用设置。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 使用相关位置创建资源组 |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | 创建存储帐户 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | 在无服务器[消耗计划](../consumption-plan.md)中创建函数应用。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | 创建 Azure Cosmos DB 数据库。 |
| [az cosmosdb show](/cli/azure/cosmosdb#az_cosmosdb_show)| 获取数据库帐户连接。 |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| 获取数据库密钥。 |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | 将连接字符串设置为函数应用中的应用设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
