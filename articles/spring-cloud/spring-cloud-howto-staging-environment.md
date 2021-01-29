---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053042"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中设置过渡环境

本文适用于：✔️ Java

本文介绍如何使用 Azure Spring Cloud 中的蓝绿色部署模式来设置过渡部署。 蓝/绿部署是一种 Azure DevOps 持续交付模式，它依赖于在部署新（绿色）版本时保持现有（蓝色）版本的活动性。 本文还会介绍如何在不直接更改生产部署的情况下，将此过渡部署投入生产。

## <a name="prerequisites"></a>先决条件

* 正在运行的应用程序。  请参阅 [快速入门：部署第一个 Azure 春季云应用程序](spring-cloud-quickstart.md)。
* Azure CLI [asc 扩展](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

如果要对本示例使用其他应用程序，需要在该应用程序面向公众的部分进行一项简单的更改。  此项更改会将过渡部署与生产部署区分开来。

>[!TIP]
> Azure Cloud Shell 是一种免费的交互式 Shell，可以使用它运行本文中的说明。  它具有常见的预安装的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请开始 [Azure Cloud Shell](https://shell.azure.com)。  若要了解详细信息，请参阅 [Azure Cloud Shell 的概述](../cloud-shell/overview.md)。

若要在 Azure Spring Cloud 中设置过渡环境，请按照后续部分中的说明进行操作。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>查看应用和部署

使用以下过程查看已部署的应用。

1. 在 Azure 门户中，请参阅 Azure 春季云实例。

1. 从左侧导航窗格中，打开 " **部署**"。

    [![部署-弃用](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. 打开 "应用" 边栏选项卡，查看服务实例的应用。

    [![应用-仪表板](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. 可以单击某个应用并查看详细信息。

    [![应用-概述](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. 打开 " **部署** " 边栏选项卡，查看应用的所有部署。 部署网格显示部署是生产还是过渡。

    [![部署面板](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. 你可以单击该部署的名称以查看部署概述。 在这种情况下，唯一的部署命名为 " *默认*"。

    [![部署概述](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对应用程序进行少许修改。 这样便可轻松区分这两个部署。 若要生成 jar 包，请运行以下命令： 

    ```console
    mvn clean package -DskipTests
    ```

1. 在 Azure CLI 中创建新的部署，并指定过渡部署名称“绿色”。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. CLI 部署成功完成后，从 **应用程序仪表板** 访问应用页面，并在左侧的 " **部署** " 选项卡中查看所有实例。

   [![部署绿色后的部署仪表板](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> 发现状态为“OUT_OF_SERVICE”，因此在验证完成之前，流量不会路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

验证绿色过渡发展是否正常工作：
1. 中转到 " **部署** "，然后单击 " `green` **过渡部署**"。
1. 在 " **概述** " 页上，单击 **测试终结点**。
1. 这会打开显示更改的过渡生成。

>[!TIP]
> * 确认测试终结点以斜线 (/) 结尾，从而确保正确加载 CSS 文件。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL 解码会返回格式为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green”的 URL。  请使用此格式来访问终结点。

>[!NOTE]    
> 配置服务器设置既应用于过渡环境，也应用于生产环境。 例如，如果在配置服务器中将应用网关的上下文路径 (`server.servlet.context-path`) 设置为 somepath，则绿色部署的路径将更改为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...”。
 
 如果此时访问面向公众的应用网关，应会看到没有新更改的旧页面。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>将绿色部署设置为生产环境

1. 在过渡环境中验证更改后，可将其推送到生产环境。 返回到 " **部署管理**"，并选择当前在中的应用程序 `Production` 。

1. 单击 **注册状态** 后面的省略号，然后将 "生产内部版本" 设置为 `staging` 。

   [![部署设置过渡部署](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. 返回到 " **部署" 管理** 页。  `green`部署部署状态应显示为 "已 *启动*"。 这现在就是正在运行的生产版本。

   [![部署设置过渡部署结果](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. 将 URL 复制并粘贴到新的浏览器窗口中，新的应用程序页应显示更改。

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