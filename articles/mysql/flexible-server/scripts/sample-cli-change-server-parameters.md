---
title: CLI 脚本 - 列出并更改 Azure Database for MySQL 灵活服务器（预览版）的服务器参数
description: 此 Azure CLI 示例脚本演示了如何列出并更改 Azure Database for MySQL 灵活服务器的服务器参数
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: bcb811c9143e97ae070873c4b94396d6a1308d4a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604613"
---
# <a name="list-and-change-server-parameters-of-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>使用 Azure CLI 列出并更改 Azure Database for MySQL 灵活服务器（预览版）的服务器参数

此示例 CLI 脚本列出了 Azure Database for MySQL 灵活服务器的可用[服务器参数](../concepts-server-parameters.md)及其允许的值，并将 max_connections 和全局 time_zone 参数设置为默认值以外的值 。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

使用变量的值编辑脚本中突出显示的行。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/change-server-parameters.sh?highlight=7,10-11 "Change server parameters for Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建托管数据库的灵活服务器。|
|[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_list)|列出灵活服务器的参数值。|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|更新灵活服务器的参数。|
|[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_show)|获取灵活服务器的特定参数值。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|删除灵活服务器。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。