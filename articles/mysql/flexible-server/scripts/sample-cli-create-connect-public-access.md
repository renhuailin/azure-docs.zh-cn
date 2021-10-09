---
title: CLI 脚本 - 创建 Azure Database for MySQL 灵活服务器（预览版）并启用公共访问连接
description: 此 Azure CLI 示例脚本演示了如何创建 Azure Database for MySQL 灵活服务器、配置服务器级防火墙规则（公共访问连接方法）并连接到该服务器。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: cc9710576eef20505a9ae27b74ce93897e7889b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701336"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-preview-and-enable-public-access-connectivity-using-azure-cli"></a>使用 Azure CLI 创建 Azure Database for MySQL 灵活服务器（预览版）并启用公共访问连接

此示例 CLI 脚本创建 Azure Database for MySQL 灵活服务器、配置服务器级防火墙规则（[公共访问连接方法](../concepts-networking-public.md)）并在服务器创建后连接到该服务器。 

脚本成功运行后，所有 Azure 服务和配置的 IP 地址均可访问 MySQL 灵活服务器，并且你将以交互模式连接到该服务器。

> [!NOTE] 
> 创建服务器后，无法更改连接方法。 例如，如果使用“公共访问(允许的 IP 地址)”创建服务器，则在创建后，不能更改为“专用访问(VNet 集成)” 。 若要详细了解连接方法，请参阅[网络概念](../concepts-networking.md)。


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

使用变量的值编辑脚本中突出显示的行。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/create-connect-burstable-server-public-access.sh?highlight=8,11-12 "Create Flexible Server and enable public access.")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建托管数据库的灵活服务器。|
|[az mysql flexible-server firewall-rule create](/cli/azure/mysql/flexible-server/firewall-rule#az_mysql_flexible_server_firewall_rule_create)|创建一个防火墙规则，以允许从输入的 IP 地址范围访问灵活服务器及其数据库。|
|[az mysql flexible-server connect](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_connect)|连接到灵活服务器以执行服务器或数据库操作。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|删除灵活服务器。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。