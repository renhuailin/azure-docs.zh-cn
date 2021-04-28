---
title: 从源代码将应用程序部署到 Azure Red Hat OpenShift
description: 了解如何使用 Source-to-Image (S2I) 生成策略将应用程序部署到 Azure Red Hat OpenShift
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, source to image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558572"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>从源代码将应用程序部署到 Azure Red Hat OpenShift

本文介绍如何使用 Source-to-Image (S2I) 生成通过源代码将应用程序部署到 Azure Red Hat OpenShift 群集。 [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) 是用于从源代码生成可重复的容器映像的生成过程。 S2I 将源代码注入到容器映像中，并让容器准备要执行的源代码，从而生成可运行的映像。 可以让 OpenShift 从源代码生成应用程序，这样你就不必在每次更改时手动构造容器。 然后，OpenShift 可以在收到源代码更改通知时自动生成并部署新版本。

## <a name="before-you-begin"></a>在开始之前

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>创建一个项目

若要创建名为 `demoproject` 的新项目，请运行以下命令：

```azurecli-interactive
oc new-project demoproject
```

输出应如以下所示：

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>启动 Web 控制台

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

在左侧菜单中切换到“开发人员”透视而不是“管理员”透视，然后在项目列表中选择 `demoproject` 。 然后转到该项目的“拓扑”页。

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift 项目拓扑":::

由于项目是空的，应该不会显示任何工作负载，而会提供有关如何部署应用程序的各种选项。

## <a name="deploying-using-the-web-console"></a>使用 Web 控制台进行部署

从提供的用于部署应用程序的选项中，选择“从 Git”。 这会转至“从 Git 导入”页。 使用 `https://github.com/sclorg/django-ex.git` 作为“Git 存储库 URL”。 示例 Web 应用程序是使用 Python 编程语言实现的。

:::image type="content" source="media/s2i/from-git.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目":::

> [!NOTE]
> OpenShift 检测到这是一个 Python 项目，并选择适当的生成器映像。

向下滚动到“高级选项”，并确保选中“创建指向应用程序的路由”。 此操作将创建 OpenShift 路由，这是一种向服务提供外部可访问的主机名来公开服务的方法。

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 路由设置":::

准备就绪后，在页面底部单击“创建”。 这将创建资源来管理应用程序的生成和部署。 然后将重定向到项目的拓扑概述。

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 拓扑":::

拓扑概述提供已部署的应用程序的直观表示形式。 在此视图中，可以看到应用程序整体结构。

单击 Git 图标可转到从中生成应用程序源代码的 Git 存储库。 左下方显示的图标显示应用程序的生成状态。 单击此图标将转到生成详细信息部分。 如果应用程序已公开路由，则可单击右上方的图标，打开已创建的应用程序路由的 URL。

当应用程序纵向扩展或缩减、开始推出和重新创建 Pod 时，拓扑视图中的应用程序表示形式会进行动画处理，让你可以实时查看正在进行的操作。

单击应用程序图标将显示更多详细信息，如下所示。

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 详细信息":::

## <a name="viewing-the-builder-logs"></a>查看生成器日志

启动生成后，单击“资源”面板上显示的“查看日志”链接 **。

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 生成日志":::

这使你能够在生成运行时监视其进度。 生成器映像（本例中为 Python）会将应用程序源代码注入最终映像，然后将其推送到 OpenShift 内部映像注册表。 当你看到最终消息“推送成功”时，生成已成功完成。

## <a name="accessing-the-application"></a>访问应用程序

完成应用程序映像的生成后，需要部署该映像。

单击左侧菜单栏中的“拓扑”，以返回到项目的拓扑视图。 使用 Web 控制台创建应用程序时，会自动为应用程序创建一个路由，该路由将在群集外部公开。 可用于从 Web 浏览器访问应用程序的 URL 会显示在你之前查看的应用程序的“资源”选项卡上。

在拓扑视图中，可单击环的右上方图标来访问已部署的应用程序的 URL。 部署完成后，单击该图标，应该会看到部署的应用程序。

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 浏览应用":::

## <a name="deploying-using-the-command-line"></a>使用命令行进行部署

你已了解如何使用 Web 控制台部署应用程序，接下来将部署相同的 Web 应用程序，但这次使用 `oc` 命令行工具。

运行以下命令以删除项目并重新开始：

```azurecli-interactive
oc delete project demoproject
```

你应会收到一条 `demoproject` 已删除的确认消息。

```output
project.project.openshift.io "demoproject" deleted
```

通过运行以下命令重新创建 `demoproject`：

```azurecli-interactive
oc new-project demoproject
```

在项目中，基于 GitHub 上的源代码创建一个新的应用程序，并为提供的 Python 最新版本指定 S2I 生成器。

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

应会显示与下面类似的输出：

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift 将使用 Git 存储库的名称作为应用程序的名称。 运行以下内容来查看生成和部署的状态：

```azurecli-interactive
oc status
```

完成生成和部署后，应会看到如下输出。

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

若要在 OpenShift 群集外公开应用程序，需要运行以下命令来创建路由：

```azurecli-interactive
oc expose service/django-ex
```

应会收到一条确认消息。

```output
route.route.openshift.io/django-ex exposed
```

通过运行以下代码检索 URL：

```azurecli-interactive
oc get route django-ex
```

应会返回分配给路由的主机名，你可使用它来浏览已部署的应用程序。

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>触发新的二进制生成

处理应用程序时，可能需要进行更改，并查看它们的部署情况。 可以轻松设置一个 Webhook，在你每次提交代码时，它将触发新的生成和部署。 但是，这可能并不理想，因为有时你只是想要查看更改，而不必将所有代码更改都推送到存储库。

如果需要快速迭代更改，可使用二进制生成方法。 为演示这一方案，请运行以下内容在本地克隆应用程序的 Git 存储库：

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

这将创建一个包含该应用程序的源代码的子目录 `django-ex`：

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

更改到子目录：

```azurecli-interactive
cd django-ex
```

打开集成的 Azure Cloud Shell 编辑器：

```azurecli-interactive
code welcome/templates/welcome/index.html
```

向下滚动并将显示 `Welcome to your Django application on OpenShift` 的行更改为显示 `Welcome to Azure Red Hat OpenShift`。 保存该文件，然后通过右上方 `...` 菜单关闭编辑器。

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 在 Azure Cloud Shell 编辑器中编辑应用程序":::

运行以下命令来启动新生成：

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

通过传递 `--from-dir=.` 标志，OpenShift 命令行将上传指定目录中的源代码，然后启动生成和部署过程。 应会看到类似于下面的输出，几分钟后，生成应该已完成。

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

如果你在浏览器中刷新应用程序，应会看到更新后的标题。

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Git 中的 Azure Red Hat OpenShift 项目 - 浏览更新后的应用":::

## <a name="clean-up-resources"></a>清理资源

完成应用程序后，可运行以下命令来删除项目：

```azurecli-interactive
oc delete project demoproject
```

还可按照[教程：删除 Azure Red Hat OpenShift 4 群集](./tutorial-delete-cluster.md)中的说明删除群集。

## <a name="next-steps"></a>后续步骤

在本指南中，你了解了以下内容：
> [!div class="checklist"]
>
> * 创建一个项目
> * 使用 Web 控制台从源代码部署应用程序
> * 使用 OpenShift 命令行从源代码部署应用程序
> * 使用 OpenShift 命令行触发二进制生成

详细了解如何使用 Source-to-Image 和[其他生成策略](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)来生成和部署应用程序。
