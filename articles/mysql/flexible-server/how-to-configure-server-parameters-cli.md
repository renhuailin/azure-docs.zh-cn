---
title: 配置服务器参数 - Azure CLI - Azure Database for MySQL 灵活服务器
description: 本文介绍如何使用 Azure CLI 命令行实用工具在 Azure Database for MySQL 灵活服务器中配置服务参数。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bd1be8c63e295aedead72fdd5c6ce407179dc660
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651692"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>使用 Azure CLI 在 Azure Database for MySQL 灵活服务器中配置服务器参数

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以使用 Azure CLI、Azure 命令行实用工具来列出、显示和更新 Azure Database for MySQL 灵活服务器的参数。 创建服务器时，将使用默认值和推荐值配置服务器参数。  

本文介绍如何使用 Azure CLI 列出、显示和更新服务器参数。

>[!Note]
> 可在服务器级别全局更新服务器参数，方法是使用 [Azure CLI](./how-to-configure-server-parameters-cli.md) 或 [Azure 门户](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MySQL 灵活服务器](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 命令行实用工具或在浏览器中使用 Azure Cloud Shell。

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>为 Azure Database for MySQL 灵活服务器列出服务器参数

若要列出服务器中的所有参数及其值，请运行 [az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器参数 。
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
有关每个列出参数的定义，请参阅[服务器系统变量](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)上的 MySQL 引用部分。

## <a name="show-server-parameter-details"></a>显示服务器参数详细信息

若要显示服务器的某个特定参数的详细信息，请运行 [az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter) 命令。

本示例显示了资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器参数 slow\_query\_log 的详细信息  。
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>修改服务器参数值

还可以修改某个服务器参数的值，这会更新 MySQL 服务器引擎的基础配置值。 若要更新服务器参数，请使用 [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter) 命令。 

更新资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器参数 slow\_query\_log  。
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
若要重置参数的值，省去可选的 `--value` 参数，服务将应用默认值。 对于上述示例，它将如下所示：
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
此代码会将 slow\_query\_log 重置为默认值 OFF 。 

## <a name="setting-non-modifiable-server-parameters"></a>设置不可修改的服务器参数

如果要更新的服务器参数不可修改，则可以选择性地使用 `init_connect` 在连接级别设置参数。 此项可为每个连接到服务器的客户端设置服务器参数。

更新资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的 init\_connect 服务器参数，以设置字符集之类的值  。
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` 可用于在会话级别更改无需 SUPER 权限的参数。 若要验证是否可以使用 `init_connect` 设置参数，请执行 `set session parameter_name=YOUR_DESIRED_VALUE;` 命令，如果出现“拒绝访问；需要 SUPER 权限”错误，则无法使用“init_connect”设置参数。

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果正在运行 MySQL Workbench 中的 `mysql.az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
>应重启服务器，确保正确填充时区表。<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

要查看可用的时区值，请运行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>设置全局级时区

可以使用 [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter) 命令来设置全局级时区。

以下命令将资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的 time\_zone 服务器参数更新为“US/Pacific”   。

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MySQL 命令行或 MySQL Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”时区。  

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)，请参阅 MySQL 文档。


## <a name="next-steps"></a>后续步骤

- 如何配置 [Azure 门户中的服务器参数](./how-to-configure-server-parameters-portal.md)
