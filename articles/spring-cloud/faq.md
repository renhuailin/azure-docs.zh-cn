---
title: 有关 Azure Spring Cloud 的常见问题解答 | Microsoft Docs
description: 本文解答有关 Azure Spring Cloud 的常见问题。
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 5cc02d09efda462a2feaaa77ff74dff9dedd2041
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860806"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud 常见问题解答

本文解答有关 Azure Spring Cloud 的常见问题。

## <a name="general"></a>常规

### <a name="why-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

Azure Spring Cloud 为 Spring Cloud 开发人员提供平台即服务 (PaaS)。 Azure Spring Cloud 可以管理应用程序基础结构，使你能够专注于应用程序代码和业务逻辑。 Azure Spring Cloud 中内置的核心功能包括 Eureka、配置服务器、服务注册表服务器、Pivotal Build Service、蓝绿部署，等等。 此服务还可让开发人员将其应用程序绑定到 Azure Cosmos DB、 Azure Database for MySQL 和 Azure Cache for Redis 等其他 Azure 服务。

Azure Spring Cloud 通过集成 Azure Monitor、Application Insights 和 Log Analytics 来增强开发人员和运营商的应用程序诊断体验。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隐私性是 Azure 及 Azure Spring Cloud 客户最关注的问题之一。 Azure 将会安全加密所有应用程序数据、日志或配置，有助于确保只有各自的客户才能访问这些数据。

* Azure Spring Cloud 中的服务实例彼此隔离。
* Azure Spring Cloud 提供完整的 TLS/SSL 和证书管理。
* 适用于 OpenJDK 和 Spring Cloud 运行时的关键安全修补程序会尽快应用到 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure Spring Cloud 在哪些区域可用？

