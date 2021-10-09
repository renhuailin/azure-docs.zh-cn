---
title: CLI 脚本 - 还原 Azure Database for MySQL 灵活服务器（预览版）
description: 此 Azure CLI 示例脚本演示了如何将单一 Azure Database for MySQL 灵活服务器还原到前一个时间点。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: d2e554bfa4d9851687542bbae8d66d51bf270bbd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701535"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>使用 Azure CLI 还原 Azure Database for MySQL 灵活服务器（预览版）

Azure Database for MySQL 灵活服务器可自动创建服务器备份，并安全地将它们存储在区域内的本地冗余存储中。

此示例 CLI 脚本执行[时间点还原](../concepts-backup-restore.md)，并从灵活服务器的备份创建新的服务器。 

新的灵活服务器通过原始服务器的配置创建而成，并且还从源服务器继承标签和设置，如虚拟网络和防火墙。 还原完成后，可以更改还原服务器的计算和存储层、配置和安全设置。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

使用变量的值编辑脚本中突出显示的行。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建托管数据库的灵活服务器。|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|从备份还原灵活服务器。|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|获取灵活服务器的详细信息。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|删除灵活服务器。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。