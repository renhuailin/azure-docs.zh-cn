---
title: Azure CLI 脚本 - 还原 Azure Database for PostgreSQL 服务器
description: 此示例 Azure CLI 脚本演示如何将 Azure Database for PostgreSQL 服务器及其数据库还原到前一个时间点。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 9af4a3d4a484ef22e147640add455741c533e9b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660468"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 还原 Azure Database for PostgreSQL 服务器
此示例 CLI 脚本可将单个 Azure Database for PostgreSQL 服务器还原到前一个时间点。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中使用的订阅 ID 替换为自己的订阅 ID。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | 从备份还原服务器。 |
| [az group delete](/cli/azure/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
- 请尝试其他脚本：[用于 PostgreSQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- [如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](../howto-restore-server-portal.md)
