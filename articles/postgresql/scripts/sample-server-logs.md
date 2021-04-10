---
title: Azure CLI 脚本 - 下载 Azure Database for PostgreSQL 中的服务器日志
description: 此示例 Azure CLI 脚本演示如何启用和下载 Azure Database for PostgreSQL 的服务器日志。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 3011182794df1c6d60fe286e48c3173dfcfcbe24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605240"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 启用和下载 Azure Database for PostgreSQL 服务器的服务器慢查询日志
此示例 CLI 脚本可启用和下载单个 Azure Database for PostgreSQL 服务器的慢查询日志。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中的 &lt;log_file_name&gt; 替换自己的服务器日志文件名。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/azure/postgres/server) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | 列出服务器的配置值。 |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | 更新服务器的配置。 |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | 列出服务器的日志文件。 |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | 下载日志文件。 |
| [az group delete](/cli/azure/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
- 尝试其他脚本：[Azure Database for PostgreSQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- [在 Azure 门户中配置和访问服务器日志](../howto-configure-server-logs-in-portal.md)
