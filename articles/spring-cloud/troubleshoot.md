---
title: Azure Spring Cloud 故障排除指南 | Microsoft Docs
description: Azure Spring Cloud 的故障排除指南
author: karlerickson
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 98f9a87825a2eb0bbae36255111ba4b019fb4750
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015388"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>排查常见的 Azure Spring Cloud 问题

本文指导如何排查 Azure Spring Cloud 开发问题。 有关更多信息，请参阅 [Azure Spring Cloud 常见问题解答](./faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、性能和应用程序问题

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的应用程序无法启动（例如，无法连接终结点，或者在重试几次后返回 502）

将日志导出到 Azure Log Analytics。 Spring 应用程序日志的表名为 AppPlatformLogsforSpring。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)。

日志中可能会显示以下错误消息：

> “org.springframework.context.ApplicationContextException：无法启动 Web 服务器”

该消息表示可能存在以下两个问题之一：

* 某个 bean 或其依赖项之一缺失。
* 某个 bean 属性缺失或无效。 在这种情况下，可能会显示“java.lang.IllegalArgumentException”。

服务绑定也可能会导致应用程序启动失败。 若要查询日志，请使用与绑定的服务相关的关键字。 例如，假设应用程序已绑定到设置为本地系统时间的 MySQL 实例。 如果该应用程序无法启动，你可能会在日志中看到以下错误消息：

> “java.sql.SQLException：服务器时区值‘协调世界时’无法识别或表示多个时区。”

若要修复此错误，请转到 MySql 实例的 `server parameters`，并将 `time_zone` 值从 SYSTEM 更改为 +0:00 。

### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的应用程序崩溃或引发意外错误

调试应用程序崩溃时，请先检查应用程序的运行状态和发现状态。 为此，请在 Azure 门户中转到“应用管理”，确保所有应用程序的状态为“正在运行”和“已启动”  。

* 如果应用程序状态为“正在运行”，但发现状态不是“已启动”，请转到[“无法注册我的应用程序”](#my-application-cant-be-registered)部分 。

* 如果发现状态为“已启动”，请转到“指标”检查应用程序的运行状况。 检查以下指标：

   - `TomcatErrorCount` (_tomcat.global.error_)：

      所有 Spring 应用程序异常都会在这里计数。 如果此数字很大，请转到“Azure Log Analytics”检查应用程序日志。

   - `AppMemoryMax` (_jvm.memory.max_)：

      应用程序可用的最大内存量。 数值可能未定义，或者即使已定义，也可能会随时间而变化。 如果已定义，则已使用和已提交的内存量始终小于或等于最大值。但如果分配时尝试增加已用内存，以使已使用内存量大于已提交内存量，则即使仍满足已使用内存量小于最大值，内存分配也可能会失败，并显示 `OutOfMemoryError` 消息 。 在这种情况下，请尝试通过使用 `-Xmx` 参数提高最大堆栈大小。

   - `AppMemoryUsed` (_jvm.memory.used_)：

      应用程序目前使用的内存量（以字节为单位）。 对于正常负载的 Java 应用程序，此指标系列将形成一个“锯齿”模式，即内存使用量先是小幅度地平稳增减，然后突然大幅度下降。此模式会重复。 发生此指标系列是因为在 Java 虚拟机中进行的垃圾回收，其中的收集操作体现在“锯齿”模式的下降部分。

    此指标对于帮助识别内存问题至关重要，例如：

    * 最开始时的内存爆炸。
    * 特定逻辑路径的浪涌内存分配。
    * 渐进式的内存泄漏。

   有关详细信息，请参阅[指标](./concept-metrics.md)。

* 如果应用程序无法启动，请验证应用程序是否具有有效的 jvm 参数。 如果 JVM 内存设置过高，日志中可能会显示以下错误消息：

   > “所需的内存 2728741K 大于可供分配的 2000M”

若要了解有关 Azure Log Analytics 的更多信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/logs/log-analytics-tutorial.md)。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的应用程序的 CPU 使用率或内存使用率过高

