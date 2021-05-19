---
title: Azure Database for MySQL 灵活服务器中使用 TLS/SSL 的加密连接
description: 有关如何在 Azure Database for MySQL 灵活服务器中使用 TLS/SSL 进行连接的说明和信息。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2e66b27f7f0c731e17bb9811e2376bbe09c1bc12
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950138"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>通过 TLS1.2/SSL 连接到 Azure Database for MySQL - 灵活服务器

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

Azure Database for MySQL 灵活服务器支持使用传输层安全性 (TLS)（以前称为安全套接字层 (SSL)）将客户端应用程序连接到 MySQL 服务。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现加密的网络连接，使你能够满足合规性要求。

Azure Database for MySQL 灵活服务器仅支持使用传输层安全性 (TLS 1.2) 的加密连接，所有使用 TLS 1.0 和 TLS 1.1 的传入连接都会被拒绝。 已为所有灵活服务器启用了强制实施 TLS 连接的功能，你不能通过禁用 TLS/SSL 来连接到灵活服务器。

## <a name="download-the-public-ssl-certificate"></a>下载公共 SSL 证书
若要用于应用程序，请下载[公共 SSL 证书](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)。

将证书文件保存到首选位置。 例如，本教程在本地环境或托管应用程序的客户端环境中使用 `c:\ssl` 或 `\var\www\html\bin`。 这将允许应用程序通过 SSL 安全连接到数据库。 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>使用 mysql 命令行客户端通过 TLS/SSL 进行连接

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

可以选择 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md)，以便从本地环境连接到服务器。 

以下示例演示如何使用 mysql 命令行界面连接到服务器。 使用 `--ssl-mode=REQUIRED` 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 `--ssl-ca` 参数。 将值替换为实际的服务器名称和密码。 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> 确认传递给 `--ssl-ca` 的值与你保存的证书的文件路径匹配。

### <a name="verify-the-tlsssl-connection"></a>验证 TLS/SSL 连接

执行 mysql status 命令，验证是否已使用 TLS/SSL 连接到 MySQL 服务器：

```dos
mysql> status
```
查看输出以确认连接是否已加密，如果已加密，输出应显示为：“SSL: 使用中的密码为 AES256-SHA。” 此密码套件显示了一个示例。你可能会看到不同的密码套件，具体取决于客户端。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保应用程序或框架支持 TLS 连接

某些使用 MySQL 作为其数据库服务的应用程序框架在安装期间默认不启用 TLS。 你的 MySQL 服务器强制实施 TLS 连接，但如果应用程序未针对 TLS 进行配置，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 TLS 连接。

## <a name="sample-code"></a>示例代码
在 Azure 门户中，你的服务器的“连接字符串”页面中预定义了连接字符串。该字符串中包含以通用语言使用 TLS/SSL 连接到数据库服务器所需的参数。 TLS/SSL 参数因连接器而异。 例如，“useSSL=true”、“sslmode=required”或“ssl_verify_cert=true”以及其他变体。

若要从应用程序通过 TLS/SSL 与灵活服务器建立加密连接，请参阅以下代码示例：

### <a name="wordpress"></a>WordPress
下载 [SSL 公共证书](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)，并在 wp-config.php 中的 ```// ** MySQL settings - You can get this info from your web host ** //``` 行后添加以下行。

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

## <a name="next-steps"></a>后续步骤
- [使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-workbench.md)
- [使用 PHP 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-php.md)
- [使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活服务器防火墙规则](./concepts-networking.md#public-access-allowed-ip-addresses)
