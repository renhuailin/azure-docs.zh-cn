---
title: CLI 脚本 - 更改服务器参数 - Azure Database for MariaDB
description: 此示例 CLI 脚本列出了所有可用服务器配置，并更新了 Azure Database for MariaDB 的服务器配置。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: c7a46f98f74648ccae9f9f9f94c218d42056decb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664642"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>使用 Azure CLI 列出和更新 Azure Database for MariaDB 服务器的配置
此示例 CLI 脚本列出了 Azure Database for MariaDB 服务器的可用配置参数及其允许的值，并将 innodb_lock_wait_timeout  设置为默认值以外的值。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 创建用于托管数据库的 MariaDB 服务器。 |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | 列出 Azure Database for MariaDB 服务器的配置。 |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | 更新 Azure Database for MariaDB 服务器的配置。 |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | 显示 Azure Database for MariaDB 服务器的配置。 |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
- 请尝试其他脚本：[Azure Database for MariaDB 的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关服务器参数的详细信息，请参阅[如何在 Azure Database for MariaDB 中配置服务器参数](../howto-server-parameters.md)。
