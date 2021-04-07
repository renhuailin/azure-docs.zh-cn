---
title: 将文件共享装载到 Python 函数应用 - Azure CLI
description: 使用 Azure CLI 创建无服务器 Python 函数应用并装载现有的文件共享。
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00bf0ecc9db3cf369fd75b427dcfba686aed0ed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035115"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>使用 Azure CLI 将文件共享装载到 Python 函数应用

此 Azure Functions 示例脚本会创建一个函数应用，并会在 Azure 文件存储中创建一个共享。 然后，它会装载共享，使函数可以访问数据。  

>[!NOTE]
>创建的函数应用在 Python 版本 3.7 上运行。 Azure Functions 也[支持 Python 版本 3.6 和 3.8](../functions-reference-python.md#python-version)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../consumption-plan.md)在 Azure Functions 中创建函数应用。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建 Azure 存储帐户。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 创建 Function App。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 在存储帐户中创建 Azure 文件存储共享。 | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 在共享中创建目录。 |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 将共享装载到函数应用。 |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | 显示装载到函数应用的文件共享。 | 

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
