---
title: 在 Azure Red Hat OpenShift 4 群集上使用 Open Liberty/WebSphere Liberty 部署 Java 应用程序
description: 在 Azure Red Hat OpenShift 4 群集上使用 Open Liberty/WebSphere Liberty 部署 Java 应用程序。
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.custom:
- devx-track-java
- devx-track-javaee
ms.openlocfilehash: 016cc443ac88dce22d8320ceadc3075d4802bdc5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360087"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上使用 Open Liberty/WebSphere Liberty 部署 Java 应用程序

本指南演示如何在 Open Liberty/WebSphere Liberty 运行时中运行 Java、Java EE、[Jakarta EE](https://jakarta.ee/) 或 [MicroProfile](https://microprofile.io/) 应用程序，然后使用 Open Liberty Operator 将容器化应用程序部署到 Azure RED Hat OpenShift (ARO) 4 群集。 本文介绍如何准备 Liberty 应用程序、生成应用程序 Docker 映像并在 ARO 4 群集上运行容器化应用程序。  有关 Open Liberty 的详细信息，请参阅 [Open Liberty 项目页](https://openliberty.io/)。 有关 IBM WebSphere Liberty 的详细信息，请参阅 [WebSphere Liberty 产品页](https://www.ibm.com/cloud/websphere-liberty)。

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>先决条件

若要成功完成本指南，请满足以下先决条件。

> [!NOTE]
> Azure Red Hat OpenShift 至少需要 40 个核心才能创建和运行 OpenShift 群集。 新 Azure 订阅的默认 Azure 资源配额不满足此要求。 若要请求提高资源上限，请参阅[标准配额：按 VM 系列提高上限](../azure-portal/supportability/per-vm-quota-requests.md)中所述。 请注意，免费试用订阅无法增加配额，请[升级到即用即付订阅](../cost-management-billing/manage/upgrade-azure-subscription.md)再请求增加配额。

1. 准备安装了 Unix 之类操作系统（如 Ubuntu、macOS）的本地计算机。
1. 安装 Java SE 实现（例如 [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)）。
1. 安装 [Maven](https://maven.apache.org/download.cgi) 3.5.0 或更高版本。
1. 安装适用于 OS 的 [Docker](https://docs.docker.com/get-docker/)。
1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 或更高版本。
1. 如果未在操作系统中预安装 [`envsubst`](https://command-not-found.com/envsubst)，请检查并安装它。
1. 在本地系统上克隆此示例的代码。 该示例位于 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro) 上。
1. 按照[创建 Azure Red Hat OpenShift 4 群集](./tutorial-create-cluster.md)中的说明进行操作。

   尽管“获取 Red Hat 请求机密”这一步标记为可选步骤，但本文仍需要此步骤。  请求机密允许 Azure Red Hat OpenShift 群集查找 Open Liberty Operator。

   如果你打算在群集上运行内存密集型应用程序，请使用 `--worker-vm-size` 参数为工作器节点指定适当的虚拟机大小。 例如，`Standard_E4s_v3` 是在群集上安装 Elasticsearch Operator 的最小虚拟机大小。 有关详细信息，请参阅：

   * [使用 Azure CLI 创建群集](/cli/azure/aro#az_aro_create)
   * [内存优化支持的虚拟机大小](./support-policies-v4.md#memory-optimized)
   * [安装 Elasticsearch Operator 的先决条件](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. 按照[连接到 Azure Red Hat OpenShift 4 群集](./tutorial-connect-cluster.md)中的步骤连接到该群集。
   * 请务必遵循“安装 OpenShift CLI”中的步骤，因为我们将在后文使用 `oc` 命令。
   * 记下类似于 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 的群集控制台 URL。
   * 记下 `kubeadmin` 凭据。

1. 验证是否可以通过用户的令牌 `kubeadmin` 登录到 OpenShift CLI。

### <a name="enable-the-built-in-container-registry-for-openshift"></a>为 OpenShift 启用内置容器注册表

本教程中的步骤创建 Docker 映像，该映像必须推送到 OpenShift 可访问的容器注册表。 最简单的选项是使用 OpenShift 提供的内置注册表。 若要启用内置容器注册表，请按照[为 Azure Red Hat OpenShift 4 配置内置容器注册表](built-in-container-registry.md)中的步骤操作。 本文用到了这些步骤中的三项内容。

* 用于登录 OpenShift Web 控制台的 Azure AD 用户的用户名和密码。
* 执行登录到 OPENSHIFT CLI 的步骤后 `oc whoami` 的输出。  将此值称为 aad-user 以便讨论。
* 容器注册表 URL。

完成启用内置容器注册表的步骤时，请记下这些内容。

### <a name="create-an-openshift-namespace-for-the-java-app"></a>创建 Java 应用的 OpenShift 命名空间

1. 使用 `kubeadmin` 凭据从浏览器登录到 OpenShift Web 控制台。
2. 导航到“管理” > “命名空间” > “创建命名空间”  。
3. 在“名称”中填写 `open-liberty-demo` 并选择“创建”，如下所示 。

   ![创建命名空间](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>创建演示项目的管理员

除了管理映像以外，还向 aad-user 授予管理 ARO 4 群集的演示项目中资源的管理权限。  登录到 OpenShift CLI，并按照以下步骤向 aad-user 授予必要的权限。

1. 使用 `kubeadmin` 凭据从浏览器登录到 OpenShift Web 控制台。
1. 在 Web 控制台的右上角，展开已登录用户的上下文菜单，然后选择“复制登录命令”。
1. 如有必要，请使用同一用户登录到新选项卡窗口。
1. 选择“显示令牌”。
1. 将“使用此令牌登录”下方的值复制到剪贴板并在 shell 中运行，如下所示。
1. 执行以下命令，将 `admin` 角色授予命名空间 `open-liberty-demo` 中的 aad-user。

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>安装 Open Liberty OpenShift Operator

创建并连接到群集后，请安装 Open Liberty Operator。  Open Liberty Operator 的主要起始页位于 [GitHub](https://github.com/OpenLiberty/open-liberty-operator) 上。

1. 使用 `kubeadmin` 凭据从浏览器登录到 OpenShift Web 控制台。
2. 导航到“Operator” > “OperatorHub”并搜索“Open Liberty Operator”  。
3. 从搜索结果中选择“Open Liberty Operator”。
4. 选择“安装”  。
5. 在弹出的“创建 Operator 订阅”中，选中“群集上的所有命名空间(默认)”作为“安装模式”、“Beta”作为“更新通道”、“自动”作为“批准策略”      ：

   ![为 Open Liberty Operator 创建 Operator 订阅](./media/howto-deploy-java-liberty-app/install-operator.png)
6. 选择“订阅”并等待一两分钟，直到 Open Liberty Operator 出现。
7. 留意 Open Liberty Operator 的状态是否为“成功”。  如果不是，请排查并解决该问题，之后再继续。
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="显示已安装 Open Liberty 的“安装的 Operator”。":::

## <a name="prepare-the-liberty-application"></a>准备 Liberty 应用程序

本指南使用 Java EE 8 应用程序作为示例。 Open Liberty 是兼容 [Java EE 8 完整配置文件](https://javaee.github.io/javaee-spec/javadocs/)的服务器，因此它可以轻松地运行该应用程序。  Open Liberty 也[兼容 Jakarta EE 8 完整配置文件](https://jakarta.ee/specifications/platform/8/apidocs/)。

### <a name="run-the-application-on-open-liberty"></a>在 Open Liberty 上运行应用程序

若要在 Open Liberty 上运行应用程序，需要创建 Open Liberty 服务器配置文件，以便 [Liberty Maven 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)可以打包应用程序进行部署。 将应用程序部署到 OpenShift 不需要 Liberty Maven 插件。  但在此示例中，我们会将它用于 Open Liberty 的开发人员 (dev) 模式。  开发人员模式允许在本地轻松运行应用程序。 在本地计算机上完成以下步骤。

1. 将 `2-simple/src/main/liberty/config/server.xml` 复制到 `1-start/src/main/liberty/config`，覆盖现有的长度为零的文件。 此 `server.xml` 将为 Open Liberty 服务器配置 Java EE 功能。
1. 将 `2-simple/pom.xml` 复制到 `1-start/pom.xml`。  此步骤将 `liberty-maven-plugin` 添加到 POM。
1. 将目录更改为本地克隆的 `1-start`。
1. 在控制台中运行 `mvn clean package`，以在目录 `./target` 中生成 war 包 `javaee-cafe.war`。
1. 运行 `mvn liberty:dev` 以在 dev 模式下启动 Open Liberty。
1. 等待服务器启动。 控制台输出应以以下消息结尾：

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. 在浏览器中打开 `http://localhost:9080/` 以访问应用程序主页。 应用程序类似于下图：

   ![JavaEE Cafe Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. 按 Control-C 以停止应用程序和 Open Liberty 服务器。

本地克隆的目录 `2-simple` 显示应用了上述更改的 Maven 项目。

## <a name="prepare-the-application-image"></a>准备应用程序映像

若要在 ARO 4 群集上部署并运行 Liberty 应用程序，请使用 [Open Liberty 容器映像](https://github.com/OpenLiberty/ci.docker)或 [WebSphere Liberty 容器映像](https://github.com/WASdev/ci.docker)将应用程序容器化为 Docker 映像。

### <a name="build-application-image"></a>生成应用程序映像

完成以下步骤以生成应用程序映像：

1. 将目录更改为本地克隆的 `2-simple`。
2. 运行 `mvn clean package` 来打包应用程序。
3. 运行以下命令之一来生成应用程序映像。
   * 使用 Open Liberty 基础映像进行生成：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * 使用 WebSphere Liberty 基础映像进行生成：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>使用 Docker 在本地运行应用程序

将容器化应用程序部署到远程群集之前，请使用本地 Docker 运行它以验证它是否正常工作：

1. 在控制台中运行 `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`。
2. 等待 Liberty 服务器启动以及应用程序成功部署。
3. 在浏览器中打开 `http://localhost:9080/` 以访问应用程序主页。
4. 按 Control-C 以停止应用程序和 Liberty 服务器。

### <a name="push-the-image-to-the-container-image-registry"></a>将映像推送到容器映像注册表

当对应用程序的状态感到满意后，请按照以下说明将其推送到内置容器映像注册表。

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>以 Azure AD 用户身份登录到 OpenShift CLI

1. 使用 Azure AD 用户的凭据从浏览器登录到 OpenShift Web 控制台。

   1. 使用 InPrivate、Incognito 或其他等效浏览器窗口功能来登录到该控制台。
   1. 选择“openid”

   > [!NOTE]
   > 记下用于在此登录的用户名和密码。 此用户名和密码将充当本文及其他文章中其他操作的管理员。
1. 使用以下步骤登录到 OpenShift CLI。  为便于讨论，将此过程称为 `oc login`。
   1. 在 Web 控制台的右上角，展开已登录用户的上下文菜单，然后选择“复制登录命令”。
   1. 如有必要，请使用同一用户登录到新选项卡窗口。
   1. 选择“显示令牌”。
   1. 将“使用此令牌登录”下方的值复制到剪贴板并在 shell 中运行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>将容器映像推送到 OpenShift 的容器注册表

执行以下命令将映像推送到 OpenShift 的容器注册表。

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

成功的输出与以下内容类似。

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

使用以下命令将映像推送到内置容器映像注册表。

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>在 ARO 4 群集上部署应用程序

现在可以将示例 Liberty 应用程序部署到之前在处理先决条件时创建的 Azure Red Hat OpenShift 4 群集。

### <a name="deploy-the-application-from-the-web-console"></a>从 Web 控制台部署应用程序

因为我们使用 Open Liberty Operator 来管理 Liberty 应用程序，因此需要创建其自定义资源定义的实例，其类型为“OpenLibertyApplication”。 然后 Operator 将负责管理部署所需的 OpenShift 资源的方方面面。

1. 使用 Azure AD 用户的凭据从浏览器登录到 OpenShift Web 控制台。
1. 展开“主页”并选择“项目” > “open-liberty-demo”  。
1. 导航到“Operator” > “安装的 Operator” 。
1. 在页面中间选择“Open Liberty Operator”。
1. 在页面中间选择“Open Liberty 应用程序”。  用户界面中的项目导航反映了所使用的技术的实际包含层次。
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java 包含层次](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. 选择“创建 OpenLibertyApplication”
1. 将生成的 yaml 替换为自己的，它位于 `<path-to-repo>/2-simple/openlibertyapplication.yaml`。
1. 选择“创建”。 你将返回到 OpenLibertyApplications 的列表。
1. 选择“javaee-cafe-simple”。
1. 在页面中间选择“资源”。
1. 在表中选择“javaee-cafe-simple”的链接，其“种类”为“路由”  。
1. 在打开的页面上选择“位置”下方的链接。

将看到在浏览器中打开的应用程序主页。

### <a name="delete-the-application-from-the-web-console"></a>从 Web 控制台删除应用程序

完成该应用程序后，请按照以下步骤从 Open Shift 删除它。

1. 在左侧导航窗格中展开“Operator”的条目。
1. 选择“安装的 Operator”。
1. 选择“Open Liberty Operator”。
1. 在页面中间选择“Open Liberty 应用程序”。
1. 选择垂直省略号（三个垂直点），然后选择“删除 OpenLiberty 应用程序”。

### <a name="deploy-the-application-from-cli"></a>从 CLI 部署应用程序

可以从 CLI 部署应用程序，而不是使用 Web 控制台 GUI。 如果尚未执行此操作，请按照 Red Hat 文档 [CLI 入门](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)中的介绍来下载并安装 `oc` 命令行工具。

1. 使用 Azure AD 用户的凭据从浏览器登录到 OpenShift Web 控制台。
2. 使用 Azure AD 用户的令牌登录到 OpenShift CLI。
3. 将目录更改为本地克隆的 `2-simple`，并运行以下命令将 Liberty 应用程序部署到 ARO 4 群集。  命令输出也以内联方式显示。

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

4. 在继续之前检查 `READY` 列下方是否是 `1/1`。  如果不是，请排查并解决该问题，之后再继续。
5. 使用 `oc get route` 命令来发现应用程序的路由主机，如下所示。

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Liberty 应用程序启动并运行后，在浏览器中打开 Route Host 的输出以访问应用程序主页。

### <a name="delete-the-application-from-cli"></a>从 CLI 删除应用程序

通过执行以下命令从 CLI 删除应用程序。

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>清理资源

按照[教程：删除 Azure Red Hat OpenShift 4 群集](./tutorial-delete-cluster.md)中的步骤删除 ARO 群集

## <a name="next-steps"></a>后续步骤

在本指南中，你了解了以下内容：
> [!div class="checklist"]
>
> * 准备 Liberty 应用程序
> * 生成应用程序映像
> * 使用 GUI 和 CLI 在 ARO 4 群集上运行容器化应用程序

可以通过本指南中使用的参考了解详细信息：

* [Open Liberty](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty 服务器配置](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty 容器映像](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 容器映像](https://github.com/WASdev/ci.docker)