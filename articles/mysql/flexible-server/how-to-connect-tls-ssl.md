---
title: Azure Database for MySQL 灵活服务器中使用 TLS/SSL 的加密连接
description: 有关如何在 Azure Database for MySQL 灵活服务器中使用 TLS/SSL 进行连接的说明和信息。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 46a451fd41f460165435305f2abf5d56fca4837d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778386"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>连接到具有加密连接 Azure Database for MySQL - 灵活服务器

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

Azure Database for MySQL 灵活服务器支持使用安全套接字层 (SSL) 和传输层安全性 (TLS) 加密将客户端应用程序连接到 MySQL 服务器。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现加密的网络连接，使你能够满足合规性要求。

默认情况下，Azure Database for MySQL 灵活服务器支持使用传输层安全性 (TLS 1.2) 的加密连接，并且会拒绝所有使用 TLS 1.0 和 TLS 1.1 的传入连接。 你的灵活服务器上的加密连接强制要求或 TLS 版本配置可以按照本文中的说明进行更改。

下面是适用于灵活服务器的 SSL 和 TLS 设置的不同配置：

| 方案   | 服务器参数设置      | 说明                                    |
|------------|--------------------------------|------------------------------------------------|
|禁用 SSL 强制要求 | require_secure_transport 设置为 OFF |如果旧版应用程序不支持与 MySQL 服务器建立加密连接，则可以通过将 require_secure_transport 设置为 OFF，来禁用与灵活服务器建立加密连接的强制要求。|
|强制使用 SSL 和低于 TLS 版本 1.2 的版本 | require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1 或 TLSV1.1| 如果旧版应用程序支持加密连接，但需要低于 TLS 版本 1.2 的版本，你可以启用加密连接，但将灵活服务器配置为允许连接使用应用程序支持的 TLS 版本（v1.0 或 v1.1）|
|强制使用 SSL 和 TLS 版本 1.2（默认配置）|require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1.2| 这是为灵活服务器建议的配置，同时也是默认配置。|
|强制使用 SSL 和 TLS 版本 1.3（受 MySQL v8.0 和更高版本支持）| require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1.3| 这是为新应用程序开发建议的有用配置|

> [!Note]
> 不支持对灵活服务器上的 SSL 密码进行更改。 将 tls_version 设置为 TLS 版本 1.2 时，默认强制使用 FIPS 密码套件。 对于版本 1.2 以外的其他 TLS 版本，SSL 密码将设为 MySQL 社区安装附带的默认设置。

在本文中，你将学习如何：

* 在以下条件下配置灵活服务器
  * 禁用 SSL
  * 强制使用 SSL 和低于 1.2 的 TLS 版本
* 在以下条件下使用 mysql 命令行连接到灵活服务器
  * 禁用加密连接
  * 启用加密连接
* 验证连接的加密状态
* 使用各种应用程序框架以加密连接方式连接到灵活服务器

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>在灵活服务器上禁用 SSL 强制要求

如果你的客户端应用程序不支持加密连接，你需要在灵活服务器上禁用加密连接强制要求。 要禁用加密连接强制要求，需要将 require_secure_transport 服务器参数设置为“OFF”（如屏幕截图所示），并保存服务器参数配置，以使其生效。 require_secure_transport 是一个动态服务器参数，它会立即生效，无需重启服务器。

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="屏幕截图显示如何禁用 Azure Database for MySQL 灵活服务器的 SSL。":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>在禁用 SSL 的情况下使用 mysql 命令行客户端进行连接

以下示例演示如何使用 mysql 命令行界面连接到服务器。 使用 `--ssl-mode=DISABLED` 连接字符串设置来禁用来自 mysql 客户端的 TLS/SSL 连接。 将值替换为实际的服务器名称和密码。

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```

请务必注意，将 require_secure_transport 设置为 OFF 并不意味着服务器端不支持加密连接。 如果在灵活服务器上将 require_secure_transport 设置为 OFF，则在客户端以加密连接方式连接时，仍然会被接受。 使用 mysql 客户端连接到配置了 require_secure_transport=OFF 的灵活服务器后，也可以正常工作，如下所示。

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```

