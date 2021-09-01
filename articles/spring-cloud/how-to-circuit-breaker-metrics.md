---
title: 通过 Micrometer 收集 Spring Cloud Resilience4J 断路器指标
description: 如何在 Azure Spring Cloud 中通过 Micrometer 收集 Spring Cloud Resilience4J 断路器指标。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: dae08798aa7b3bc1937295e0cd7701446bd7259e
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015555"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>通过 Micrometer 收集 Spring Cloud Resilience4J 断路器指标（预览版）

本文档介绍了如何通过 Application Insights java 进程内代理收集 Spring Cloud Resilience4j 断路器指标。 利用此功能，你可以使用 Micrometer 通过 Application Insights 监视 resilience4j 断路器的指标。

我们使用 [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 来展示其工作原理。

## <a name="prerequisites"></a>先决条件

* 根据 [Application Insights 的 Java 进程内代理指南](./how-to-application-insights.md#enable-java-in-process-agent-for-application-insights)启用 Java 进程内代理。
* 根据 [Application Insights 指南](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)启用 resilience4j 指标的维度收集。
* 安装 git、Maven 和 Java（如果开发计算机尚未使用这些程序）。

## <a name="build-and-deploy-apps"></a>构建和部署应用

以下过程构建并部署应用。

1. 克隆并构建演示存储库。

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. 创建包含终结点的应用程序

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. 部署应用程序。

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * 包括 Resilience4j 所需的依赖项：
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
>
> * 客户代码必须使用 `CircuitBreakerFactory` 的 API，该 API 是在你包括 Spring Cloud 断路器入门版时作为自动创建的 `bean` 实现的。 有关详细信息，请参阅 [Spring Cloud 断路器](https://spring.io/projects/spring-cloud-circuitbreaker#overview)。
>
> * 以下 2 个依赖项与上述 resilient4j 包冲突。  请确保客户不要包括这些依赖项。
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> 导航到网关应用程序提供的 URL，然后从 [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 访问终结点，如下所示：
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>从门户查找 Resilence4j 指标

1. 从 Azure Spring Cloud 门户中选择“Application Insights”边栏选项卡，然后选择“Application Insights”。 

   [ ![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. 从“Application Insights”页面中选择“指标”。   从“指标命名空间”中选择“azure.applicationinsights”。   另外，请选择采用“平均值”的“resilience4j_circuitbreaker_buffered_calls”指标。 

   [ ![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. 选择采用“平均值”的“resilience4j_circuitbreaker_calls”指标。 

   [ ![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. 选择采用“平均值”的“resilience4j_circuitbreaker_calls”指标。  选择“添加筛选器”，然后选择“createNewAccount”作为名称。 

   [ ![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. 选择采用“平均值”的“resilience4j_circuitbreaker_calls”指标。   选择“应用拆分”，然后选择“种类”。

   [ ![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. 选择采用“平均值”的“resilience4j_circuitbreaker_calls”、“resilience4j_circuitbreaker_buffered_calls”和“resilience4j_circuitbreaker_slow_calls” 指标。

   [ ![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>另请参阅

* [Application insights](./how-to-application-insights.md)
* [分布式跟踪](./how-to-distributed-tracing.md)
* [断路器仪表板](./tutorial-circuit-breaker.md)
