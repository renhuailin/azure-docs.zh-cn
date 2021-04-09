---
title: 快速入门：使用 Azure CLI 进行连接 - Azure Database for PostgreSQL - 灵活服务器
description: 本快速入门提供多种方法来通过 Azure CLI 与 Azure Database for PostgreSQL - 灵活服务器进行连接。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f4eec89aadee1966271286b9280916af973e4b1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614337"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>快速入门：通过 Azure CLI 与 Azure Database for PostgreSQL - 灵活服务器进行连接和查询

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器当前以公共预览版提供。

本快速入门演示了如何通过 ```az postgres flexible-server connect``` 命令使用 Azure CLI 连接到 Azure Database for PostgreSQL 灵活服务器。 通过此命令可测试与数据库服务器的连接并运行查询。 还可以使用交互模式运行多个查询。 

## <a name="prerequisites"></a>先决条件
- 一个 Azure 帐户。 如果没有帐户，可[获取一个免费试用帐户](https://azure.microsoft.com/free/)。
- 安装 [Azure CLI](/cli/azure/install-azure-cli) 最新版本（2.20.0 或更高版本）
- 通过 ```az login``` 命令使用 Azure CLI 登录 
- 使用 ```az config param-persist on``` 启用参数持久性。 参数持久性将帮助你使用本地上下文，而无需重复大量参数，如资源组或位置。

## <a name="create-an-postgresql-flexible-server"></a>创建 PostgreSQL 灵活服务器

首先，我们将创建一个托管 PostgreSQL 服务器。 在 [Azure Cloud Shell](https://shell.azure.com/) 中，运行以下脚本，并记下该命令生成的“服务器名称”、“用户名”和“密码”  。

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
可以为此命令提供其他参数以对其进行自定义。 查看 [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) 的所有参数。

## <a name="view-all-the-arguments"></a>查看所有参数
可以通过 ```--help``` 参数查看此命令的所有参数。 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>测试数据库服务器连接
可以使用命令在开发环境中测试和验证与数据库的连接。

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**示例：** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
如果连接成功，则会显示输出。
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

如果连接失败，请尝试以下解决方案：
- 检查是否已在客户端计算机上打开端口 5432。
- 如果服务器管理员用户名和密码正确
- 如果已为客户端计算机配置防火墙规则
- 如果已在虚拟网络中为服务器配置了专用访问权限，请确保客户端计算机位于同一虚拟网络中。

## <a name="run-single-query"></a>运行单个查询
可以通过 ```--querytext``` 参数 ```-q``` 使用命令执行单个查询。

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**示例：** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

应会看到如下所示的输出：

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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

## <a name="run-multiple-queries-using-interactive-mode"></a>使用交互模式运行多个查询
可以使用“交互”模式运行多个查询。 若要启用交互模式，请运行以下命令

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**示例：**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

你将看到 psql shell 体验，如下所示：

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理服务器](./how-to-manage-server-cli.md)