```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

总之，require_secure_transport=OFF 设置放宽了在灵活服务器上强制执行加密连接的要求，除了加密连接之外，还允许从客户端到服务器的非加密连接。

## <a name="enforce-ssl-with-tls-version--12"></a>强制使用 SSL 和低于 TLS 版本 1.2 的版本

如果你的应用程序支持通过 SSL 连接到 MySQL 服务器，但支持低于 1.2 的 TLS 版本，你将需要在灵活服务器上设置 TLS 版本服务器参数。 若要设置需要灵活服务器支持的 TLS 版本，需要将 tls_version 服务器参数设置为 TLSV1、TLSV1.1 或 TLSV1 和 TLSV1.1（如屏幕截图中所示），并保存服务器参数配置，以使其生效。 tls_version 是静态服务器参数，需要重启服务器才能使参数生效。

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="屏幕截图显示如何为 Azure Database for MySQL 灵活服务器设置 tls 版本。":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>使用 mysql 命令行客户端通过 TLS/SSL 进行连接

### <a name="download-the-public-ssl-certificate"></a>下载公共 SSL 证书

若要对客户端应用程序使用加密连接，需要下载[公共 SSL 证书](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)，该证书也在 Azure 门户“网络”边栏选项卡中提供，如以下屏幕截图中所示。

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="屏幕截图显示如何从 Azure 门户下载公共 SSL 证书。":::

将证书文件保存到首选位置。 例如，本教程在本地环境或托管应用程序的客户端环境中使用 `c:\ssl` 或 `\var\www\html\bin`。 这使应用程序可以通过 SSL 安全连接到数据库。

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

可以选择 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md)，以便从本地环境连接到服务器。

以下示例演示如何使用 mysql 命令行界面连接到服务器。 使用 `--ssl-mode=REQUIRED` 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 `--ssl-ca` 参数。 将值替换为实际的服务器名称和密码。

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

> [!Note]
> 确认传递给 `--ssl-ca` 的值与你保存的证书的文件路径匹配。

如果你尝试以未加密的连接方式连接到你的服务器，将会出现错误，指示禁止使用不安全传输的连接，类似于以下内容：

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>验证 TLS/SSL 连接

执行 mysql status 命令，验证是否已使用 TLS/SSL 连接到 MySQL 服务器：

```dos
mysql> status
```

查看输出，确认连接是否已加密，如果已加密，输出应显示为：“SSL: 使用的密码为”。 此密码套件显示了一个示例。你可能会看到不同的密码套件，具体取决于客户端。

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>使用各种应用程序框架以加密连接方式连接到灵活服务器

在 Azure 门户中，你的服务器的“连接字符串”页面中预定义了连接字符串。该字符串中包含以通用语言使用 TLS/SSL 连接到数据库服务器所需的参数。 TLS/SSL 参数因连接器而异。 例如，“useSSL=true”、“sslmode=required”或“ssl_verify_cert=true”以及其他变体。

若要从应用程序通过 TLS/SSL 与灵活服务器建立加密连接，请参阅以下代码示例：

### <a name="wordpress"></a>WordPress

下载 [SSL 公共证书](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)，在 wp-config.php 中的 ```// **MySQL settings - You can get this info from your web host** //``` 行之后添加以下几行。

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP（使用 PDO）

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java（适用于 Java 的 MySQL 连接器）

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java（适用于 Java 的 MariaDB 连接器）

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

### <a name="nodejs"></a>Node.js

```node
var fs = require('fs');
var mysql = require('mysql');
const serverCa = [fs.readFileSync("/var/www/html/DigiCertGlobalRootCA.crt.pem", "utf8")];
var conn=mysql.createConnection({
    host:"mydemoserver.mysql.database.azure.com",
    user:"myadmin",
    password:"yourpassword",
    database:"quickstartdb",
    port:3306,
    ssl: {
        rejectUnauthorized: true,
        ca: serverCa
    }
});
conn.connect(function(err) {
  if (err) throw err;
});
```

## <a name="next-steps"></a>后续步骤

* [使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-workbench.md)
* [使用 PHP 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-php.md)
* [使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
* 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
* 详细了解 [Azure Database for MySQL 灵活服务器防火墙规则](./concepts-networking-public.md#public-access-allowed-ip-addresses)
