---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d610e79773dabb9942352747f1f032b17730ffca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738746"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中设置过渡环境

本文适用于：✔️ Java

本文介绍如何使用 Azure 春季云中的蓝绿色部署模式设置过渡部署。 蓝绿色部署是一种 Azure DevOps 持续交付模式，它依赖于使现有 (蓝) 版本保持活动，同时部署新的 (绿色) 。 本文介绍了如何将此过渡部署放入生产环境中，而无需更改生产部署。

## <a name="prerequisites"></a>先决条件

* *标准***定价层** 上的 Azure 春季云实例。
* Azure CLI [Azure 春季云扩展](/cli/azure/azure-cli-extensions-overview)

本文使用从弹簧初始值设定项生成的应用程序。 如果要在本示例中使用其他应用程序，则需要对应用程序的面向公众的部分进行简单的更改，以便将过渡部署与生产区分开。

>[!TIP]
> Azure Cloud Shell 是一种免费的交互式 Shell，可以使用它运行本文中的说明。  它具有常见的预安装的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请开始 [Azure Cloud Shell](https://shell.azure.com)。  若要了解详细信息，请参阅 [Azure Cloud Shell 的概述](../cloud-shell/overview.md)。

若要在 Azure 春季云中设置蓝绿色部署，请按照后续部分中的说明进行操作。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>准备应用和部署
若要生成应用程序，请执行以下步骤：
1. 使用带有 [此配置](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client)的弹簧初始值设定项为示例应用生成代码。

2. 下载代码。
3. 将以下源文件 Hellocontroller.java 添加到该文件夹 `\src\main\java\com\example\hellospring\` 。
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. 生成 .jar 文件：
```azurecli
mvn clean packge -DskipTests
```
5. 在 Azure 春季 Cloud 实例中创建应用：
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
```
6. 将应用部署到 Azure 春季 Cloud：
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. 修改过渡部署的代码：
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. 重新生成 .jar 文件：
```azurecli
mvn clean packge -DskipTests
```
9. 创建绿色部署： 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>查看应用和部署

使用以下过程查看已部署的应用。

1. 在 Azure 门户中，请参阅 Azure 春季云实例。

1. 从左侧导航窗格中，打开 "应用" 边栏选项卡以查看服务实例的应用。

    [![应用-仪表板](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. 可以单击某个应用并查看详细信息。

    [![应用-概述](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. 打开 **部署** 以查看应用的所有部署。 该网格显示了生产部署和过渡部署。

    [![应用/部署仪表板](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. 单击 URL 以打开当前部署的应用程序。
    ![已部署 URL](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. 单击 "**状态**" 列中的 "**生产**" 查看默认应用。
    ![默认正在运行](media/spring-cloud-blue-green-staging/running-default-app.png)
1. 单击 "**状态**" 列中的 "**暂存**"，查看暂存应用。
    ![暂存正在运行](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * 确认测试终结点以斜线 (/) 结尾，从而确保正确加载 CSS 文件。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL 解码会返回格式为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green”的 URL。  请使用此格式来访问终结点。

>[!NOTE]    
> 配置服务器设置既应用于过渡环境，也应用于生产环境。 例如，如果在配置服务器中将应用网关的上下文路径 (`server.servlet.context-path`) 设置为 somepath，则绿色部署的路径将更改为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...”。
 
 如果此时访问面向公众的应用网关，应会看到没有新更改的旧页面。

## <a name="set-the-green-deployment-as-the-production-environment"></a>将绿色部署设置为生产环境

1. 在过渡环境中验证更改后，可将其推送到生产环境。 在 "**应用** / **部署**" 页上，选择当前在中的应用程序 `Production` 。

1. 单击绿色部署的 **注册状态** 后面的省略号，并将 "过渡生成" 设置为 "生产"。 

   [![将生产设置为过渡](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. 现在，应用程序的 URL 应显示你所做的更改。

   ![立即在部署中过渡](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> 将绿色部署设置为生产环境后，以前的部署将变成过渡部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果你对更改不满意，可修改应用程序代码，生成新的 jar 包，然后使用 Azure CLI 将其上传到绿色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>删除过渡部署

若要在 Azure 门户中删除过渡部署，请转到过渡部署页面，然后选择“删除”按钮。

或者，在 Azure CLI 中运行以下命令删除过渡部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>后续步骤

* [适用于 Azure Spring Cloud 的 CI/CD](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)