---
title: 如何将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序
description: 了解如何将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 45ded9214f310f3505cc5d032754333c5cf702d8
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862540"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序

本文适用于：✔️ Java

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文介绍如何将应用程序绑定到 Azure Database for MySQL 实例。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Database for MySQL 帐户
* Azure CLI

如果没有已部署的 Azure Spring Cloud 实例，请按照[快速入门：使用 Azure 门户启动 Azure Spring Cloud 应用](./quickstart.md)中的说明部署第一个 Spring Cloud 应用。

## <a name="prepare-your-java-project"></a>准备 Java 项目

1. 在项目的 pom.xml 文件中，添加以下依赖项：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```

1. 在 application.properties 文件中，删除所有 `spring.datasource.*` 属性。

1. 通过运行 `az spring-cloud app deploy` 更新当前应用，或者通过运行 `az spring-cloud app deployment create` 针对此更改创建新的部署。

## <a name="bind-your-app-to-the-azure-database-for-mysql-instance"></a>将应用绑定到 Azure Database for MySQL 实例

#### <a name="service-binding"></a>[服务绑定](#tab/Service-Binding)
1. 记下 Azure Database for MySQL 帐户的管理员用户名和密码。

1. 连接到服务器，从 MySQL 客户端创建名为“testdb”的数据库，然后创建新的非管理员帐户。

1. 在 Azure 门户的“Azure Spring Cloud”服务页上，查找“应用程序仪表板”，然后选择要绑定到 Azure Database for MySQL 实例的应用程序 。  这是在上一步更新或部署的应用程序。

1. 选择“服务绑定”，然后选择“创建服务绑定”按钮 。

1. 填充窗体，选择“Azure MySQL”作为“绑定类型”，使用以前使用的数据库名称以及在第一步记下的用户名和密码。

1. 重启应用，此绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称，然后验证其详细信息。 `property` 字段应如下所示：

    ```properties
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

以下 Terraform 脚本演示如何使用 Azure Database for MySQL 设置 Azure Spring Cloud 应用。

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

variable "administrator_login" {
  type        = string
  description = "The MySQL administrator login"
  default     = "myadmin"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "random_password" "password" {
  length           = 32
  special          = true
  override_special = "_%@"
}

resource "azurerm_mysql_server" "database" {
  name                = "mysql-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location

  administrator_login          = var.administrator_login
  administrator_login_password = random_password.password.result

  sku_name                          = "B_Gen5_1"
  storage_mb                        = 5120
  version                           = "5.7"
  auto_grow_enabled                 = true
  backup_retention_days             = 7
  geo_redundant_backup_enabled      = false
  infrastructure_encryption_enabled = false
  public_network_access_enabled     = true
  ssl_enforcement_enabled           = true
  ssl_minimal_tls_version_enforced  = "TLS1_2"
}

resource "azurerm_mysql_database" "database" {
  name                = "mysqldb-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "database" {
  name                = "mysqlfw-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "example-springcloudapp"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "spring.datasource.url" : "jdbc:mysql://${azurerm_mysql_server.database.fqdn}:3306/${azurerm_mysql_database.database.name}?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "spring.datasource.username" : "${var.administrator_login}@${azurerm_mysql_server.database.name}"
    "spring.datasource.password" : random_password.password.result
    "spring.jpa.properties.hibernate.dialect" : "org.hibernate.dialect.MySQL5InnoDBDialect"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>后续步骤

本文介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Database for MySQL 实例。 若要详细了解如何将服务绑定到应用程序，请参阅[将 Azure Cosmos DB 数据库绑定到 Azure Spring Cloud 应用程序](./how-to-bind-cosmos.md)。
