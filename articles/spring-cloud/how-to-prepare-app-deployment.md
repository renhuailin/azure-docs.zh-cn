---
title: 如何准备要部署到 Azure Spring Cloud 中的应用程序
description: 了解如何准备要部署到 Azure Spring Cloud 中的应用程序。
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/06/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: faa4c57a4fc5e75d0e6262833c27833e9069fb30
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418709"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>准备要部署到 Azure Spring Cloud 中的应用程序

::: zone pivot="programming-language-csharp"
Azure Spring Cloud 提供强大的服务来托管、监视、缩放和更新 Steeltoe 应用。 本文介绍了如何准备现有的需要部署到 Azure Spring Cloud 的 Steeltoe 应用程序。

本文介绍了在 Azure Spring Cloud 中运行 .NET Core Steeltoe 应用所需的依赖项、配置和代码。 有关如何将应用程序部署到 Azure Spring Cloud 的信息，请参阅[部署首个 Azure Spring Cloud 应用程序](./quickstart.md)。

>[!Note]
> 针对 Azure Spring Cloud 的 Steeltoe 支持目前以公共预览版的形式提供。 使用公共预览版产品/服务，客户可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。  有关预览期间支持的详细信息，请参阅[常见问题解答](https://azure.microsoft.com/support/faq/)或提交[支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="supported-versions"></a>支持的版本

Azure Spring Cloud 支持：

* .NET Core 3.1
* Steeltoe 2.4 和 3.0

## <a name="dependencies"></a>依赖项

对于 Steeltoe 2.4，请将最新的 [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 包添加到项目文件中：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

对于 Steeltoe 3.0，请将最新的 [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 包添加到项目文件中：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>更新 Program.cs

在 `Program.Main` 方法中，调用 `UseAzureSpringCloudService` 方法。

对于 Steeltoe 2.4.4，请在 `ConfigureWebHostDefaults` 和 `AddConfigServer` 之后调用 `UseAzureSpringCloudService`（如调用）：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

对于 Steeltoe 3.0.0，请在 `ConfigureWebHostDefaults` 和任何 Steeltoe 配置代码之前调用 `UseAzureSpringCloudService`：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>启用 Eureka Server 服务发现

在该应用在 Azure Spring Cloud 中运行时将使用的配置源中，将 `spring.application.name` 设置为与将项目部署到的 Azure Spring Cloud 应用相同的名称。

例如，如果将名为 `EurekaDataProvider` 的 .NET 项目部署到名为 `planet-weather-provider` 的 Azure Spring Cloud 应用中，则 appSettings.json 文件应包含以下 JSON：

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>使用服务发现

若要通过使用 Eureka Server 服务发现来调用服务，请将 HTTP 请求发送给 `http://<app_name>`，其中 `app_name` 是目标应用的 `spring.application.name` 的值。 例如，以下代码调用 `planet-weather-provider` 服务：

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```

::: zone-end

::: zone pivot="programming-language-java"
本主题介绍如何准备现有的需要部署到 Azure Spring Cloud 的 Java Spring 应用程序。 在配置正确的情况下，Azure Spring Cloud 可以提供强大的服务来监视、缩放和更新 Java Spring Cloud 应用程序。

在运行此示例之前，可以尝试[基础知识快速入门](./quickstart.md)。

其他示例说明了在配置 POM 文件时，如何将应用程序部署到 Azure Spring Cloud。

* [启动第一个应用](./quickstart.md)
* [生成并运行微服务](./quickstart-sample-app-introduction.md)

本文介绍所需的依赖项，以及如何将它们添加到 POM 文件。

## <a name="java-runtime-version"></a>Java 运行时版本

只有 Spring/Java 应用程序能够在 Azure Spring Cloud 中运行。

Azure Spring Cloud 支持 Java 8 和 Java 11。 托管环境包含用于 Azure 的最新版 Azul Zulu OpenJDK。 若要详细了解用于 Azure 的 Azul Zulu OpenJDK，请参阅[安装 JDK](/azure/developer/java/fundamentals/java-jdk-install)。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

若要准备要部署到 Azure Spring Cloud 的现有 Spring Boot 应用程序，请按以下部分中所述，在应用程序 POM 文件中包含 Spring Boot 和 Spring Cloud 依赖项。

Azure Spring Cloud 将在发布后的一个月内支持最新的 Spring Boot 或 Spring Cloud 版本。 可以从 [Spring Boot 版本](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases)获取支持的 Spring Boot 版本，从 [Spring Cloud 版本](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases)获取 Spring Cloud 版本。 

下表列出了支持的 Spring Boot 和 Spring Cloud 组合：

Spring Boot 版本 | Spring Cloud 版本
---|---
2.3.x | Hoxton.SR8+
2.4.x、2.5.x | 2020.0 即 Ilford +

> [!NOTE]
> - 请将 Spring Boot 升级到 2.5.2 或 2.4.8，以解决以下 CVE 报告的问题：[CVE-2021-22119：使用 spring-security-oauth2-client 时出现拒绝服务攻击](https://tanzu.vmware.com/security/cve-2021-22119)。 如果使用的是 Spring Security，请将其升级到 5.5.1、5.4.7、5.3.10 或 5.2.11。
> - 我们发现 Spring Boot 2.4.0 在应用与 Spring Cloud 服务注册表之间的 TLS 身份验证上存在问题，请使用 2.4.1 或更高版本。 如果坚持使用 2.4.0，请参阅[常见问题解答](./faq.md?pivots=programming-language-java#development)以获取解决方法。

### <a name="dependencies-for-spring-boot-version-2223"></a>Spring Boot 版本 2.2/2.3 的依赖项

对于 Spring Boot 版本 2.2，请将以下依赖项添加到应用程序 POM 文件中。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Spring Boot 版本 2.4 的依赖项

对于 Spring Boot 版本 2.2，请将以下依赖项添加到应用程序 POM 文件中。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.8</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> 请勿在配置中指定 `server.port`。 Azure Spring Cloud 会将此设置重写为固定端口号。 也请遵从此设置，不要在代码中指定服务器端口。

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>启用 Azure Spring Cloud 功能的其他推荐依赖项

若要启用服务注册表到分布式跟踪的 Azure Spring Cloud 内置功能，你还需要在应用程序中包含以下依赖项。 如果不需要特定应用的相应功能，你可以删除这些依赖项。

### <a name="service-registry"></a>服务注册表

若要使用托管的 Azure 服务注册表服务，请在 pom.xml 文件中包括 `spring-cloud-starter-netflix-eureka-client` 依赖项，如下所示：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

服务注册表服务器的终结点自动作为应用的环境变量注入。 然后，应用程序可自行注册到服务注册表服务器，并发现其他依赖性微服务。

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 注释

将以下注释添加到应用程序源代码中。

```java
@EnableDiscoveryClient
```

有关示例，请参阅前面示例中的 piggymetrics 应用程序：

```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>分布式配置

若要启用分布式配置，请在 pom.xml 文件的 dependencies 节中包括以下 `spring-cloud-config-client` 依赖项：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

> [!WARNING]
> 请勿在启动配置中指定 `spring.cloud.config.enabled=false`。 否则，应用程序将再也不能与配置服务器配合使用。

### <a name="metrics"></a>指标

在 pom.xml 文件的 dependencies 节中包括 `spring-boot-starter-actuator` 依赖项，如下所示：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 指标会定期从 JMX 终结点拉取。 可以通过 Azure 门户将指标可视化。

 > [!WARNING]
 > 请在配置属性中指定 `spring.jmx.enabled=true`。 否则，无法在 Azure 门户中直观显示指标。

### <a name="distributed-tracing"></a>分布式跟踪

还需让 Azure Application Insights 实例能够兼容 Azure Spring Cloud 服务实例。 若要了解如何将 Application Insights 与 Azure Spring Cloud 配合使用，请参阅[有关分布式跟踪的文档](./how-to-distributed-tracing.md)。

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3

在 pom.xml 文件的 dependencies 节中包括下面的 `spring-cloud-starter-sleuth` 和 `spring-cloud-starter-zipkin` 依赖项：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Spring Boot 2.4

在 pom.xml 文件的 dependencies 节中包括下面的 `spring-cloud-sleuth-zipkin` 依赖项：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>另请参阅

* [分析应用程序日志和指标](./diagnostic-services.md)
* [设置配置服务器](./how-to-config-server.md)
* [将分布式跟踪与 Azure Spring Cloud 配合使用](./how-to-distributed-tracing.md)
* [Spring 快速入门指南](https://spring.io/quickstart)
* [Spring Boot 文档](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>后续步骤

本主题介绍了如何配置 Java Spring 应用程序，以便将其部署到 Azure Spring Cloud。 若要了解如何设置配置服务器实例，请参阅[设置配置服务器实例](./how-to-config-server.md)。

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
::: zone-end
