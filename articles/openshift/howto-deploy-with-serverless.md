---
title: 使用 OpenShift Serverless 将应用程序部署到 Azure Red Hat OpenShift
description: 了解如何使用 OpenShift Serverless 将应用程序部署到 Azure Red Hat OpenShift
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverless
ms.openlocfilehash: 0cd6ac8ae31c43bf1fe1be1d42ec27f183512442
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110063619"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>使用 OpenShift Serverless 将应用程序部署到 Azure Red Hat OpenShift

在本文中，你会使用 [OpenShift Serverless](https://www.openshift.com/learn/topics/serverless) 将应用程序部署到 Azure Red Hat OpenShift 群集。 OpenShift Serverless 可帮助开发人员部署和运行应用程序，这些应用程序会将按需纵向扩展或缩减到零，从而消除不使用的资源消耗。

应用程序代码可与适当的运行时一起打包到容器中，当事件触发应用程序容器时，无服务器功能会启动这些容器。 应用程序可由各种事件源触发，例如来自自己应用程序的事件、来自多个提供商的云服务、软件即服务 (SaaS) 系统和其他服务。

以无服务器方式部署任何容器的所有方面的管理直接内置在 OpenShift 接口。 开发人员可以直观地识别推动其容器化应用程序启动的事件，有多种方法可用于修改事件参数。 OpenShift Serverless 应用程序可与其他 OpenShift 服务（如 OpenShift 管道、服务网格和监视）集成，从而提供完整的无服务器应用程序开发和部署体验。

## <a name="before-you-begin"></a>在开始之前

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>安装 Knative 命令行工具 (kn)

可从 <https://github.com/knative/client/releases/> 下载适用于你的计算机的最新 CLI 版本

如果在 Azure Cloud Shell 中运行这些命令，请下载适用于 Linux 的最新 Knative CLI。

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>启动 Web 控制台

通过运行以下命令找出群集 Web 控制台 URL：

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

应会得到一个类似于下面的 URL。

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

在浏览器中启动控制台 URL，使用 `kubeadmin` 凭据登录。

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift 登录屏幕":::

## <a name="install-the-openshift-serverless-operator"></a>安装 OpenShift Serverless 操作器

登录 OpenShift Web 控制台后，请确保你处于“管理员”角度。 打开“操作器中心”，查找“OpenShift 无服务器”操作器，然后选择它。

![查找 OpenShift Serverless 操作器](media/serverless/serverless-operatorhub.png)

接下来，通过单击“安装”来打开操作器安装页。

![单击“安装”以安装操作器](media/serverless/serverless-clickinstall.png)

为你的 Azure Red Hat OpenShift 群集版本选择适当的更新通道，并将操作器安装到 `openshift-serverless` 命名空间中。 向下滚动，然后单击“安装”。

![操作器安装页](media/serverless/serverless-installpage.png)

几分钟后，状态页应反映操作器已安装并可供使用。 单击“查看操作器”按钮以继续。

![操作器已安装并可供使用](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>安装 Knative Serving

使用上游 Knative 可以在 OpenShift Serverless 上以无服务器方式运行任何容器。 Knative 扩展了 Kubernetes 以提供一组组件，用于使用无服务器方法部署、运行和管理新式应用程序。

### <a name="create-an-instance-of-the-knative-serving"></a>创建 Knative Serving 的实例

通过单击左上方的项目下拉列表来切换到 `knative-serving` 命名空间，然后在“提供的 API”下，单击“Knative Serving”卡中的“创建实例”。

![单击以创建 Knative 服务实例](media/serverless/serverless-createknativeserving.png)

保留默认值，然后在“创建 Knative Serving”页中向下滚动以单击“创建”按钮。

![保留默认值并单击“创建”](media/serverless/serverless-createknativeserving2.png)

等到“状态”列显示“就绪”，此时 OpenShift Serverless 应已安装，你已准备好创建 OpenShift Serverless 项目。

![Knative Serving 就绪](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>创建无服务器项目

若要创建名为 `demoserverless` 的新项目，请运行以下命令：

```azurecli-interactive
oc new-project demoserverless
```

应该会看到与下面类似的输出：

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

在左侧菜单中切换到“开发人员”透视而不是“管理员”透视，然后在项目列表中选择 `demoserverless` 。 然后转到该项目的“拓扑”页。

![Azure Red Hat OpenShift 项目拓扑](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>使用 Web 控制台进行部署

从提供的用于部署应用程序的选项中，选择“从 Git”。 这会转至“从 Git 导入”页。 使用 `https://github.com/sclorg/django-ex.git` 作为“Git 存储库 URL”。 示例 Web 应用程序是使用 Python 编程语言实现的。

![Git 中的 Azure Red Hat OpenShift 项目](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift 检测到这是一个 Python 项目，并选择适当的生成器映像。

向下滚动到“资源”，并确保选择“Knative 服务”作为要生成的资源类型。 此操作会创建 Knative 服务，这是使 OpenShift Serverless 在空闲时可以缩减到零的部署类型。

![Azure Red Hat OpenShift 项目 - Knative](media/serverless/serverless-knative.png)


准备就绪后，在页面底部单击“创建”。 这将创建资源来管理应用程序的生成和部署。 然后将重定向到项目的拓扑概述。

拓扑概述提供已部署的应用程序的直观表示形式。 在此视图中，可以看到应用程序整体结构。

等待生成完成。 可能需要几分钟时间。 生成完成后，服务左下角会出现一个绿色复选标记。

![Azure Red Hat OpenShift 项目拓扑 - 生成成功](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>查看应用程序规模

在拓扑视图顶部的“显示选项”列表中，单击“Pod 计数” 。 等待 Pod 计数纵向缩减到零个 Pod。 纵向缩减可能需要几分钟时间。

![Azure Red Hat OpenShift 项目拓扑 - 缩减到零](media/serverless/serverless-scaledtozero.png)

单击 Knative 服务面板右上角的“打开 URL”图标。 应用程序会在新选项卡中打开。关闭新浏览器选项卡并返回到拓扑视图。 在拓扑视图中，可以看到应用程序已纵向扩展到一个 Pod，用于容纳你的请求。 几分钟后，应用程序会重新向下缩减到零个 Pod。

![Azure Red Hat OpenShift 项目拓扑 - 扩展到一](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>强制新修订版本和设置流量分布

对服务配置进行每次更新时，都会创建服务的新修订版本。 默认情况下，服务路由会将所有流量指向最新的就绪修订版本。 可以通过定义获取部分流量的新修订版本来更改此行为。 Knative 服务允许进行流量映射，这意味着可以将服务的修订版本映射到流量的已分配部分。 流量映射还提供了一个选项，用于为特定修订版本创建唯一的 Url。

在“拓扑”中，单击服务中的修订版本以查看其详细信息。 Pod 圈下以及详细信息面板顶部的徽章应该是 `(REV)`。 在侧面板的“资源”选项卡中，向下滚动并单击与服务关联的配置。

![Azure Red Hat OpenShift 项目拓扑 - 修订版本](media/serverless/serverless-revdetails.png)

通过切换到“YAML”选项卡并向下滚动以将 `timeoutSeconds` 的值编辑为 `301`，从而强制进行配置更新。 单击“保存”，以保存更新的配置。 在实际方案下，也可以通过更新容器映像标记来触发此类配置更新。

![通过更新配置来强制实施新的修订版本](media/serverless/serverless-confupdate.png)

返回“拓扑”视图，你会看到已部署了新的修订版本。 单击以徽标 `(KSVC)` 结尾的服务，然后单击“设置流量分配”按钮，此时应能够在服务的不同修订版本之间分配流量。

![设置流量分配](media/serverless/serverless-trafficdist.png)

“拓扑”视图现在会显示在两个修订版本之间分配流量的方式。

![查看流量分配](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>使用 Knative 命令行工具 (kn)

在前面的步骤中，已使用 OpenShift Web 控制台创建应用程序并将其部署到 OpenShift Serverless。 由于 OpenShift Serverless 在下面运行 Knative，因此还可以使用 Knative 命令行工具 (kn) 创建 Knative 服务。

> [!NOTE]
> 如果尚未安装 `kn` CLI，请确保按照本文先决条件部分中的步骤进行操作。 此外，请确保已使用 OpenShift 命令行工具 `oc` 登录。

我们将使用已经 `quay.io/rhdevelopers/knative-tutorial-greeter` 生成的容器映像。

### <a name="deploy-a-service"></a>部署服务

若要部署服务，请运行以下命令：

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

会看到如下所示的输出。

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

可以通过运行以下内容来检索项目中的路由列表：

```azurecli-interactive
kn route list
```

你会获取命名空间中的路由列表。 在浏览器中打开 URL，以查看部署的服务。

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>部署服务的新修订版本

通过运行以下命令，传递 `:latest` 映像标记和环境变量 `MESSAGE_PREFIX`，来部署应用程序的新修订版本：

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

你会收到一条确认，指出已部署新的修订版本 `greeter-v2`。

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

若要查看所有修订版本及其流量分配的列表，请运行以下命令：

```azurecli-interactive
kn revision list
```

你会获得类似于下面这样的列表。 请注意，新修订版本会获得 100% 的流量。

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>蓝/绿部署和 canary 部署

默认情况下，部署新修订版本时，会获得 100% 的流量。 假设你要实现一个蓝/绿部署策略，你可以在其中快速回滚到较旧版本的应用程序。 Knative 使此策略变得简单。

更新服务以创建三个流量标记，同时将100% 的流量发送到

- current：指向当前已部署版本
- prev：指向上一个版本
- latest：始终指向最新版本

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

你会收到类似于下面的确认。

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

使用以下命令列出路由：

```azurecli-interactive
kn route describe greeter
```

你将获得一个输出，其中显示每个标记的 URL 以及流量分配。

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

假设你要快速回滚到上一个版本，则可以更新流量分配，以便将 100% 的流量发送到上一个标记：

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

通过使用以下命令列出路由，再次进行检查：

```azurecli-interactive
kn route describe greeter
```

你会看到一个输出，其中显示 100% 的流量分配会进入上一个版本。

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

操作流量分配，同时在浏览器中刷新主路由（在本例中为 `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io`）。

## <a name="clean-up-resources"></a>清理资源

完成应用程序后，可运行以下命令来删除项目：

```azurecli-interactive
oc delete project demoserverless
```

还可按照[教程：删除 Azure Red Hat OpenShift 4 群集](./tutorial-delete-cluster.md)中的说明删除群集。

## <a name="next-steps"></a>后续步骤

本指南已介绍如何：
> [!div class="checklist"]
>
> * 安装 OpenShift Serverless 操作器和 Knative Serving
> * 使用 Web 控制台部署无服务器项目
> * 使用 Knative CLI (kn) 部署无服务器项目
> * 使用 Knative CLI (kn) 配置蓝/绿部署和 canary 部署

详细了解如何使用 [OpenShift Serverless](https://www.openshift.com/learn/topics/serverless) 在 Azure Red Hat OpenShift 上生成和部署事件驱动的无服务器应用程序，按照 [OpenShift Serverless 入门](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html)文档和 [创建和管理无服务器应用程序](https://docs.openshift.com/container-platform/4.5/serverless/serving-creating-managing-apps.html)文档中的说明操作。
