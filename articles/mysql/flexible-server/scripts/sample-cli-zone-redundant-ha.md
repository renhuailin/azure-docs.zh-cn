---
title: CLI 脚本 - 在 Azure Database for MySQL 灵活服务器（预览版）中配置区域冗余高可用性
description: 此 Azure CLI 示例脚本演示如何在 Azure Database for MySQL 灵活服务器中配置区域冗余高可用性。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 879cfa3b8c20c466c1aefcc0539109cbdc06cd32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701197"
---
# <a name="configure-zone-redundant-high-availability-in-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>在 Azure Database for MySQL 灵活服务器（预览版）中使用 Azure CLI 配置区域冗余高可用性

此示例 CLI 脚本在 Azure Database for MySQL 灵活服务器中配置和管理[区域冗余高可用性](../concepts-high-availability.md)。 只能在创建灵活服务器期间启用区域冗余高可用性，但可以随时禁用。 还可以选择主要副本和备用副本的可用性区域。 

目前，仅“常规用途”和“内存优化”定价层支持区域冗余高可用性。


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

编辑脚本中突出显示的行，将变量替换为你的值。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/zone-redundant-ha.sh?highlight=7,10-11,13-14 "Configure Zone-Redundant High Availability.")]


## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建用于托管数据库的灵活服务器。|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|更新灵活服务器。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|删除灵活服务器。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。