---
title: 使用 Azure Spring Boot Actuator 管理和监视应用
description: 了解如何使用 Spring Boot Actuator 管理和监视应用。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: d9c24868fa847ef3b8be13ca0d7775e24b3116cc
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015549"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>使用 Azure Spring Boot Actuator 管理和监视应用

本文适用于：✔️ Java

在将新的二进制文件部署到应用后，可能需要检查功能并查看有关正在运行的应用程序的信息。 本文介绍了如何从 Azure Spring Cloud 提供的测试终结点访问 API，并公开了用于应用的生产就绪功能。

## <a name="prerequisites"></a>先决条件

本文假定你有一个可以在 Azure Spring Cloud 服务上成功部署和启动的 Spring Boot 2.x 应用程序。  请参阅[快速入门：使用 Azure 门户启动现有的 Azure Spring Cloud 应用程序](./quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>通过测试终结点验证应用

1. 请转到“应用程序仪表板”并选择你的应用，以进入应用概述页。

1. 在“概述”窗格中，应该会看到“测试终结点” 。  从命令行或浏览器访问此终结点，并观察 API 响应。

1. 请记录将会在后面部分中用到的测试终结点 URI。

>[!TIP]
> * 如果该应用返回前端页并通过相对路径引用其他文件，请确认你的测试终结点是以斜杠 (/) 结尾的。 这将确保正确加载 CSS 文件。
> * 如果你从浏览器中查看 API，并且你的浏览器要求你在查看该页之前输入登录凭据，请使用 [URL 解码](https://www.urldecoder.org/)对测试终结点进行解码。 URL 解码会返回格式为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/\<app-name>/\<deployment-name>”的 URL。  请使用这种格式来访问终结点。

## <a name="add-actuator-dependency"></a>添加执行器依赖项

若要将执行器添加到基于 Maven 的项目，请添加“Starter”依赖项：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

请编译这个新的二进制文件并将它部署到你的应用。

## <a name="enable-production-ready-features"></a>启用生产就绪功能

利用执行器终结点，可以监视应用程序并与之进行交互。 默认情况下，Spring Boot 应用程序公开 `health` 和 `info` 终结点，以显示任意应用程序信息和运行状况信息。

若要观察配置以及可配置的环境，还需要启用 `env` 和 `configgrops` 终结点。

1. 转到应用“概述”窗格，选择“设置”菜单中的“配置”，转到“环境变量”配置页。  
1. 采用类似于“key:value”的形式添加以下属性。 此环境将打开 Spring Actuator 终结点“env”、“health”、“info”。

   ```properties
   management.endpoints.web.exposure.include: env,health,info
   ```

1. 选择“保存”按钮，应用程序将自动重启并加载新环境变量。

现在可以返回到应用概述窗格，并等待“预配状态”更改为“成功”。  运行的实例将会有多个。

> [!Note]
> 在将应用公开后，这些执行器终结点也会公开。 可通过删除环境变量 `management.endpoints.web.exposure.include` 来隐藏所有的终结点，并设置 `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>通过查看执行器终结点来查看应用程序信息

1. 你现在可以访问 URL `"<test-endpoint>/actuator/"` 来查看 Spring Boot Actuator 公开的所有终结点。
1. 访问 URL `"<test-endpoint>/actuator/env"`，可以看到应用使用的活动配置文件，以及所有加载的环境变量。
1. 如果需要搜索某个特定环境，可访问 URL `"<test-endpoint>/actuator/env/{toMatch}"` 来查看该环境。

若要查看所有内置的终结点，请参阅[公开终结点](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>后续步骤

* [了解用于 Azure Spring Cloud 的指标](./concept-metrics.md)
* [了解 Azure Spring Cloud 中的应用状态](./concept-app-status.md)
