---
title: 在 Spring Boot 应用中使用动态配置
titleSuffix: Azure App Configuration
description: 了解如何动态更新 Spring Boot 应用的配置数据
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 22214c6113d182363ccd86d9e79dac971eb0e432
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465809"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>教程：在 Java Spring 应用中使用动态配置

应用程序配置包含两个适用于 Spring 的库。 `azure-spring-cloud-appconfiguration-config` 需要 Spring Boot 并依赖于 `spring-cloud-context`。 `azure-spring-cloud-appconfiguration-config-web` 需要 Spring Web 以及 Spring Boot。 支持手动触发这两个库来检查刷新的配置值。 `azure-spring-cloud-appconfiguration-config-web` 还添加了自动检查配置刷新的支持。

通过刷新操作，无需重启应用程序即可刷新配置值，不过，这会导致在 `@RefreshScope` 中重新创建所有的 bean。 客户端库将缓存当前加载的配置的哈希 ID，以避免对配置存储发出过多的调用。 在缓存值过期前，刷新操作不会更新该值，即使该值在配置存储中已发生更改。 每个请求的默认过期时间为 30 秒。 可以根据需要重写它。

`azure-spring-cloud-appconfiguration-config-web` 的自动刷新是基于活动（具体而言，是基于 Spring Web 的 `ServletRequestHandledEvent`）触发的。 如果 `ServletRequestHandledEvent` 未触发，则即使缓存过期时间已过，`azure-spring-cloud-appconfiguration-config-web` 的自动刷新也不会触发刷新。

## <a name="use-manual-refresh"></a>使用手动刷新

应用程序配置公开可用于检查缓存是否已过期以及刷新触发时限是否已过的 `AppConfigurationRefresh`。

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

如果已触发刷新，`AppConfigurationRefresh` 的 `refreshConfigurations()` 将返回值为 true 的 `Future`，否则返回值 false。 False 表示缓存过期时间未过、未发生更改，或者另一线程当前正在检查刷新。

## <a name="use-automated-refresh"></a>使用自动刷新

若要使用自动刷新，请从使用应用程序配置的 Spring Boot 应用（例如，按[适用于应用程序配置的 Spring Boot 快速入门](quickstart-java-spring-app.md)创建的应用）着手。

然后，在文本编辑器中打开 pom.xml 文件，并使用以下代码为 `azure-spring-cloud-appconfiguration-config-web` 添加 `<dependency>`。

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> 如果需要支持较旧的依赖项，请参阅[上一个库](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md)。

1. 更新 `bootstrap.properties` 以启用刷新

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. 打开 Azure 门户并导航到与应用程序关联的应用程序配置资源。 选择“操作”下的“配置资源管理器”，然后通过选择“+ 创建” > “键值”并添加以下参数以创建新的键值对。   

    | 密钥 | 值 |
    |---|---|
    | sentinel | 1 |

    暂时将“标签”和“内容类型”保留为空   。

1. 选择“应用”。

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 打开浏览器窗口，访问 URL：`http://localhost:8080`。  将显示与密钥关联的消息。

    还可以使用 curl 来测试应用程序，例如：

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 若要测试动态配置，请打开与应用程序关联的 Azure 应用程序配置门户。 选择“配置资源管理器”，并更新所显示的密钥的值，例如：

    | 键 | 值 |
    |---|---|
    | /application/config.message | Hello - Updated |

1. 将之前创建的 sentinel 密钥更新为新值。 此更改将在刷新间隔时间段过去后触发应用程序刷新所有配置密钥。

    | 密钥 | 值 |
    |---|---|
    | sentinel | 2 |

1. 刷新浏览器页面，查看显示的新消息。

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 Spring Boot 应用，可以通过应用程序配置动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
