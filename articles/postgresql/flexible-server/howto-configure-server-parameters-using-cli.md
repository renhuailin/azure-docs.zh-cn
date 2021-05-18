---
title: 配置参数 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何使用 Azure CLI 在 Azure Database for PostgreSQL 灵活服务器中配置 Postgres 参数。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 315e4439ca66a3fbadac228c013797f516bc2940
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605758"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>使用 Azure CLI 自定义 Azure Database for PostgreSQL 灵活服务器的服务器参数

可以使用命令行接口 (Azure CLI) 列出、显示和更新 Azure PostgreSQL 服务器的配置参数。 引擎参数的一个子集会在服务器级别公开，并且可以进行修改。 

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：
- 按照[创建 Azure Database for PostgreSQL](quickstart-create-server-cli.md) 创建 Azure Database for PostgreSQL 服务器和数据库
- 将 [Azure CLI](/cli/azure/install-azure-cli) 命令行接口安装在计算机上或通过浏览器在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

## <a name="list-server-parameters-for-a-flexible-server"></a>列出灵活服务器的服务器参数

若要列出服务器中的所有可修改参数及其值，请运行 [az postgres flexible-server parameter list](/cli/azure/postgres/flexible-server/parameter) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器参数 。

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>显示服务器参数详细信息

若要显示服务器的某个特定参数的详细信息，请运行 [az postgres flexible-server parameter show](/cli/azure/postgres/flexible-server/parameter) 命令。

此示例显示了资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器参数 log\_min\_messages 的详细信息。  

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>修改服务器参数值

还可以修改某个服务器参数的值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新参数，请使用 [az postgres flexible-server parameter set](/cli/azure/postgres/flexible-server/parameter) 命令。 

更新资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器参数 log\_min\_message  。

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

若要重置参数的值，只需选择省略可选的 `--value` 参数，服务将应用默认值。 以上述示例为例，它类似于下面这样：

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

此命令会将 log\_min\_messages 参数重置为默认值 WARNING 。 有关服务器参数和允许值的详细信息，请参阅有关[设置参数](https://www.postgresql.org/docs/12/config-setting.html)的 PostgreSQL 文档。

## <a name="next-steps"></a>后续步骤

- 若要配置和访问服务器日志，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-logging.md)
