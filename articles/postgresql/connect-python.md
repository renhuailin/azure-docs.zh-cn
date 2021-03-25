---
title: 快速入门：使用 Python 进行连接 - Azure Database for PostgreSQL - 单一服务器
description: 本快速入门提供了可用于从 Azure Database for PostgreSQL - 单一服务器连接和查询数据的 Python 代码示例。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998934"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>快速入门：使用 Python 连接到 Azure Database for PostgreSQL 并查询其中的数据 - 单一服务器

在快速入门中，了解如何连接到 Azure Database for PostgreSQL 单一服务器上的数据库，以及如何运行 SQL 语句以在 macOS、Ubuntu Linux 或 Windows 上使用 Python 进行查询。

> [!TIP]
> 如果希望使用 PostgreSQL 生成 Django 应用程序，请查看教程 - [使用 PostgreSQL 部署 Django Web 应用](../app-service/tutorial-python-postgresql-app.md)教程。


## <a name="prerequisites"></a>先决条件
对于本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
- 使用 [Azure 门户](./quickstart-create-server-database-portal.md) <br/> 或 [Azure CLI](./quickstart-create-server-database-azure-cli.md) 创建 Azure Database for PostgreSQL 单一服务器（如果没有）。
- 请完成以下操作之一以启用连接，具体取决于你使用的是公共访问还是私有访问。

  |操作| 连接方法|操作指南|
  |:--------- |:--------- |:--------- |
  | **配置防火墙规则** | 公用 | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | 配置服务终结点 | 公用 | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | 配置专用链接 | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9+ 或 3.4+。

- 最新 [pip](https://pip.pypa.io/en/stable/installing/) 包安装程序。
- 在终端或命令提示符窗口中使用 `pip install psycopg2` 安装 [psycopg2](https://pypi.python.org/pypi/psycopg2/)。 有关详细信息，请参阅[如何安装 `psycopg2`](http://initd.org/psycopg/docs/install.html)。

## <a name="get-database-connection-information"></a>获取数据库连接信息
连接到 Azure Database for PostgreSQL 数据库需要完全限定的服务器名称和登录凭据。 可以从 Azure 门户获取此信息。

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索 Azure Database for PostgreSQL 服务器名称并选择该名称。
1. 在服务器的“概述”页上，复制完全限定的“服务器名称”和“管理员用户名”  。 完全限定的“服务器名称”始终为“\<my-server-name>.postgres.database.azure.com”的格式，“管理员用户名”始终为“\<my-admin-username>@\<my-server-name>”的格式。

   你还需要管理员密码。 如果忘记，可以从此页重置它。

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL 服务器名称":::

> [!IMPORTANT]
>  请替换以下值：
>   - `<server-name>` 和 `<admin-username>` 替换为从 Azure 门户复制的值。
>   - `<admin-password>` 替换为服务器密码。
>   - `<database-name>` 替换为你在创建服务器时自动创建一个名为 postgres 的默认数据库。 你可以重命名该数据库，或使用 SQL 命令[创建新的数据库](https://www.postgresql.org/docs/9.0/sql-createdatabase.html)。

## <a name="step-1-connect-and-insert-data"></a>步骤 1：连接并插入数据
下面的代码示例使用
-  [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 函数连接到 Azure Database for PostgreSQL 数据库，并使用 SQL INSERT 语句加载数据。
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数对数据库执行 SQL 查询。

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

如果代码成功运行，则会生成以下输出：

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="命令行输出":::


[存在问题？请告诉我们](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>步骤 2：读取数据
下面的代码示例连接到 Azure Database for PostgreSQL 数据库，并使用
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL SELECT 语句来读取数据。
- 通过使用以下语句，[cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) 接受查询并返回要循环访问的结果集

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[存在问题？请告诉我们](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>步骤 3：更新数据
下面的代码示例使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL UPDATE 语句来更新数据。

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[存在问题？请告诉我们](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>步骤 5：删除数据
下面的代码示例运行 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL DELETE 语句删除先前插入的清单项。

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[存在问题？请告诉我们](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中使用的所有资源，请使用以下命令删除该资源组：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用门户管理 Azure Database for MySQL 服务器](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [使用 CLI 管理 Azure Database for MySQL 服务器](./how-to-manage-server-cli.md)<br/>

[找不到要查找的内容？请告诉我们。](https://aka.ms/postgres-doc-feedback)
