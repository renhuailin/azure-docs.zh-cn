---
title: 快速入门 - 示例应用简介 - Azure Spring Cloud
description: 介绍本系列快速入门中用于部署到 Azure Spring Cloud 的示例应用。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a44558f6d0a1351c5f9c61fba6d6686757fd3292
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014748"
---
# <a name="introduction-to-the-sample-app"></a>示例应用简介

::: zone pivot="programming-language-csharp"
此系列快速入门使用由两个微服务组成的示例应用，介绍如何将 .NET Core Steeltoe 应用部署到 Azure Spring Cloud 服务。 你将使用 Azure Spring Cloud 功能，如服务发现、Config Server、日志、指标和分布式跟踪。

## <a name="functional-services"></a>功能服务

该示例应用由两个微服务组成：

* `planet-weather-provider` 服务返回天气文本以响应指定行星名称的 HTTP 请求。 例如，对于水星，它可能会返回“非常热”。 它从 Config 服务器获取天气数据。 Config 服务器从 Git 存储库中的 YAML 文件获取天气数据，例如：

   ```yaml
   MercuryWeather: very warm
   VenusWeather: quite unpleasant
   MarsWeather: very cool
   SaturnWeather: a little bit sandy
   ```

* `solar-system-weather` 服务返回四个行星的数据以响应 HTTP 请求。 它通过向 `planet-weather-provider` 发出四个 HTTP 请求来获取数据。 它使用 Eureka 服务器发现服务调用 `planet-weather-provider`。 它将返回 JSON，例如：

   ```json
   [{
       "Key": "Mercury",
       "Value": "very warm"
   }, {
       "Key": "Venus",
       "Value": "quite unpleasant"
   }, {
       "Key": "Mars",
       "Value": "very cool"
   }, {
       "Key": "Saturn",
       "Value": "a little bit sandy"
   }]
   ```

下图对示例应用体系结构进行了说明：

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="示例应用图":::

## <a name="code-repository"></a>代码存储库

示例应用位于 GitHub 上的 Azure-Samples/Azure-Spring-Cloud-Samples 存储库的 [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) 文件夹中。

以下快速入门中的说明会根据需要引用源代码。

::: zone-end

::: zone pivot="programming-language-java"
本快速入门将使用熟悉的示例应用 [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices) 的微服务版本，演示如何将应用部署到 Azure Spring Cloud 服务。 Pet Clinic 示例演示了微服务体系结构模式并强调了服务明细。 你将了解如何通过 Azure Spring Cloud 功能（包括服务发现、Config Server、日志、指标、分布式跟踪和适合开发人员的工具支持）将服务部署到 Azure。

若要遵循 Azure Spring Cloud 部署示例，只需使用根据需要提供的源代码位置。

![PetClinic 的体系结构](media/build-and-deploy/microservices-architecture-diagram.jpg)

## <a name="functional-services-to-be-deployed"></a>要部署的功能服务

PetClinic 分解为 4 核微服务。 所有微服务都是由业务域组织的可独立部署的应用程序。

* **客户服务**：包含普通用户输入逻辑和验证，其中包括宠物和主人信息（姓名、地址、城市、电话）。
* **访问服务**：存储并显示每条宠物评论的访问信息。
* **兽医服务**：存储并显示兽医的信息，包括姓名和专业领域。
* **API 网关**：API 网关是系统中的单个入口点，用于处理请求并将请求路由到相应的服务，或调用多个服务来聚合结果。  这三个核心服务向客户端公开外部 API。 在实际系统中，函数的数量可能会随系统的复杂性迅速增长。 呈现一个复杂的网页可能会涉及数百个服务。

## <a name="infrastructure-services-hosted-by-azure-spring-cloud"></a>由 Azure Spring Cloud 托管的基础结构服务

分布式系统中有几种支持核心服务的常用模式。 Azure Spring Cloud 提供的工具可增强 Spring Boot 应用程序以实现以下模式：

* **配置服务**：Azure Spring Cloud Config 是一种面向分布式系统的可水平缩放的集中式配置服务。 它使用当前支持本地存储、Git 和 Subversion 的可插入存储库。
* **服务发现**：支持自动检测服务实例的网络位置，由于自动缩放、失败和升级，可能会有动态分配的地址。

## <a name="database-configuration"></a>数据库配置

在其默认配置中，PetClinic 使用在启动时用数据填充的内存数据库 (HSQLDB)。 如果需要持久性数据库配置，则为 MySql 提供类似的设置。 Connector/J 的依赖项（MySQL JDBC 驱动程序）已经包含在 pom.xml 文件中。

## <a name="sample-usage-of-petclinic"></a>PetClinic 的示例用法

有关完整实现的详细信息，请参阅 [PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices) 的分支。 示例根据需要引用源代码。

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预配 Azure Spring Cloud 实例](./quickstart-provision-service-instance.md)
