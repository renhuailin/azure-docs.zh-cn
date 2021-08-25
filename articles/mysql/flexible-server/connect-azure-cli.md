---
title: 快速入门：使用 Azure CLI 进行连接 - Azure Database for MySQL - 灵活服务器
description: 本快速入门提供多种方法来通过 Azure CLI 与 Azure Database for MySQL - 灵活服务器进行连接。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: f75b933769e94986b1c140fb8bcbadca44b96cf1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643093"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>快速入门：通过 Azure CLI 与 Azure Database for MySQL - 灵活服务器进行连接和查询

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本快速入门演示了如何在 Azure CLI 中使用 ```az mysql flexible-server connect``` 连接到 Azure Database for MySQL 灵活服务器并使用 ```az mysql flexible-server execute``` 命令执行单个查询或 sql 文件。 通过此命令可测试与数据库服务器的连接并运行查询。 还可以使用交互模式运行多个查询。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，可[获取一个免费试用帐户](https://azure.microsoft.com/free/)。
- 安装 [Azure CLI](/cli/azure/install-azure-cli) 最新版本（2.20.0 或更高版本）
- 通过 ```az login``` 命令使用 Azure CLI 登录 
- 使用 ```az config param-persist on``` 启用参数持久性。 参数持久性将帮助你使用本地上下文，而无需重复大量参数，如资源组或位置等。

## <a name="create-an-mysql-flexible-server"></a>创建 MySQL 灵活服务器

首先，我们将创建一个托管 MySQL 服务器。 在 [Azure Cloud Shell](https://shell.azure.com/) 中，运行以下脚本，并记下该命令生成的“服务器名称”、“用户名”和“密码”  。

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

可以为此命令提供其他参数以对其进行自定义。 查看 [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create) 的所有参数。

## <a name="create-a-database"></a>创建数据库
如果尚未创建数据库，请运行 **newdatabase** 命令进行创建。

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>查看所有参数
可以通过 ```--help``` 参数查看此命令的所有参数。 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>测试数据库服务器连接
运行以下脚本，在开发环境中测试和验证与数据库的连接。

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**示例：**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

对于成功的连接，应看到以下输出：

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
如果连接失败，请尝试以下解决方案：
- 检查是否已在客户端计算机上打开端口 3306。
- 如果服务器管理员用户名和密码正确
- 如果已为客户端计算机配置防火墙规则
- 如果已在虚拟网络中为服务器配置了专用访问权限，请确保客户端计算机位于同一虚拟网络中。

## <a name="run-multiple-queries-using-interactive-mode"></a>使用交互模式运行多个查询
可以使用“交互”模式运行多个查询。 若要启用交互模式，请运行以下命令

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**示例：**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

你将看到 MySQL shell 体验，如下所示：

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

## <a name="run-single-query"></a>运行单个查询
运行以下命令，以使用 ```--querytext``` 参数 ```-q``` 执行单个查询。

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**示例：**
```azurecli
az mysql flexible-server execute -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

应会看到如下所示的输出：

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-sql-file"></a>运行 SQL 文件
可以通过 ```--file-path``` 参数 ```-q``` 使用命令执行 SQL 文件。

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --file-path "<file-path>"
```

**示例：** 
```azurecli
az mysql flexible-server execute -n mysqldemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -f "./test.sql"
```

应会看到如下所示的输出：

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Running sql file '.\test.sql'...
Successfully executed the file.
Closed the connection to mysqldemoserver.
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
* [连接到具有加密连接 Azure Database for MySQL - 灵活服务器](how-to-connect-tls-ssl.md)
* [管理服务器](./how-to-manage-server-cli.md)