美国东部、美国东部 2、美国中部、美国中南部、美国中北部、美国西部、美国西部 2、西欧、北欧、英国南部、东南亚、澳大利亚东部、加拿大中部、阿拉伯联合酋长国北部、印度中部、韩国中部、东亚、日本东部、南非北部和中国东部 2 (Mooncake)。 [了解详细信息](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>是否有客户数据存储在指定区域之外？

Azure Spring Cloud 是一种区域服务。 Azure Spring Cloud 中的所有客户数据都存储在指定区域的同一地区内的多个区域，以实现冗余。 若要了解有关地区和区域的详细信息，请参阅 [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud 有哪些已知限制？

Azure Spring Cloud 有以下已知限制：

* 用于创建每个应用程序的应用程序名称将会覆盖 `spring.application.name`。
* `server.port` 默认设置为端口 1025。 如果应用其他任何值，则该值将被替代。 请遵从此设置，不要在代码中指定服务器端口。
* Azure 门户和 Azure 资源管理器模板不支持上传应用程序包。 如需上传应用程序包，仅可通过 Azure CLI 部署应用程序。

### <a name="what-pricing-tiers-are-available"></a>有哪些定价层可用？

我应使用哪一种？每个层中的限制是什么？

* Azure Spring Cloud 提供两个定价层：“基本”和“标准”。 基本层面向开发/测试和试用 Azure Spring Cloud。 标准层经过优化，可运行常规用途生产流量。 有关限制和功能级别的比较，请参阅 [Azure Spring Cloud 定价详细信息](https://azure.microsoft.com/pricing/details/spring-cloud/)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供反馈和报告问题？

如果遇到 Azure Spring Cloud 的问题，请创建 [Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 若要提交功能请求或提供反馈，请参阅 [Azure 反馈](https://feedback.azure.com/forums/34192--general-feedback)。

## <a name="development"></a>开发

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是一名 Spring Cloud 开发人员，但不熟悉 Azure。 能够最快帮助我了解如何开发 Azure Spring Cloud 应用程序的方法是什么？

如需了解开始使用 Azure Spring Cloud 的最快方法，请遵循[快速入门：通过使用 Azure 门户启动 Azure Spring Cloud 应用程序](./quickstart.md)中的说明。

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支持哪些 Java 运行时？

Azure Spring Cloud 支持 Java 8 和 11。 请参阅 [Java 运行时和操作系统版本](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>是否支持 Spring Boot 2.4.x？
我们已确认 Spring Boot 2.4 出现问题，目前正与 Spring 社区合作解决该问题。 请提供在应用和 Eureka 之间启用 TLS 身份验证所需的这两个依赖项。

```xml
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

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>在哪里可以查看 Spring Cloud 应用程序日志和指标？

在“应用概述”选项卡和“[Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer)”选项卡中可以找到指标。

Azure Spring Cloud 支持将 Spring Cloud 应用程序日志和指标导出到 Azure 存储、事件中心和 [Log Analytics](../azure-monitor/logs/data-platform-logs.md)。 Log Analytics 中的表名称是 AppPlatformLogsforSpring。 若要了解如何启用它，请参阅[诊断服务](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支持分布式跟踪？

是的。 有关详细信息，请参阅[教程：将分布式跟踪与 Azure Spring Cloud 配合使用](./how-to-distributed-tracing.md)。

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>服务绑定支持哪些资源类型？

目前支持三个服务：

* Azure Cosmos DB
* Azure Database for MySQL
* Azure Redis 缓存。
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>是否可以从应用程序内部查看、添加或移动永久性卷？

是的。

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Azure Spring Cloud 实例有多少出站公共 IP 地址？

出站公共 IP 地址数可能因层级和其他因素而异。

| Azure Spring Cloud 实例类型 | 默认的出站公共 IP 地址数 |
| -------------------------------- | ---------------------------------------------- |
| 基本层实例             | 1                                              |
| 标准层实例          | 2                                              |
| VNet 注入实例         | 1                                              |

### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>是否可以增加出站公共 IP 地址的数量？

是，你可以提交[支持票证](https://azure.microsoft.com/support/faq/)以请求更多出站公共 IP 地址。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>删除/移动 Azure Spring Cloud 服务实例时，是否也会删除/移动其扩展资源？

这取决于拥有扩展资源的资源提供程序的逻辑。 `Microsoft.AppPlatform` 实例的扩展资源不属于同一命名空间，因此行为因资源提供程序而异。 例如，删除/移动操作不会级联到诊断设置资源。 如果为新的 Azure Spring Cloud 实例预配了与已删除实例相同的资源 ID，或者将以前的 Azure Spring Cloud 实例移回，则先前的诊断设置资源将继续对其进行扩展。

可以使用 Azure CLI 删除 Spring Cloud 的诊断设置：

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java 运行时和操作系统版本

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure Spring Cloud 中支持哪些版本的 Java 运行时？

Azure Spring Cloud 支持具有最新内部版本的 Java LTS 版本，当前支持的版本为 2020年 6 月版本、java 8 和 Java 11。 请参阅[安装适用于 Azure 和 Azure Stack 的 JDK](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>谁构建了这些 Java 运行时？

Azul 系统。 Azul Zulu for Azure - Enterprise Edition 内部版 JDK 是适用于 Azure 和 Azure Stack 的 OpenJDK 的免费、多平台、生产就绪型发行版，由 Microsoft 及 Azul Systems 提供支持。 这些版本包含构建和运行 Java SE 应用程序所需的所有组件。

### <a name="how-often-will-java-runtimes-get-updated"></a>Java 运行时多久更新一次？

这些 LTS 和 MTS JDK 版本提供季度安全更新程序和 Bug 修补程序，并根据需要提供关键的带外更新和修补程序。 此支持包括更新版 Java（例如 Java 11）中报告的针对 Java 7 和 8 的安全更新和 Bug 修复的后向移植。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 和 Java 11 LTS 版本将受到多长时间的支持？

请参阅[针对 Azure 和 Azure Stack 的 Java 长期支持](/azure/developer/java/fundamentals/java-support-on-azure)。

* 对 Java 8 LTS 的支持将持续至 2030 年 12 月。
* 对 Java 11 LTS 的支持将持续至 2027 年 9 月。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>如何下载本地开发支持的 Java 运行时？

请参阅[安装适用于 Azure 和 Azure Stack 的 JDK](/azure/developer/java/fundamentals/java-jdk-install)。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>对于较旧的 Java 运行时，停用策略是什么？

公开通知将在旧的运行时版本停用之前 12 个月发出。 你可以在 12 个月内迁移到更高版本。

* 订阅管理员将在我们停用 Java 版本时收到电子邮件通知。
* 停用信息将在文档中发布。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>如何获取对 Java 运行时级别的问题的支持？

可以通过 Azure 支持提交支持票证。  请参阅[如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>运行应用的操作系统是什么？

操作系统使用最新的 Ubuntu LTS 版本，目前 [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/) 是默认操作系统。

### <a name="how-often-are-os-security-patches-applied"></a>操作系统安全修补程序多久应用一次？

适用于 Azure Spring Cloud 的安全修补程序每月向生产环境推出。
适用于 Azure Spring Cloud 的关键安全修补程序（CVE 评分 > = 9）将尽快推出。
::: zone-end

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud 是否支持蓝绿部署？

是的。 有关详细信息，请参阅[设置过渡环境](./how-to-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>是否可以访问 Kubernetes 来操作应用程序容器？

不是。  Azure Spring Cloud 可使开发人员抽身于底层体系结构，将注意力放在应用程序代码和业务逻辑上。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支持从源构建容器？

是的。 有关详细信息，请参阅 [从源代码启动 Spring Cloud 应用程序](./quickstart.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支持应用实例中的自动缩放？

是的。  有关详细信息，请参阅[设置自动缩放](./how-to-setup-autoscale.md)。

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>将现有 Spring Cloud 微服务迁移到 Azure Spring Cloud 的最佳做法是什么？

在将现有 Spring Cloud 微服务迁移到 Azure Spring Cloud 时，最好遵循以下最佳做法：

* 需要解析所有应用程序依赖项。
* 准备好配置项、环境变量和 JVM 参数，以便可将其与 Azure Spring Cloud 中的部署进行比较。
* 若要使用服务绑定，请分析你的 Azure 服务，并确保设置适当的访问权限。
* 我们建议删除或禁用可能与 Azure Spring Cloud 管理的服务（例如，我们的服务发现服务、配置服务器等）相冲突的嵌入式服务。
* 我们建议使用官方的、稳定的 Pivotal Spring 库。 非正式版、beta 版或克隆版的 Pivotal Spring 库不附带服务级别协议 (SLA) 支持。

迁移后，请监视 CPU/RAM 指标和网络流量，以确保适当缩放应用程序实例。
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core 版本

### <a name="which-net-core-versions-are-supported"></a>支持哪些 .NET Core 版本？

.NET Core 3.1 和更高版本。

### <a name="how-long-will-net-core-31-be-supported"></a>将支持 .NET Core 3.1 到什么时候？

直到 2022 年 12 月 3 日。 请参阅 [.NET Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。
::: zone-end

## <a name="troubleshooting"></a>疑难解答

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>极少发生的服务注册表不可用情况会产生什么影响？

在一些很少发生的场景中，你可能会从应用程序日志中看到如下错误：

```output
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```

由于网络不稳定或其他网络问题，Spring 框架因速率极低引入了此问题。

这应该不会对用户体验产生影响，eureka 客户端具有检测信号和重试策略来解决此问题。 可以将它视为一个暂时性错误，放心地忽略它。

我们将在不久的将来增强这方面，避免用户的应用程序中出现此错误。

## <a name="next-steps"></a>后续步骤

如果有其他问题，请参阅 [Azure Spring Cloud 故障排除指南](./troubleshoot.md)。
