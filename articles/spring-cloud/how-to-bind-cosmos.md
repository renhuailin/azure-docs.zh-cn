---
title: 将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序
description: 了解如何将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 387d526002411395e8bebc0fa59925bfa383e598
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861331"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>将 Azure Cosmos DB 数据库绑定到 Azure Spring Cloud 应用程序

本文适用于：✔️ Java

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Cosmos DB 数据库。

先决条件：

* 部署的 Azure Spring Cloud 实例。 按[有关如何通过 Azure CLI 进行部署的快速入门](./quickstart.md)操作即可入门。
* 最低权限级别为“参与者”的 Azure Cosmos DB 帐户。

## <a name="prepare-your-java-project"></a>准备 Java 项目

1. 在 Azure Spring Cloud 应用程序的 pom.xml 文件中添加以下依赖项之一。 选择适合 API 类型的依赖项。

    * API 类型：Core (SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * API 类型：MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API 类型：Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API 类型：Azure 表

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. 通过运行 `az spring-cloud app deploy` 更新当前应用，或者通过运行 `az spring-cloud app deployment create` 针对此更改创建新的部署。

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>将应用绑定到 Azure Cosmos DB

#### <a name="service-binding"></a>[服务绑定](#tab/Service-Binding)
Azure Cosmos DB 具有五种支持绑定的不同 API 类型。 以下过程演示如何使用它们：

1. 创建 Azure Cosmos DB 数据库。 如需帮助，请参阅有关如何[创建数据库](../cosmos-db/create-cosmosdb-resources-portal.md)的快速入门。

1. 记录数据库名称。 对于此过程，数据库名称是 **testdb**。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 转到“应用程序仪表板”，选择要绑定到 Azure Cosmos DB 的应用程序。 此应用程序是在上一步更新或部署的应用程序。

1. 选择“服务绑定”，然后选择“创建服务绑定” 。 若要填写表单，请选择以下项：
   * **绑定类型** 值 **Azure Cosmos DB**。
   * API 类型。
   * 数据库名称。
   * Azure Cosmos DB 帐户。

    > [!NOTE]
    > 如果使用 Cassandra，请使用数据库名称的密钥空间。

1. 选择应用程序页上的“重启”来重启应用程序。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应类似于以下示例：

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
以下 Terraform 脚本演示如何使用 Azure Cosmos DB MongoDB API 设置 Azure Spring Cloud 应用。

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
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
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>后续步骤

本问介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Cosmos DB 数据库。 若要详细了解如何将服务绑定到应用程序，请参阅[绑定到 Azure Cache for Redis 缓存](./how-to-bind-redis.md)。