如果应用程序的 CPU/内存使用率较高，则表示出现了以下两个问题之一：

* 所有应用实例的 CPU/内存使用率较高。
* 部分应用实例的 CPU/内存使用率较高。

若要确定出现了哪一种情况，请执行以下操作：

1. 转到“指标”，然后选择“服务 CPU 使用率百分比”或“已用服务内存”  。
2. 添加 App= 筛选器，指定要监视哪个应用程序。
3. 按实例拆分指标。

如果所有实例的 CPU/内存使用率较高，则需要横向扩展应用程序或纵向扩展 CPU/内存使用率。 有关详细信息，请参阅[教程：在 Azure Spring Cloud 中缩放应用程序](./how-to-scale-manual.md)。

如果部分实例的 CPU/内存使用率较高，请检查实例状态及其发现状态。

有关详细信息，请参阅 [Azure Spring Cloud 的指标](./concept-metrics.md)。

如果所有实例都已启动并运行，请转到 Azure Log Analytics，以查询应用程序日志并查看代码逻辑。 通过这种方式，可查看其是否影响缩放分区。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)。

若要了解有关 Azure Log Analytics 的更多信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/logs/log-analytics-tutorial.md)。 使用 [Kusto 查询语言](/azure/kusto/query/)查询日志。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>用于将 Spring 应用程序部署到 Azure Spring Cloud 的检查清单

将应用程序加入之前，确保其满足以下条件：

* 应用程序可以使用指定的 Java 运行时版本在本地运行。
* 环境配置（CPU/RAM/实例）符合应用程序提供商规定的最低要求。
* 配置项有预期的值。 有关详细信息，请参阅[配置服务器](./how-to-config-server.md)。
* 环境变量有预期的值。
* JVM 参数有预期的值。
* 建议禁用/删除应用程序包中的嵌入式配置服务器和 Spring 服务注册表服务 。
* 若要通过服务绑定来绑定 Azure 资源，请确保目标资源已启动并运行。

## <a name="configuration-and-management"></a>配置和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure Spring Cloud 服务实例时遇到了问题

当你通过 Azure 门户设置 Azure Spring Cloud 服务实例时，Azure Spring Cloud 将为你执行验证。

但如果你尝试通过使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 或 [Azure 资源管理器模板](../azure-resource-manager/index.yml)设置 Azure Spring Cloud 服务实例，请确认：

* 订阅处于活动状态。
* 位置受 Azure Spring Cloud 的[支持](./faq.md)。
* 已创建实例的资源组。
* 资源名称符合命名规则。 该名称只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 该值必须包含 2 到 32 个字符。

如果要使用资源管理器模板设置 Azure Spring Cloud 服务实例，请先参阅[了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/templates/syntax.md)。

Azure Spring Cloud 服务实例的名称将用于请求 `azureapps.io` 下的子域名，因此，如果该名称与现有名称冲突，设置操作会失败。 可以在活动日志中找到更多详细信息。

### <a name="i-cant-deploy-a-net-core-app"></a>无法部署 .NET Core 应用

无法通过 Azure 门户或资源管理器模板为 .NET Core Steeltoe 应用上传 .zip 文件。

使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 部署应用程序包时，Azure CLI 会定期轮询部署进度，并最终显示部署结果。

确保应用程序打包时采用正确的 .zip 文件格式。 如果未正确打包，进程会停止响应或会收到错误消息。

### <a name="i-cant-deploy-a-jar-package"></a>无法部署 JAR 包

无法通过 Azure 门户或资源管理器模板上传 Java 存档文件 (JAR)/源包。

使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 部署应用程序包时，Azure CLI 会定期轮询部署进度，并最终显示部署结果。

如果轮询中断，仍可使用以下命令提取部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

