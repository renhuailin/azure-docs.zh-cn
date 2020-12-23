---
title: CLI 脚本 - 下载慢查询日志 - Azure Database for MySQL
description: 此示例 Azure CLI 脚本演示如何启用和下载 Azure Database for MySQL 服务器的服务器日志。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 28dbf51fa58dfdfc375bf8f44964a6b66db008e8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539152"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 启用和下载 Azure Database for MySQL 服务器的服务器慢查询日志
此示例 CLI 脚本可启用和下载单个 Azure Database for MySQL 服务器的慢查询日志。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中的 &lt;log_file_name&gt; 替换自己的服务器日志文件名。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | 列出服务器的配置值。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | 更新服务器的配置。 |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) | 列出服务器的日志文件。 |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) | 下载日志文件。 |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
- 请尝试其他脚本：[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
