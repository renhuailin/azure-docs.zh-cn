---
title: CLI 脚本 - 重启/停止/启动 Azure Database for MySQL 灵活服务器（预览版）
description: 该 Azure CLI 示例脚本演示了如何重启/停止/启动 Azure Database for MySQL 灵活服务器。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 2c4d17b0bcbba1c231b06f6112ef8b43e9b4786a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604650"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>使用 Azure CLI 重启/停止/启动 Azure Database for MySQL 灵活服务器（预览版）

该示例 CLI 脚本会在 Azure Database for MySQL 灵活服务器上执行重启、启动和停止操作。 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]


> [!IMPORTANT]
> 当你停止服务器时，它将在接下来的 7 天中一直保持该状态。 如果在此期间未手动启动服务器，则服务器将在 7 天结束时自动启动。 如果不使用服务器，则可以选择再次停止。

在服务器停止期间，无法在服务器上执行任何管理操作。 若要更改服务器上的任何配置设置，需要启动服务器。 

在执行停止/启动操作之前，另请参阅[停止/启动限制](../concepts-limitations.md#stopstart-operation)。


[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

编辑脚本中突出显示的行，将变量替换为你的值。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/restart-start-stop.sh?highlight=7,10-11 "Create a server, perform restart / start / stop operations.")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建托管数据库的灵活服务器。|
|[az mysql flexible-server stop](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_stop)|停止灵活服务器。|
|[az mysql flexible-server start](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_start)|启动灵活服务器。|
|[az mysql flexible-server restart](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restart)|重启灵活服务器。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|删除灵活服务器。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[适用于 Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。