---
title: 快速入门：使用 Python 进行连接 - Azure Database for MySQL
description: 本快速入门提供了多个 Python 代码示例，你可以使用它来连接到 Azure Database for MySQL 并查询其中的数据。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: f1e54096e2008997bd86cc9cc1bda5363fa41744
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643104"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入门：使用 Python 连接到 Azure Database for MySQL 并查询其中的数据

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

在本快速入门中，你将使用 Python 连接到 Azure Database for MySQL。 然后使用 SQL 语句在 Mac、Ubuntu Linux 和 Windows 平台的数据库中查询、插入、更新和删除数据。 

## <a name="prerequisites"></a>先决条件
对于本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
- 使用 [Azure 门户](./quickstart-create-mysql-server-database-using-azure-portal.md)创建 Azure Database for MySQL 单一服务器 <br/> 或 [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) 创建 Azure Database for PostgreSQL 单一服务器（如果没有）。
- 请完成以下操作之一以启用连接，具体取决于你使用的是公共访问还是私有访问。

   |操作| 连接方法|操作指南|
   |:--------- |:--------- |:--------- |
   | **配置防火墙规则** | 公用 | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | 配置服务终结点 | 公用 | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | 配置专用链接 | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [创建数据库和非管理员用户](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>安装 Python 和 MySQL 连接器

使用以下步骤在计算机上安装 Python 和用于 Python 的 MySQL 连接器： 

> [!NOTE]
> 本快速入门使用 [MySQL 连接器/Python 开发人员指南](https://dev.mysql.com/doc/connector-python/en/)。

1. 下载并安装适合自己 OS 的 [Python 3.7 或更高版本](https://www.python.org/downloads/)。 请确保将 Python 添加到 `PATH`，因为 MySQL 连接器需要它。
   
2. 打开命令提示符或 `bash` shell，使用大写 V 开关运行 `python -V`，以便检查 Python 版本。
   
3. 最新版本的 Python 中包含 `pip` 包安装程序。 通过运行 `pip install -U pip` 将 `pip` 更新为最新版本。 
   
   如果未安装 `pip`，则可使用 `get-pip.py` 下载并安装它。 有关详细信息，请参阅[安装](https://pip.pypa.io/en/stable/installing/)。 
   
4. 使用 `pip`，安装用于 Python 的 MySQL 连接器及其依赖项：
   
   ```bash
   pip install mysql-connector-python
   ```
   
[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>获取连接信息

从 Azure 门户获取连接到 Azure Database for MySQL 所需的连接信息。 需要服务器名称、数据库名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
   
1. 在门户搜索栏中，搜索并选择创建的 Azure Database for MySQL 服务器，如 **mydemoserver**。
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL 服务器名称":::
   
1. 从服务器的“概览”页中记下“服务器名称”和“服务器管理员登录名”。   如果忘记了密码，也可通过此页重置密码。
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL 服务器名称 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>步骤 1：创建表并插入数据

通过以下代码连接到服务器和数据库，创建一个表，然后使用 INSERT SQL 语句加载数据。此代码导入 mysql.connector 库，并使用以下方法：
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 函数，用于通过配置集合中的[参数](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)连接到 Azure Database for MySQL。 
- [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法，用于对 MySQL 数据库执行 SQL 查询。 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html)，在用完游标后使用。
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html)，用于关闭连接。

> [!IMPORTANT]
> - SSL 默认为启用状态。 从本地环境进行连接可能需要下载 [DigiCertGlobalRootG2 SSL 证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)。
> - 将 `<mydemoserver>`、`<myadmin>`、`<mypassword>` 和 `<mydatabase>` 占位符替换为 MySQL 服务器和数据库的值。

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>步骤 2：读取数据

使用以下代码进行连接，并使用 SELECT SQL 语句读取数据。 该代码导入 mysql.connector 库，并使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法对 MySQL 数据库执行 SQL 查询。 

代码使用 [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) 方法读取数据行，将结果集保留在集合行中，并使用 `for` 迭代器对行进行循环操作。

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>步骤 3：更新数据

使用以下代码进行连接，并使用 UPDATE SQL 语句更新数据。 该代码导入 mysql.connector 库，并使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法对 MySQL 数据库执行 SQL 查询。 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>步骤 4：删除数据

使用以下代码进行连接，并使用 **DELETE** SQL 语句删除数据。 该代码导入 mysql.connector 库，并使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法对 MySQL 数据库执行 SQL 查询。 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [使用 CLI 管理 Azure Database for MySQL 服务器](./how-to-manage-single-server-cli.md)

[找不到要查找的内容？请告诉我们。](https://aka.ms/mysql-doc-feedback)
