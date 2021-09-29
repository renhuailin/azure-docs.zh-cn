---
title: 管理服务器 - Azure CLI - Azure Database for MySQL 灵活服务器
description: 了解如何通过 Azure CLI 管理 Azure Database for MySQL 灵活服务器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 0788ab52bbd70d63cf32d501fc54e10910509e15
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128587777"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for MySQL 灵活服务器（预览版）

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍如何管理 Azure 中部署的灵活服务器（预览版）。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](/cli/azure/reference-index#az_login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli
az account set --subscription <subscription id>
```

> [!IMPORTANT]
>如果尚未创建灵活服务器，请创建一个，以便开始使用此操作指南。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

可以使用以下命令轻松地纵向扩展计算层、vCore 和存储。 若要查看你可以执行的所有服务器操作，可参阅 [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

下面是上述参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 输入 Azure Database for MySQL 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|Standard_D4ds_v4|输入计算层的名称和大小。 遵循简写约定 Standard_{VM size}。 有关详细信息，请参阅[定价层](../concepts-pricing-tiers.md)。
storage-size | 6144 | 服务器的存储容量（以 MB 为单位）。 最小值为 5120，以 1024 为增量递增。

> [!IMPORTANT]
>- 存储可以纵向扩展（但不能纵向缩减）


## <a name="manage-mysql-databases-on-a-server"></a>管理服务器上的 MySQL 数据库。
可以使用以下任何命令来创建、删除、列出和查看服务器上数据库的数据库属性

| Cmdlet | 使用情况| 说明 |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |创建数据库|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|从服务器中删除数据库。 此命令不会删除服务器。 |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|列出服务器上的所有数据库|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|显示数据库的更多详细信息|

## <a name="update-admin-password"></a>更新管理员密码
可以使用此命令更改管理员角色的密码
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 请确保密码至少有 8 个字符，至多有 128 个字符。
> 密码必须包含以下类别中的三个类别的字符：英文大写字母、英文小写字母、数字和非字母数字字符。

## <a name="delete-a-server"></a>删除服务器
如果只想删除 MySQL 灵活服务器，可运行 [az mysql flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤
- [了解如何启动或停止服务器](how-to-stop-start-server-portal.md)
- [了解如何管理虚拟网络](how-to-manage-virtual-network-cli.md)
- [排查连接问题](how-to-troubleshoot-common-connection-issues.md)
- [创建和管理防火墙](how-to-manage-firewall-cli.md)