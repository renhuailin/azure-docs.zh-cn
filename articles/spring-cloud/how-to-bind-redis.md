---
title: 将 Azure Cache for Redis 绑定到 Azure Spring Cloud 应用程序
description: 了解如何将 Azure Redis 缓存绑定到 Azure Spring Cloud 应用程序
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 31cb033bad3a6b356b447754670fd88bf0ee4663
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861334"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>将 Azure Cache for Redis 绑定到 Azure Spring Cloud 应用程序

本文适用于：✔️ Java

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文介绍如何将应用程序绑定到 Azure Redis 缓存。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Redis 缓存服务实例
* 用于 Azure CLI 的 Azure Spring Cloud 扩展

如果没有已部署的 Azure Spring Cloud 实例，请按照[有关如何部署 Azure Spring Cloud 应用的快速入门](./quickstart.md)中的步骤操作。

## <a name="prepare-your-java-project"></a>准备 Java 项目

1. 在项目的 pom.xml 文件中，添加以下依赖项：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. 从 `application.properties` 文件中删除任何 `spring.redis.*` 属性

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 创建新的部署。

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>将应用与 Azure Cache for Redis 绑定

#### <a name="service-binding"></a>[服务绑定](#tab/Service-Binding)
1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 转到“应用程序仪表板”，选择要绑定到 Azure Cache for Redis 的应用程序。 此应用程序是在上一步更新或部署的应用程序。

1. 选择“服务绑定”，然后选择“创建服务绑定” 。 填充窗体，确保选择“绑定类型”值“Azure Cache for Redis”、你的 Azure Cache for Redis 服务器，以及“主密钥”选项。 

1. 重新启动应用。 绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应如下所示：

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

以下 Terraform 脚本演示如何使用 Azure Cache for Redis 设置 Azure Spring Cloud 应用。

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>后续步骤

本问介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Cache for Redis。 若要详细了解如何将服务绑定到应用程序，请参阅[绑定到 Azure Database for MySQL 实例](./how-to-bind-mysql.md)。
