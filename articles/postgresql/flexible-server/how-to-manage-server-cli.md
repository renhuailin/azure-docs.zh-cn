---
title: 管理服务器 - Azure CLI - Azure Database for PostgreSQL 灵活服务器
description: 了解如何通过 Azure CLI 管理 Azure Database for PostgreSQL 灵活服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778492"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for PostgreSQL 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器现提供预览版。

本文介绍如何管理 Azure 中部署的灵活服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。 

需要在本地运行 Azure CLI 版本 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

使用 [az login](/cli/azure/reference-index#az_login) 命令登录到你的帐户。 

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择订阅。 记下 az login 输出中的 id 值，以用作以下命令中 subscription 参数的值  。 如果有多个订阅，请选择应计费的资源所在的订阅。 若要确定所有订阅，请使用 [az account list](/cli/azure/account#az_account_list) 命令。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果尚未创建灵活服务器，将需要创建该服务器才能按照此操作指南进行操作。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

可以使用以下命令轻松地纵向扩展计算层、vCore 和存储。 有关可运行的所有服务器操作的列表，请参阅 [az postgres 灵活服务器](/cli/azure/postgres/flexible-server)概述。

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

下面是上述代码中的参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 为服务器输入唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|Standard_D4ds_v3|输入计算层的名称和大小。 此值遵循以速记形式列出的 Standard_{VM size} 约定。 有关详细信息，请参阅[定价层](../concepts-pricing-tiers.md)。
storage-size | 6144 | 输入服务器的存储容量（以 MB 为单位）。 最小值为 5120，以 1024 为增量递增。

> [!IMPORTANT]
> 无法纵向缩减存储。 

## <a name="manage-postgresql-databases-on-a-server"></a>管理服务器上的 PostgreSQL 数据库

可以通过多个应用程序连接到 Azure Database for PostgreSQL 服务器。 如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本地实例。 现在，使用 psql 命令行工具连接到 Azure Database for PostgreSQL 服务器。

1. 运行以下 psql 命令：

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，以下命令通过访问凭据连接到 PostgreSQL 服务器 mydemoserver.postgres.database.azure.com 上名为“postgres”的默认数据库。 出现提示时，输入所选的 `<server_admin_password>`。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   连接后，psql 工具会显示 postgres 提示符，你可以在其中输入 SQL 命令。 如果所使用的 psql 版本不同于 Azure Database for PostgreSQL 服务器版本，初始连接输出中便会出现警告。

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
   > psql: 致命错误: 主机 `<IP address>`、用户 "myadmin"、数据库 "postgres" 没有 pg_hba.conf 条目，SSL 出现致命错误:需要 SSL 连接。 请指定 SSL 选项，然后重试。”
   >
   > 确认在防火墙规则中允许你的客户端 IP 地址。

2. 在提示符下，通过键入以下命令来创建名为“postgresdb”的空数据库：

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 在提示符下，运行以下命令来将连接切换到新建的数据库 postgresdb：

    ```bash
    \c postgresdb
    ```

4. 键入 `\q` 并选择 Enter 以退出 psql。

在本部分中，你已通过 psql 连接到 Azure Database for PostgreSQL 服务器并创建了一个空用户数据库。

## <a name="reset-the-admin-password"></a>重置管理员密码

可以使用以下命令更改管理员角色的密码：

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 选择一个至少含有 8 个字符而最多含有 128 个字符的密码。 密码必须包含以下类别中的三个类别的字符： 
> - 英文大写字母
> - 英文小写字母
> - 数字
> - 非字母数字字符

## <a name="delete-a-server"></a>删除服务器

若要删除 Azure Database for PostgreSQL 灵活服务器，请运行 [az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete) 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

- [了解备份和还原概念](concepts-backup-restore.md)
- [优化和监视服务器](concepts-monitoring.md)