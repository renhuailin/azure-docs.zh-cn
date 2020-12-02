---
title: 管理服务器 Azure CLI-Azure Database for PostgreSQL-灵活的服务器
description: 了解如何从 Azure CLI 管理 Azure Database for PostgreSQL 灵活的服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493672"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for PostgreSQL 灵活的服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL-灵活的服务器处于预览阶段。

本文介绍如何管理在 Azure 中部署的灵活服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。 

需要在本地运行 Azure CLI 版本2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

使用 [az login](/cli/azure/reference-index#az-login) 命令登录到你的帐户。 

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择订阅。 记下 **az login** 输出中的 **id** 值，以用作以下命令中的 **订阅** 参数的值。 如果有多个订阅，请选择应为资源计费的订阅。 若要标识所有订阅，请使用 [az account list](/cli/azure/account#az-account-list) 命令。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果尚未创建灵活的服务器，需要执行此操作方法指南。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

可以使用以下命令轻松扩展计算层、Vcore 和存储。 有关可以运行的所有服务器操作的列表，请参阅 [az postgres 灵活-服务器](/cli/azure/postgres/flexible-server) 概述。

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

下面是上述代码中的参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 输入服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|Standard_D4ds_v3|输入计算层和大小的名称。 值遵循 *Standard_ {VM size}* "中的" 速记 "约定。 有关详细信息，请参阅[定价层](../concepts-pricing-tiers.md)。
storage-size | 6144 | 输入服务器的存储容量（以 mb 为单位）。 最小值为5120，增量为1024。

> [!IMPORTANT]
> 不能缩小存储。 

## <a name="manage-postgresql-databases-on-a-server"></a>管理服务器上的 PostgreSQL 数据库

可以通过多个应用程序连接到 Azure Database for PostgreSQL 服务器。 如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本地实例。 现在，我们使用 psql 命令行工具连接到 Azure Database for PostgreSQL 服务器。

1. 运行以下 **psql** 命令：

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，以下命令通过访问凭据连接到 PostgreSQL 服务器 **mydemoserver.postgres.database.azure.com** 上名为 " **postgres** " 的默认数据库。 出现提示时，请输入所 `<server_admin_password>` 选的。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   连接后，psql 工具将显示 **postgres** 提示符，你可以在其中输入 SQL 命令。 如果正在使用的 psql 版本与 Azure Database for PostgreSQL 服务器上的版本不同，则在初始连接输出中会出现警告。

   psql 输出示例：

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 如果未将防火墙配置为允许客户端的 IP 地址，则会出现以下错误：
   >
   > psql: 致命错误: 主机 `<IP address>`、用户 "myadmin"、数据库 "postgres" 没有 pg_hba.conf 条目，SSL 出现致命错误:需要 SSL 连接。 请指定 SSL 选项，然后重试。 "
   >
   > 确认允许在防火墙规则中使用客户端的 IP 地址。

2. 在提示符下键入以下命令，创建名为 **postgresdb** 的空数据库：

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 在提示符下，运行以下命令以切换到新创建的数据库 **postgresdb** 的连接：

    ```bash
    \c postgresdb
    ```

4. 键入  `\q` ，然后选择 Enter 以退出 psql。

在本部分中，你通过 psql 连接到 Azure Database for PostgreSQL 服务器并创建了一个空白用户数据库。

## <a name="reset-the-admin-password"></a>重置管理员密码

可以通过以下命令更改管理员角色的密码：

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 选择至少包含8个字符且最多包含128个字符的密码。 密码必须包含以下三个类别中的字符： 
> - 英文大写字母
> - 英文小写字母
> - 数字
> - 非字母数字字符

## <a name="delete-a-server"></a>删除服务器

若要删除 Azure Database for PostgreSQL 灵活的服务器，请运行 [az postgres 挠性-server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

- [了解备份和还原概念](concepts-backup-restore.md)
- [优化和监视服务器](concepts-monitoring.md)