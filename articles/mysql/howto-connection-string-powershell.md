---
title: 使用 PowerShell 生成连接字符串 - Azure Database for MySQL
description: 本文提供一个 Azure PowerShell 示例，用于生成连接到 Azure Database for MySQL 所用的连接字符串。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 83a33f1f2f22af6aafbe207c56cdc022a4b9510a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652813"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>如何使用 PowerShell 生成 Azure Database for MySQL 连接字符串

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文演示如何为 Azure Database for MySQL 服务器生成连接字符串。 可以使用连接字符串从许多不同的应用程序连接到 Azure Database for MySQL。

## <a name="requirements"></a>要求

本文使用以下指南中创建的资源作为起点：

* [快速入门：使用 PowerShell 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>获取连接字符串

`Get-AzMySqlConnectionString` cmdlet 用于生成连接字符串，以便将应用程序连接到 Azure Database for MySQL。 下面的示例从 mydemoserver 返回 PHP 客户端的连接字符串。

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

`Client` 参数的有效值包括：

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 自定义 Azure Database for MySQL 服务器参数](howto-configure-server-parameters-using-powershell.md)
