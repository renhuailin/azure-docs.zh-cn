---
title: 实时流式传输 Azure Spring Cloud 应用日志
description: 如何使用日志流式处理来立即查看应用程序日志
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: cc34e87823e11b2c80d669edb0afe50703e38527
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015508"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>实时流式传输 Azure Spring Cloud 应用日志

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 支持在 Azure CLI 中进行日志流式处理，以获取实时应用程序控制台日志，从而进行故障排除。 还可以[通过诊断设置分析日志和指标](./diagnostic-services.md)。

## <a name="prerequisites"></a>先决条件

* 安装适用于 Spring Cloud 的 [Azure CLI 扩展](/cli/azure/install-azure-cli)，最低版本为 0.2.0。
* 具有正在运行的应用程序的 Azure Spring Cloud 实例，例如 [Spring Cloud 应用](./quickstart.md)。

> [!NOTE]
>  ASC CLI 扩展已从 0.2.0 版本更新为 0.2.1 版本。 此更改影响日志流式处理命令的语法：`az spring-cloud app log tail`，将其替换为 `az spring-cloud app logs`。 `az spring-cloud app log tail` 命令将在未来版本中弃用。 如果你一直使用 0.2.0 版本，可以升级到 0.2.1。 首先，使用 `az extension remove -n spring-cloud` 命令删除旧版本。  然后，使用 `az extension add -n spring-cloud` 命令安装 0.2.1 版本。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 跟踪日志

若要避免重复指定资源组和服务实例名称，请设置默认资源组名称和群集名称。

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
```

在以下示例中，将在命令中省略资源组和服务名称。

### <a name="tail-log-for-app-with-single-instance"></a>跟踪具有单个实例的应用的日志

如果名为 auth-service 的应用只有一个实例，则可以使用以下命令查看该应用实例的日志：

```azurecli
az spring-cloud app logs -n auth-service
```

这会返回以下日志：

```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>跟踪具有多个实例的应用的日志

如果名为 `auth-service` 的应用具有多个实例，则可以使用 `-i/--instance` 选项查看实例日志。

首先，可以通过以下命令获取应用实例名称。

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```

结果如下：

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
```

然后，可以使用 `-i/--instance` 选项来流式传输应用实例日志：

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

还可以从 Azure 门户获取应用实例的详细信息。  在 Azure Spring Cloud 服务的左侧导航窗格中选择“应用”之后，选择“应用实例” 。

### <a name="continuously-stream-new-logs"></a>连续流式传输新日志

默认情况下，`az spring-cloud app logs` 仅打印流式传输到应用控制台的现有日志，然后退出。 如果要流式传输新日志，请添加 -f (--follow)：

```azurecli
az spring-cloud app logs -n auth-service -f
```

查看支持的所有日志记录选项：

```azurecli
az spring-cloud app logs -h
```

### <a name="format-json-structured-logs"></a>设置 JSON 结构化日志的格式

> [!NOTE]
> 需要 spring-cloud 扩展版本 2.4.0 或更高版本。

为应用启用[结构化应用程序日志](./structured-app-log.md)后，日志将以 JSON 格式打印。 这种格式难以阅读。 可以使用 `--format-json` 参数将 JSON 日志格式化为人工可读格式。

```shell
# Raw JSON log
$ az spring-cloud app logs -n auth-service
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Disable delta property : false"}
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Single vip registry refresh property : null"}

# Formatted JSON log
$ az spring-cloud app logs -n auth-service --format-json
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Disable delta property : false
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Single vip registry refresh property : null
```

参数 `--format-json` 还可采用可选的自定义格式，使用关键字参数 [ 格式字符串语法 ](https://docs.python.org/3/library/string.html#format-string-syntax)。

```shell
# Custom format
$ az spring-cloud app logs -n auth-service --format-json="{message}{n}"
Disable delta property : false
Single vip registry refresh property : null
```

> 使用的默认格式为：
>
> ```format
> {timestamp} {level:>5} [{thread:>15.15}] {logger{39}:<40.40}: {message}{n}{stackTrace}
> ```

## <a name="next-steps"></a>后续步骤

* [快速入门：通过日志、指标和跟踪来监视 Azure Spring Cloud 应用](./quickstart-logs-metrics-tracing.md)
* [通过诊断设置分析日志和指标](./diagnostic-services.md)
