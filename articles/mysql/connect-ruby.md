---
title: 快速入门：使用 Ruby 进行连接 - Azure Database for MySQL
description: 本快速入门提供多个 Ruby 代码示例，使用这些示例可连接到适用于 MySQL 的 Azure 数据库并查询其中的数据。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: ruby
ms.topic: quickstart
ms.custom: mvc
ms.date: 5/26/2020
ms.openlocfilehash: b250d7895e878cb1d422903517337a0eed160757
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643103"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入门：使用 Ruby 连接到 Azure Database for MySQL 并查询其中的数据

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本快速入门演示如何在 Windows、Ubuntu Linux 和 Mac 平台中使用 [Ruby](https://www.ruby-lang.org) 应用程序和 [mysql2](https://rubygems.org/gems/mysql2) gem 连接到适用于 MySQL 的 Azure 数据库。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本主题假设你熟悉如何使用 Ruby 进行开发，但不太熟悉 Azure Database for MySQL 的用法。

## <a name="prerequisites"></a>先决条件

此快速入门使用以下任意指南中创建的资源作为起点：

- [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> 确保已使用 [Azure 门户](./howto-manage-firewall-using-portal.md)或 [Azure CLI](./howto-manage-firewall-using-cli.md) 将服务器的防火墙规则添加到连接的 IP 地址

## <a name="install-ruby"></a>安装 Ruby

在自己的计算机上安装 Ruby、Gem 和 MySQL2 库。

### <a name="windows"></a>Windows

1. 下载并安装 [Ruby](https://rubyinstaller.org/downloads/) 版本 2.3。
2. 从“开始”菜单启动新的命令提示符 (cmd)。
3. 将目录切换到 Ruby 版本 2.3 所在的目录。 `cd c:\Ruby23-x64\bin`
4. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
5. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
6. 运行命令 `gem install mysql2`，使用 Gem 生成适用于 Ruby 的 Mysql2 模块。

### <a name="macos"></a>macOS

1. 运行命令 `brew install ruby`，使用 Homebrew 安装 Ruby。 如需更多安装选项，请参阅 Ruby [安装文档](https://www.ruby-lang.org/en/documentation/installation/#homebrew)。
2. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
3. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
4. 运行命令 `gem install mysql2`，使用 Gem 生成适用于 Ruby 的 Mysql2 模块。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 通过运行命令 `sudo apt-get install ruby-full` 安装 Ruby。 如需更多安装选项，请参阅 Ruby [安装文档](https://www.ruby-lang.org/en/documentation/installation/)。
2. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
3. 通过运行命令 `sudo gem update --system` 安装 Gem 的最新更新。
4. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
5. 通过运行命令 `sudo apt-get install build-essential` 安装 gcc、make 和其他生成工具。
6. 运行 `sudo apt-get install libmysqlclient-dev` 命令安装 MySQL 客户端开发人员库。
7. 运行命令 `sudo gem install mysql2`，使用 Gem 生成适用于 Ruby 的 mysql2 模块。

## <a name="get-connection-information"></a>获取连接信息

获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，单击“所有资源”，然后搜索已创建的服务器（例如 mydemoserver）。
3. 单击服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。   如果忘记了密码，也可通过此面板来重置密码。
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="Azure Database for MySQL 服务器名称":::

## <a name="run-ruby-code"></a>运行 Ruby 代码

1. 将以下部分中的 Ruby 代码粘贴到文本文件，然后使用文件扩展名 .rb 将这些文件保存到项目文件夹中（例如 `C:\rubymysql\createtable.rb` 或 `/home/username/rubymysql/createtable.rb`）。
2. 若要运行此代码，请启动命令提示符或 Bash shell。 将目录切换到项目文件夹 `cd rubymysql`
3. 然后键入 Ruby 命令并后接文件名（例如 `ruby createtable.rb`）以运行应用程序。
4. 在 Windows OS 上，如果 Ruby 应用程序不在路径环境变量中，则可能需要使用完整路径来启动 Node 应用程序，例如 `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>进行连接并创建表

使用以下代码进行连接，使用 CREATE TABLE SQL 语句创建表，然后使用 INSERT INTO SQL 语句将行添加到表中。

该代码使用 mysql2::client 类连接到 MySQL 服务器。 然后调用 ```query()``` 方法，以便运行 DROP、CREATE TABLE 和 INSERT INTO 命令。 最后，调用 ```close()``` 以在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling

rescue Exception => e
    puts e.message

# Cleanup

ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>读取数据

使用以下代码进行连接，并使用 SELECT SQL 语句读取数据。

该代码使用 mysql2::client 类通过 ```new()``` 方法连接到 Azure Database for MySQL。 然后，该代码调用 ```query()``` 方法来运行 SELECT 命令。 然后，它会调用方法 ```close()```，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling

rescue Exception => e
    puts e.message

# Cleanup

ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>更新数据

使用以下代码进行连接，并使用 UPDATE SQL 语句更新数据。

该代码使用 [mysql2::client](https://rubygems.org/gems/mysql2-client-general_log) 类的 .new() 方法连接到适用于 MySQL 的 Azure 数据库。 然后，该代码调用 ```query()``` 方法来运行 UPDATE 命令。 然后，它会调用方法 ```close()```，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling

rescue Exception => e
    puts e.message

# Cleanup

ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="delete-data"></a>删除数据

使用以下代码进行连接，并使用 DELETE SQL 语句读取数据。

该代码使用 [mysql2::client](https://rubygems.org/gems/mysql2/) 类连接到 MySQL 服务器，运行 DELETE 命令，然后关闭与服务器的连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling


rescue Exception => e
    puts e.message

# Cleanup


ensure
    client.close if client
    puts 'Done.'
end
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
> [使用导出和导入功能迁移数据库](./concepts-migrate-import-export.md)

> [!div class="nextstepaction"]
> [详细了解 MySQL2 客户端](https://rubygems.org/gems/mysql2-client-general_log)