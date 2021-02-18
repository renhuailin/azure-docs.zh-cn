---
title: 在 Azure Red Hat OpenShift 4 群集上使用开放式自由/WebSphere 自由部署 Java 应用程序
description: 在 Azure Red Hat OpenShift 4 群集上使用开放式自由/WebSphere 自由部署 Java 应用程序。
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java、jakartaee、javaee、microprofile、开放式-自由、websphere-、aro、openshift、red hat
ms.openlocfilehash: acb448a9662daa58b5d2ff42861e238e23586b33
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633880"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上使用开放式自由/WebSphere 自由部署 Java 应用程序

本指南演示如何在开放的自由/WebSphere 自由运行时运行 Java、Java EE、 [雅加达 EE](https://jakarta.ee/)或 [MicroProfile](https://microprofile.io/) 应用程序，然后使用开放式自由运算符将容器化应用程序部署到 Azure RED Hat OpenShift (ARO) 4 群集。 本文介绍如何准备可自由应用的应用程序，构建应用程序 Docker 映像并在 ARO 4 群集上运行容器化应用程序。  有关开放自由的详细信息，请参阅 [打开自由项目页](https://openliberty.io/)。 有关 IBM WebSphere 的更多详细信息，请参阅 [WebSphere 自由产品页](https://www.ibm.com/cloud/websphere-liberty)。

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>先决条件

若要成功完成本指南，请完成以下先决条件。

> [!NOTE]
> Azure Red Hat OpenShift 至少需要 40 个核心才能创建和运行 OpenShift 群集。 新 Azure 订阅的默认 Azure 资源配额不满足此要求。 若要请求提高资源上限，请参阅[标准配额：按 VM 系列提高上限](../azure-portal/supportability/per-vm-quota-requests.md)中所述。 请注意，免费试用订阅不适用于配额增加，请在请求增加配额之前 [升级到即用即付订阅](../cost-management-billing/manage/upgrade-azure-subscription.md) 。

1. 使用安装了类似于 Unix 的操作系统 (例如，Ubuntu、macOS) 准备本地计算机。
1. 安装 Java SE 实现 (例如， [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) 。
1. 安装 [Maven](https://maven.apache.org/download.cgi) 3.5.0 或更高版本。
1. 安装适用于你的操作系统的 [Docker](https://docs.docker.com/get-docker/) 。
1. 安装 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) 2.0.75 或更高版本。
1. [`envsubst`](https://command-not-found.com/envsubst)如果未在操作系统中预先安装，请检查并安装。
1. 在本地系统上克隆此示例的代码。 该示例位于 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro)上。
1. 按照 [创建 Azure Red Hat OpenShift 4 群集](./tutorial-create-cluster.md)中的说明进行操作。

   尽管 "获取 Red Hat 请求机密" 步骤标记为可选，但 **本文仍需要此** 步骤。  "获取密钥" 允许 Azure Red Hat OpenShift 群集查找开放式自由运算符。

   如果打算在群集上运行内存密集型应用程序，请使用参数为辅助角色节点指定适当的虚拟机大小 `--worker-vm-size` 。 例如， `Standard_E4s_v3` 是在群集上安装 Elasticsearch 操作员的最小虚拟机大小。 有关详细信息，请参阅：

   * [Azure CLI 创建群集](/cli/azure/aro?preserve-view=true&view=azure-cli-latest#az-aro-create)
   * [内存优化支持的虚拟机大小](./support-policies-v4.md#memory-optimized)
   * [安装 Elasticsearch 运算符的先决条件](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. 按照 [连接到 Azure Red Hat OpenShift 4 群集](./tutorial-connect-cluster.md)中的步骤连接到群集。
   * 请务必遵循 "安装 OpenShift CLI" 中的步骤，因为我们将 `oc` 在本文的后面部分使用该命令。
   * 写下群集控制台 URL，如所示 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 。
   * 记下 `kubeadmin` 凭据。

1. 验证是否可以通过用户的令牌登录到 OpenShift CLI `kubeadmin` 。

### <a name="enable-the-built-in-container-registry-for-openshift"></a>为 OpenShift 启用内置容器注册表

本教程中的步骤创建一个 Docker 映像，该映像必须推送到 OpenShift 可访问的容器注册表。 最简单的方法是使用 OpenShift 提供的内置注册表。 若要启用内置容器注册表，请按照为 [Azure Red Hat OpenShift 4 配置内置容器注册表](built-in-container-registry.md)中的步骤操作。 本文使用了这些步骤中的三个项目。

* 登录 OpenShift web 控制台 Azure AD 用户的用户名和密码。
* `oc whoami`执行登录到 OPENSHIFT CLI 的步骤后的输出。  此值称为 " **aad-用户** 讨论"。
* 容器注册表 URL。

完成启用内置容器注册表的步骤时，请注意以下各项。

### <a name="create-an-openshift-namespace-for-the-java-app"></a>创建 Java 应用的 OpenShift 命名空间

1. 使用凭据登录到浏览器中的 OpenShift web 控制台 `kubeadmin` 。
2. 导航到 "**管理**  >  **命名空间**" "  >  **创建命名空间**"。
3. 填写 `open-liberty-demo` **名称** 并选择 " **创建**"，如下所示。

   ![创建命名空间](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>创建演示项目的管理员

除了映像管理以外，还将向 **aad 用户** 授予管理权限，以便在 ARO 4 群集的演示项目中管理资源。  登录到 OpenShift CLI，并按照以下步骤向 **aad 用户** 授予必要的权限。

1. 使用凭据登录到浏览器中的 OpenShift web 控制台 `kubeadmin` 。
1. 在 web 控制台的右上角，展开已登录用户的上下文菜单，然后选择 " **复制登录命令**"。
1. 如果需要，请使用相同的用户登录到新的选项卡窗口。
1. 选择 " **显示令牌**"。
1. 将以下 **使用此令牌登录** 的值复制到剪贴板并在 shell 中运行，如下所示。
1. 执行以下命令，将 `admin` 角色授予命名空间中的 **aad 用户** `open-liberty-demo` 。

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>安装开放式自由 OpenShift 运算符

创建并连接到群集后，请安装开放式自由运算符。  开放式自由运算符的主要起始页位于 [GitHub](https://github.com/OpenLiberty/open-liberty-operator)上。

1. 使用凭据登录到浏览器中的 OpenShift web 控制台 `kubeadmin` 。
2. 导航到 "**运算符**  >  **OperatorHub** " 并搜索 "**开放式自由运算符**"。
3. 从搜索结果中选择 " **打开自由运算符** "。
4. 选择“安装”  。
5. 在 popup **Create Operator 订阅** 中，检查 **群集上的所有命名空间 (默认)** **安装模式**、 **Beta 版****更新通道** 和 **自动****批准策略**：

   ![为开放式自由运算符创建操作员订阅](./media/howto-deploy-java-liberty-app/install-operator.png)
6. 选择 " **订阅** " 并等待一分钟或两分钟，直到显示 "开放式自由运算符"。
7. 观察 "开放式自由" 运算符，状态为 "成功"。  如果不这样做，请诊断并解决问题，然后继续。
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="安装了显示 &quot;开放自由&quot; 的已安装操作员。":::

## <a name="prepare-the-liberty-application"></a>准备自由应用程序

在本指南中，我们将使用 Java EE 8 应用程序作为示例。 开放式自由是 [JAVA EE 8 完全配置文件](https://javaee.github.io/javaee-spec/javadocs/) 兼容的服务器，因此它可以轻松地运行应用程序。  开放式自由也是 [雅加达 EE 8 完全配置文件兼容](https://jakarta.ee/specifications/platform/8/apidocs/)。

### <a name="run-the-application-on-open-liberty"></a>在开放自由上运行应用程序

若要在开放自由上运行应用程序，需要创建一个开放的自由服务器配置文件，以便 " [自由 Maven" 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) 可以打包应用程序进行部署。 将应用程序部署到 OpenShift 不需要自由 Maven 插件。  但在此示例中，我们将使用开放式的开发人员 (开发) 模式。  开发人员模式允许在本地运行应用程序。 在本地计算机上完成以下步骤。

1. 复制 `2-simple/src/main/liberty/config/server.xml` 到 `1-start/src/main/liberty/config` ，覆盖现有的长度为零的文件。 这 `server.xml` 将为开放式的自由服务器配置 JAVA EE 功能。
1. 将复制 `2-simple/pom.xml` 到 `1-start/pom.xml` 。  此步骤将添加 `liberty-maven-plugin` 到 POM。
1. 将目录更改为 `1-start` 你的本地副本。
1. `mvn clean package`在控制台中运行，以 `javaee-cafe.war` 在目录中生成 war 包 `./target` 。
1. 运行 `mvn liberty:dev` 以在开发模式下启动开放。
1. 等待服务器启动。 控制台输出应以以下消息结束：

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. `http://localhost:9080/`在浏览器中打开以访问应用程序主页。 该应用程序将类似于下图：

   ![JavaEE 咖啡馆 Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. 按 **ctrl-c** 停止应用程序并打开 "自由服务器"。

`2-simple`本地克隆的目录显示了已应用上述更改的 Maven 项目。

## <a name="prepare-the-application-image"></a>准备应用程序映像

若要在 ARO 4 群集上部署并运行你的自由应用程序，请使用 [开放式自由容器映像](https://github.com/OpenLiberty/ci.docker) 或 [WebSphere 自由容器映像](https://github.com/WASdev/ci.docker)，将应用程序容器化为 Docker 映像。

### <a name="build-application-image"></a>构建应用程序映像

完成以下步骤以生成应用程序映像：

1. 将目录更改为 `2-simple` 你的本地副本。
2. 运行 `mvn clean package` 将应用程序打包。
3. 运行以下命令之一来生成应用程序映像。
   * 采用开放的自由基映像生成：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * 用 WebSphere 的 "自由" 基本映像生成：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>通过 Docker 在本地运行应用程序

将容器化应用程序部署到远程群集之前，请使用本地 Docker 运行，验证其是否正常工作：

1. `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`在控制台中运行。
2. 等待 "自由服务器启动" 并且应用程序成功部署。
3. `http://localhost:9080/`在浏览器中打开以访问应用程序主页。
4. 按 **ctrl-c** 停止应用程序和自由服务器。

### <a name="push-the-image-to-the-container-image-registry"></a>将映像推送到容器映像注册表

当你对应用程序的状态感到满意后，请按照以下说明将其推送到内置容器映像注册表。

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>以 Azure AD 用户身份登录到 OpenShift CLI

1. 使用 Azure AD 用户的凭据，从浏览器登录到 OpenShift web 控制台。

   1. 使用 InPrivate、Incognito 或其他等效的浏览器窗口功能登录到控制台。
   1. 选择 **openid**

   > [!NOTE]
   > 记下用于登录的用户名和密码。 此用户名和密码将充当此文章和其他文章中其他操作的管理员。
1. 使用以下步骤登录到 OpenShift CLI。  对于讨论，此过程称为 `oc login` 。
   1. 在 web 控制台的右上角，展开已登录用户的上下文菜单，然后选择 " **复制登录命令**"。
   1. 如果需要，请使用相同的用户登录到新的选项卡窗口。
   1. 选择 " **显示令牌**"。
   1. 将以下 **使用此令牌登录** 的值复制到剪贴板并在 shell 中运行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>将容器映像推送到 OpenShift 的容器注册表

执行以下命令，将映像推送到 OpenShift 的容器注册表。

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

成功的输出将如下所示。

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

用以下命令将映像推送到内置容器映像注册表。

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>在 ARO 4 群集上部署应用程序

现在，你可以将示例自由应用程序部署到你之前在处理必备组件时创建的 Azure Red Hat OpenShift 4 群集。

### <a name="deploy-the-application-from-the-web-console"></a>从 web 控制台部署应用程序

由于我们使用开放式自由运算符来管理自由应用程序，因此我们需要创建其 *自定义资源定义* 的实例，类型为 "OpenLibertyApplication"。 然后，该操作员将负责管理部署所需的 OpenShift 资源的所有方面。

1. 使用 Azure AD 用户的凭据，从浏览器登录到 OpenShift web 控制台。
1. 展开 "**主页**"，选择 "**项目**" "  >  **自由-自由演示**"。
1. 导航到 **操作员**  >  **安装的操作员**。
1. 在页面中间，选择 " **开放式自由运算符**"。
1. 在页面的中间，选择 " **打开自由应用程序**"。  用户界面中的项导航反映了所使用的技术的实际包含层次结构。
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java 包含](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. 选择 **创建 OpenLibertyApplication**
1. 将生成的 yaml 替换为您自己的，该位置位于 `<path-to-repo>/2-simple/openlibertyapplication.yaml` 。
1. 选择“创建”。 你将返回到 OpenLibertyApplications 的列表。
1. 选择 **javaee-简单**。
1. 在页面的中间，选择 " **资源**"。
1. 在表中，选择 **javaee** 的链接，其中包含 **路由****类型**。
1. 在打开的页面上，选择以下链接 **位置**。

你将看到在浏览器中打开的应用程序主页。

### <a name="delete-the-application-from-the-web-console"></a>从 web 控制台删除应用程序

完成应用程序后，请按照以下步骤操作，从 Open Shift 删除应用程序。

1. 在左侧导航窗格中，展开 " **运算符**" 条目。
1. 选择 **已安装的操作员**。
1. 选择 " **开放式自由运算符**"。
1. 在页面的中间选择 " **打开自由应用程序**"。
1. 选择 (三个垂直点) 的垂直省略号，然后选择 " **删除 OpenLiberty 应用程序**"。

### <a name="deploy-the-application-from-cli"></a>从 CLI 部署应用程序

你可以从 CLI 部署应用程序，而不是使用 web 控制台 GUI。 如果尚未执行此操作，请 `oc` 通过 [在 CLI 中使用](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)Red Hat 文档入门下载并安装命令行工具。

1. 使用 Azure AD 用户的凭据，从浏览器登录到 OpenShift web 控制台。
2. 以 Azure AD 用户的令牌登录到 OpenShift CLI。
3. 将目录更改为 `2-simple` 本地克隆，并运行以下命令，将你的自由应用程序部署到 ARO 4 群集。  命令输出也显示为内联。

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. 继续之前，请检查 `1/1` 列下的 `READY` 。  否则，请在继续操作之前调查并解决问题。
5. 通过命令发现应用程序的路由主机 `oc get route` ，如下所示。

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   在自由应用启动并运行后，在浏览器中打开 **路由主机** 的输出，以访问应用程序主页。

### <a name="delete-the-application-from-cli"></a>从 CLI 中删除应用程序

通过执行以下命令从 CLI 中删除应用程序。

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>清理资源

按照[教程：删除 Azure Red Hat OpenShift 4 群集](./tutorial-delete-cluster.md)中的步骤操作，删除 ARO 群集

## <a name="next-steps"></a>后续步骤

本指南介绍了如何：
> [!div class="checklist"]
>
> * 准备自由应用程序
> * 构建应用程序映像
> * 使用 GUI 和 CLI 在 ARO 4 群集上运行容器化应用程序

可以通过本指南中使用的参考了解详细信息：

* [自由打开](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [开放式自由运算符](https://github.com/OpenLiberty/open-liberty-operator)
* [打开自由服务器配置](https://openliberty.io/docs/ref/config/)
* [自由 Maven 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [打开自由容器映像](https://github.com/OpenLiberty/ci.docker)
* [WebSphere 自由容器映像](https://github.com/WASdev/ci.docker)