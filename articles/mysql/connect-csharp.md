---
title: 快速入门：使用 C# 进行连接 - Azure Database for MySQL
description: 本快速入门提供一个 C# (.NET) 代码示例，使用该示例可连接到适用于 MySQL 的 Azure 数据库并查询其中的数据。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 16127f59ce1f4a258bf76d97b072ec8cc5f6c6c5
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643108"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入门：使用 .NET (C#) 连接到 Azure Database for MySQL 并查询其中的数据

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本快速入门演示如何使用 C# 应用程序连接到 Azure Database for MySQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 

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

[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>创建一个 C# 项目
在命令提示符处运行以下命令：

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>获取连接信息
获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，单击“所有资源”，然后搜索已创建的服务器（例如 mydemoserver）。
3. 单击服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。   如果忘记了密码，也可通过此面板来重置密码。
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Azure Database for MySQL 服务器名称":::

## <a name="step-1-connect-and-insert-data"></a>步骤 1：连接并插入数据
通过以下代码来连接和加载数据，只需使用 `CREATE TABLE` 和 `INSERT INTO` SQL 语句即可。 代码使用 `MySqlConnection` 类的以下方法：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用于与 MySQL 建立连接。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用于设置 CommandText 属性
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用于运行数据库命令。 

将 `Server`、`Database`、`UserID`、`Password` 参数替换为你在创建服务器和数据库时指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>步骤 2：读取数据

使用以下代码进行连接，并使用 `SELECT` SQL 语句读取数据。 该代码将 `MySqlConnection` 类与以下方法配合使用：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用于与 MySQL 建立连接。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用于设置 CommandText 属性。
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync)，用于运行数据库命令。 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync)，用于转到结果中的记录。 然后，代码使用 GetInt32 和 GetString 分析记录中的值。


将 `Server`、`Database`、`UserID`、`Password` 参数替换为你在创建服务器和数据库时指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>步骤 3：更新数据
使用以下代码进行连接，并使用 `UPDATE` SQL 语句读取数据。 该代码将 `MySqlConnection` 类与以下方法配合使用：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用于与 MySQL 建立连接。 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用于设置 CommandText 属性
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用于运行数据库命令。 


将 `Server`、`Database`、`UserID`、`Password` 参数替换为你在创建服务器和数据库时指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>步骤 4：删除数据
使用以下代码进行连接，并使用 `DELETE` SQL 语句删除数据。 

该代码将 `MySqlConnection` 类与以下方法配合使用
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用于与 MySQL 建立连接。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用于设置 CommandText 属性。
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用于运行数据库命令。 


将 `Server`、`Database`、`UserID`、`Password` 参数替换为你在创建服务器和数据库时指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
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
