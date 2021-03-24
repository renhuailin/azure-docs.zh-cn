---
title: 实时流式传输 Azure Spring Cloud 应用日志
description: 如何使用日志流式处理来立即查看应用程序日志
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877178"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>实时流式传输 Azure Spring Cloud 应用日志

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 支持在 Azure CLI 中进行日志流式处理，以获取实时应用程序控制台日志，从而进行故障排除。 还可以[通过诊断设置分析日志和指标](./diagnostic-services.md)。

## <a name="prerequisites"></a>先决条件

* 安装适用于 Spring Cloud 的 [Azure CLI 扩展](/cli/azure/install-azure-cli)，最低版本为 0.2.0。
* 具有正在运行的应用程序的 Azure Spring Cloud 实例，例如 [Spring Cloud 应用](./spring-cloud-quickstart.md)。

> [!NOTE]
>  ASC CLI 扩展已从 0.2.0 版本更新为 0.2.1 版本。 此更改影响日志流式处理命令的语法：`az spring-cloud app log tail`，将其替换为 `az spring-cloud app logs`。 `az spring-cloud app log tail` 命令将在未来版本中弃用。 如果你一直使用 0.2.0 版本，可以升级到 0.2.1。 首先，使用 `az extension remove -n spring-cloud` 命令删除旧版本。  然后，使用 `az extension add -n spring-cloud` 命令安装 0.2.1 版本。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 跟踪日志

若要避免重复指定资源组和服务实例名称，请设置默认资源组名称和群集名称。
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
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
默认情况下，`az spring-cloud ap log tail` 仅打印流式传输到应用控制台的现有日志，然后退出。 如果要流式传输新日志，请添加 -f (--follow)：  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
查看支持的所有日志记录选项：
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>后续步骤
* [快速入门：通过日志、指标和跟踪来监视 Azure Spring Cloud 应用](spring-cloud-quickstart-logs-metrics-tracing.md)
* [通过诊断设置分析日志和指标](./diagnostic-services.md)

