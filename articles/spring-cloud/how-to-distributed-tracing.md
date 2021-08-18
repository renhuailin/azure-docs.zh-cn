---
title: 将分布式跟踪与 Azure Spring Cloud 配合使用
description: 了解如何通过 Azure Application Insights 使用 Spring Cloud 的分布式跟踪
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8eaac1c68c9cff329fcc7b2c408063f01762169b
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014568"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>将分布式跟踪与 Azure Spring Cloud 配合使用

使用 Azure Spring Cloud 中的分布式跟踪工具，可以轻松地调试和监视复杂问题。 Azure Spring Cloud 将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](../azure-monitor/app/app-insights-overview.md) 集成。 这种集成可以通过 Azure 门户提供强大的分布式跟踪功能。

::: zone pivot="programming-language-csharp"
本文介绍如何启用 .NET Core Steeltoe 应用以使用分布式跟踪。

## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需要一个已[准备好部署到 Azure Spring Cloud](how-to-prepare-app-deployment.md) 的 Steeltoe 应用。

## <a name="dependencies"></a>依赖项

对于 Steeltoe 2.4.4，请添加以下 NuGet 包：

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe.Management.ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

对于 Steeltoe 3.0.0，请添加以下 NuGet 包：

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>更新 Startup.cs

1. 对于 Steeltoe 2.4.4，请在 `ConfigureServices` 方法中调用 `AddDistributedTracing` 和 `AddZipkinExporter`。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   对于 Steeltoe 3.0.0，请 `ConfigureServices` 在方法中调用 `AddDistributedTracing`。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. 对于 Steeltoe 2.4.4，请 `Configure` 在方法中调用 `UseTracingExporter`。

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   对于 Steeltoe 3.0.0，不需要在 `Configure` 方法中进行任何更改。

## <a name="update-configuration"></a>更新配置

将以下设置添加到在 Azure Spring Cloud 中运行应用时将使用的配置源：

1. 将 `management.tracing.alwaysSample` 设置为 true。

2. 如果要查看在 Eureka 服务器、配置服务器和用户应用之间发送的跟踪跨度，请将 `management.tracing.egressIgnorePattern` 设置为 "/api/v2/spans|/v2/apps/. */permissions|/eureka/.* |/oauth/.*"。

例如，appsettings.json 将包括以下属性：

```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

有关 .NET Core Steeltoe 应用中的分布式跟踪的详细信息，请参阅 Steeltoe 文档中的[分布式跟踪](https://docs.steeltoe.io/api/v3/tracing/)。
::: zone-end
::: zone pivot="programming-language-java"
在本文中，学习如何：

> [!div class="checklist"]
> * 在 Azure 门户中启用分布式跟踪。
> * 将 Spring Cloud Sleuth 添加到应用程序。
> * 查看微服务应用程序的依赖项映射。
> * 通过不同的筛选器搜索跟踪数据。

## <a name="prerequisites"></a>先决条件

若要完成这些过程，需要一个已预配且正在运行的 Azure Spring Cloud 服务。 请完成[部署第一个 Azure Spring Cloud 应用程序](./quickstart.md)快速入门，以预配和运行 Azure Spring Cloud 服务。

## <a name="add-dependencies"></a>添加依赖项

1. 将以下行添加到 application.properties 文件：

   ```xml
   spring.zipkin.sender.type = web
   ```

   完成此更改后，Zipkin 发送方就可以将内容发送到 Web。

1. 如果已按 [Azure Spring Cloud 应用程序准备指南](how-to-prepare-app-deployment.md)进行了操作，则可跳过此步骤。 否则，请转到本地开发环境并编辑 pom.xml 文件，以添加以下 Spring Cloud Sleuth 依赖项：

    * Spring Boot 版本低于 2.4.x。

      ```xml
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-zipkin</artifactId>
          </dependency>
      </dependencies>
      ```

    * Spring Boot 版本不低于 2.4.x。

      ```xml
      <dependencyManagement>
          <dependencies>
            <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-sleuth-zipkin</artifactId>
           </dependency>
      </dependencies>
      ```

1. 再次为 Azure Spring Cloud 服务执行生成和部署操作，使之反映这些更改。

## <a name="modify-the-sample-rate"></a>修改采样率

可以通过修改采样率，更改遥测数据收集速率。 例如，如果要按平常一半的频率采样，请打开 application.properties 文件并更改以下行：

```xml
spring.sleuth.sampler.probability=0.5
```

如果已生成并部署应用程序，则可修改采样率。 为此，可在 Azure CLI 或 Azure 门户中添加上一行作为环境变量。
::: zone-end

## <a name="enable-application-insights"></a>启用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”页上，选择“分布式跟踪”。 
1. 选择“编辑设置”，以便编辑现有设置或添加新设置。
1. 新建一个 Application Insights 查询，或选择一个现有的。
1. 选择要监视哪个日志记录类别，并指定保留时间（天）。
1. 选择“应用”以应用新跟踪。

## <a name="view-the-application-map"></a>查看应用程序映射

返回到“分布式跟踪”页面，选择“查看应用程序映射”。  查看应用程序和监视设置的视觉表现形式。 若要了解如何使用应用程序，请参阅[应用程序映射：会审分布式应用程序](../azure-monitor/app/app-map.md)。

## <a name="use-search"></a>使用搜索

使用搜索函数查询其他特定的遥测项。 在“分布式跟踪”页面上，选择“搜索”。  有关如何使用搜索函数的详细信息，请参阅[在 Application Insights 中使用搜索](../azure-monitor/app/diagnostic-search.md)。

## <a name="use-application-insights"></a>使用 Application Insights

除了应用程序映射和搜索功能，Application Insights 还提供监视功能。 在 Azure 门户中搜索应用程序的名称，然后打开 Application Insights 页面来查找监视信息。 有关如何使用这些工具的更多指南，请查看 [Azure Monitor 日志查询](/azure/data-explorer/kusto/query/)。

## <a name="disable-application-insights"></a>禁用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”部分，选择“分布式跟踪”。 
1. 选择“禁用”，禁用 Application Insights。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Spring Cloud 中启用并了解分布式跟踪。 若要了解如何将服务绑定到应用程序，请参阅[将 Azure Cosmos DB 数据库绑定到 Azure Spring Cloud 应用程序](./how-to-bind-cosmos.md)。
