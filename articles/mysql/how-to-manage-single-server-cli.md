---
title: 管理服务器 - Azure CLI - Azure Database for MySQL
description: 了解如何通过 Azure CLI 管理 Azure Database for MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542059"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for MySQL 单一服务器

本文介绍如何管理 Azure 中部署的单一服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件
如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。 本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](/cli/azure/reference-index#az-login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

如果尚未创建服务器，请参考此[快速入门](quickstart-create-mysql-server-database-using-azure-cli.md)创建一个。

## <a name="scale-compute-and-storage"></a>缩放计算和存储
可以使用以下命令轻松地纵向扩展定价层、计算和存储。 可以参阅 [az mysql server 概述](/cli/azure/mysql/server)，了解可执行的所有服务器操作

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

下面是上述参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 输入 Azure Database for MySQL 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|GP_Gen5_2|输入定价层和计算配置的名称。 请遵循简写约定 {pricing tier} _{compute generation}_ {vCores}。 有关详细信息，请参阅[定价层](./concepts-pricing-tiers.md)。
storage-size | 6144 | 服务器的存储容量（以 MB 为单位）。 最小值为 5120，以 1024 为增量递增。

> [!Important]
> - 存储可以纵向扩展（但不能纵向缩减）
> - 不支持从“基本”定价层纵向扩展到“常规用途”或“内存优化”定价层。 可以[使用 bash 脚本](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)或[使用 MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134) 进行手动纵向扩展


## <a name="manage-mysql-databases-on-a-server"></a>管理服务器上的 MySQL 数据库
可以使用以下任何命令来创建、删除、列出和查看服务器上数据库的数据库属性

| Cmdlet | 使用情况| 说明 |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |创建数据库|
|[az mysql db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|从服务器中删除数据库。 此命令不会删除服务器。 |
|[az mysql db list](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|列出服务器上的所有数据库|
|[az mysql db show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|显示数据库的更多详细信息|

## <a name="update-admin-password"></a>更新管理员密码
可以使用此命令更改管理员角色的密码
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  请确保密码至少有 8 个字符，至多有 128 个字符。
> 密码必须包含以下类别中的三个类别的字符：英文大写字母、英文小写字母、数字和非字母数字字符。

## <a name="delete-a-server"></a>删除服务器
如果只想删除 MySQL 单一服务器，可运行 [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) 命令。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤
- [重启服务器](howto-restart-server-cli.md)
- [还原处于错误状态的服务器](howto-restore-server-cli.md)
- [监视和优化服务器](concepts-monitoring.md)