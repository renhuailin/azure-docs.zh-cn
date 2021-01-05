---
title: 收集春季 Cloud Resilience4J 断路器指标
description: 如何收集春季 Cloud Resilience4J 断路器指标。
author: MikeDodaro
ms.author: brendanm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3fd1e644d69fe1d721526afcacb362adca48bf7a
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831799"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a> (预览收集弹簧 Cloud Resilience4J 断路器指标) 

本文档介绍了如何 Application Insights java 进程内代理收集弹簧 Cloud Resilience4j 断路器指标。  利用此功能，可以监视 Application Insights 中的 resilience4j 断路器的指标。

我们使用 "春季-断路器" [-断路器演示](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 其工作原理。

## <a name="prerequisites"></a>先决条件

* 启用 java In-Process Agent 中的 Java In-Process 代理 [以获取 Application Insights 指南](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-application-insights#enable-java-in-process-agent-for-application-insights)。 

* 为 [Application Insights 指南](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation)中的 resilience4j 指标启用维度收集。

* 安装 git、Maven 和 Java （如果开发计算机尚未使用该程序）。

## <a name="build-and-deploy-apps"></a>构建和部署应用

以下过程构建并部署应用。

1. 克隆并构建演示存储库。

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. 创建包含终结点的应用程序

```azcli
az spring-cloud app create --name resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. 部署应用程序。

```azcli
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
> * 客户代码必须使用的 API `CircuitBreakerFactory` ，该 API 在 `bean` 包含春季云断路器 starter 时自动创建。 有关详细信息，请参阅 [春季断路器断路](https://spring.io/projects/spring-cloud-circuitbreaker#overview)器。
>
> * 以下2个依赖关系与上述 resilient4j 包冲突。  确保客户不包含这些客户。
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
> 导航至网关应用程序提供的 URL，并按如下所示从 " [春季-](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 断路器" 演示访问终结点：
>
>   ```
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>从门户查找 Resilence4j 指标

1. 从 Azure 春季 Cloud 门户中选择 " **Application Insights** " 边栏选项卡，并单击 " **Application Insights**"。

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. 从 " **Application Insights** " 页中选择 "**指标**"。  从 "**指标命名空间**" 选择 **applicationinsights.config** 。  同时选择 " **resilience4j_circuitbreaker_buffered_calls** 度量值 **"。**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. 选择 **resilience4j_circuitbreaker_calls** 度量值和 **平均值**。

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. 选择 **resilience4j_circuitbreaker_calls**  度量值和 **平均值**。  单击 " **添加筛选器**"，然后选择 "名称" 作为 **createNewAccount**。

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. 选择 **resilience4j_circuitbreaker_calls**  度量值和 **平均值**。  然后单击 " **应用拆分**"，并选择 " **类型**"。

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. 选择 " **resilience4j_circuitbreaker_calls**"、"**resilience4j_circuitbreaker_buffered_calls**" 和 "**平均值**" **resilience4j_circuitbreaker_slow_calls** 指标。

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>另请参阅

* [Application insights](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-application-insights)
* [分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)
* [断路器仪表板](spring-cloud-tutorial-circuit-breaker.md)