确保应用程序打包时采用正确的[可执行 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)。 如果未正确打包，你将收到类似于以下内容的错误消息：

> “错误:无效或受损的 jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714”

### <a name="i-cant-deploy-a-source-package"></a>无法部署源包

无法通过 Azure 门户或资源管理器模板上传 JAR/源包。

使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 部署应用程序包时，Azure CLI 会定期轮询部署进度，并最终显示部署结果。

如果轮询中断，仍可使用以下命令提取生成和部署日志：

`az spring-cloud app show-deploy-log -n <app-name>`

但请注意，一个 Azure Spring Cloud 服务实例每次只能对一个源包触发一个生成作业。 有关详细信息，请参阅[部署应用程序](./quickstart.md)和[在 Azure Spring Cloud 中设置过渡环境](./how-to-staging-environment.md)。

### <a name="my-application-cant-be-registered"></a>我的应用程序无法注册

在大多数情况下，如果在项目对象模型 (POM) 文件中未正确配置“所需依赖项”和“服务发现”，就会发生这种情况 。 配置后，内置的服务注册表服务器终结点将作为环境变量注入到应用程序中。 然后，应用程序可自行注册到服务注册表服务器，并发现其他依赖性微服务。

等待至少 2 分钟，然后新注册的实例就会开始接收流量。

如果要将现有的基于 Spring Cloud 的解决方案迁移到 Azure，请确保删除（或禁用）临时的服务注册表和配置服务器实例，以免与 Azure Spring Cloud 提供的托管实例冲突 。

还可以在 Azure Log Analytics 中检查服务注册表客户端日志。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)

若要了解有关 Azure Log Analytics 的更多信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/logs/log-analytics-tutorial.md)。 使用 [Kusto 查询语言](/azure/kusto/query/)查询日志。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要检查应用程序的环境变量

环境变量会通知 Azure Spring Cloud 框架，确保 Azure 了解在何处以何种方式配置构成应用程序的服务。 在排查潜在问题的过程中，必须执行的第一步是确保环境变量正确。  可以使用 Spring Boot Actuator 终结点来查看环境变量。

> [!WARNING]
> 此过程使用测试终结点公开环境变量。  如果测试终结点可以公开访问，或者你已将域名分配给应用程序，请勿继续操作。

1. 转到  `https://<your application test endpoint>/actuator/health` 。

    - 类似于 `{"status":"UP"}` 的响应表明终结点已启用。
    - 如果响应为负面，请在 POM.xml 文件中包括以下依赖项：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 在启用 Spring Boot Actuator 终结点的情况下，请转到 Azure 门户并找到应用程序的配置页。  添加名为 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 且值为 `*` 的环境变量。

1. 重启应用程序。

1. 转到 `https://<your application test endpoint>/actuator/env` 并检查响应。  应如下所示：

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

找到名为 `systemEnvironment` 的子节点。  此节点包含应用程序的环境变量。

> [!IMPORTANT]
> 在使应用程序可以公共访问之前，请记得反转公开环境变量的操作。  转到 Azure 门户，找到应用程序的配置页，然后删除此环境变量：`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>我找不到应用程序的指标或日志

转到“应用管理”，确保应用程序的状态为“正在运行”和“已启动” 。

检查应用程序包中是否启用了 JMX。 可以使用配置属性 `spring.jmx.enabled=true` 启用此功能。

检查应用程序包中是否启用了 `spring-boot-actuator` 依赖项，以及该项是否成功启动。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果应用程序日志可以存档到某个存储帐户，但未发送到 Azure Log Analytics，请检查是否已[正确设置工作区](../azure-monitor/logs/quick-create-workspace.md)。 如果使用的是免费层 Azure Log Analytics，请注意，[免费层不提供服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。

## <a name="next-steps"></a>后续步骤

* [如何自行诊断并解决 Azure Spring Cloud 中的问题](./how-to-self-diagnose-solve.md)
