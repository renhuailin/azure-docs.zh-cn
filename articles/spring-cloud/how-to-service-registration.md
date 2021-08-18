---
title: 自动执行服务注册表和发现
description: 了解如何使用 Spring Cloud 服务注册表来自动执行服务发现和注册
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 97f0a5326422613a96b530c70cf316b6db1b6b35
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014744"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>发现和注册 Spring Cloud 服务

服务发现是基于微服务的体系结构的关键要求。  手动配置每个客户端会耗费时间，并引入人为错误的可能性。  Azure Spring Cloud 服务注册表解决了这一问题。  配置后，服务注册表服务器将为应用程序的微服务控制服务注册和发现。 服务注册表服务器维护已部署的微服务的注册表、启用客户端负载平衡，并从客户端分离服务提供程序，而无需依赖 DNS。

::: zone pivot="programming-language-csharp"
要了解如何为 Steeltoe 应用设置服务注册，请参阅[准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序](how-to-prepare-app-deployment.md)。
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>使用 Spring Cloud 服务注册表注册应用程序

必须在应用程序的 pom.xml 文件中包含若干依赖项，应用程序才能使用 Spring Cloud 服务注册表管理服务注册和发现。
将 spring-cloud-starter-netflix-eureka-client 和 spring-cloud-starter-azure-spring-cloud-client 的依赖项添加到 pom.xml  

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>3.0.2</version>
        </dependency>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
            <version>2.1.0</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-client</artifactId>
            <version>1.19.4</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey.contribs</groupId>
            <artifactId>jersey-apache-client4</artifactId>
            <version>1.19.4</version>
        </dependency>
```

## <a name="update-the-top-level-class"></a>更新顶级类

最后，向应用程序的顶级类添加注释

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

    @SpringBootApplication
    @EnableEurekaClient
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring Cloud 服务注册表服务器终结点将作为环境变量注入到应用程序中。  现在，微服务将可以自行注册到服务注册表服务器并发现其他依赖性微服务。

请注意，将更改从服务器传播到所有微服务可能需要几分钟时间。
::: zone-end
