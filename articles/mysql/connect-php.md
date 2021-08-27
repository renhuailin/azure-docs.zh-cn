---
title: 快速入门：使用 PHP 进行连接 - Azure Database for MySQL
description: 本快速入门提供了多个 PHP 代码示例，你可以使用它来连接到 Azure Database for MySQL 并查询其中的数据。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: f10870062ec20195419f76d40dfc22008971b58c
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643105"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入门：使用 PHP 连接到 Azure Database for MySQL 并查询其中的数据

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
本快速入门演示了如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 应用程序连接到 Azure Database for MySQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。

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

- [创建数据库和非管理员用户](./howto-create-users.md?tabs=single-server)
- 安装操作系统的最新 PHP 版本
    - [macOS 上的 PHP](https://secure.php.net/manual/install.macosx.php)
    - [Linux 上的 PHP](https://secure.php.net/manual/install.unix.php)
    - [Windows 上的 PHP](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> 在本快速入门中，我们使用 [MySQLi](https://www.php.net/manual/en/book.mysqli.php) 库来管理连接和查询服务器。

## <a name="get-connection-information"></a>获取连接信息
可以通过执行以下步骤，从 Azure 门户获取数据库服务器连接信息：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 导航到“Azure Databases for MySQL”页。 搜索并选择“Azure Database for MySQL”。
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="查找 Azure Database for MySQL":::

2. 选择 MySQL 服务器（如 mydemoserver）。
3. 在“概述”页中，复制“服务器名称”旁边的完全限定的服务器名称，以及“服务器管理员登录名”旁边的管理员用户名  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!IMPORTANT]
> - 如果忘记了密码，可以[重置密码](./howto-create-manage-server-portal.md#update-admin-password)。
> - 将 host、username、password 和 db_name 参数替换为你自己的值** 

## <a name="step-1-connect-to-the-server"></a>步骤 1：连接到服务器
SSL 默认为启用状态。 从本地环境进行连接可能需要下载 [DigiCertGlobalRootG2 SSL 证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)。 此代码调用：
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php)，用于初始化 MySQLi。
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php)，用于指向 SSL 证书路径。 这是本地环境所必需的，但不是应用服务 Web 应用或 Azure 虚拟机所必需的。
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php)，用于连接到 MySQL。
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php)，用于关闭连接。


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno())
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[存在问题？请告诉我们](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>步骤 2：创建表
使用以下代码进行连接。 此代码调用：
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php)，用于运行查询。
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>步骤 3：插入数据
通过以下代码使用 INSERT SQL 语句插入数据。 此代码使用以下方法：
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)，用于创建准备好的 insert 语句
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)，用于为每个插入的列值绑定参数。
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)，用于关闭语句


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>步骤 4：读取数据
在以下代码中使用 SELECT SQL 语句读取数据。  此代码使用以下方法：
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php)，用于执行 SELECT 查询
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php)，用于提取生成的行。

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>步骤 5：删除数据
在以下代码中使用 DELETE SQL 语句删除行。 此代码使用以下方法：
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)用于创建准备好的 delete 语句
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)，用于绑定参数
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)用于执行准备好的 delete 语句
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)用于关闭语句

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
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
