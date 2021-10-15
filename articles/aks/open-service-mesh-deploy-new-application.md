---
title: 使用 Open Service Mesh 管理新的应用程序
description: 如何使用 Open Service Mesh 管理新的应用程序
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 77728c7b61fc6b76cb5ecb51ff1d90e0c0a93e99
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535549"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>使用 Azure Kubernetes 服务 (AKS) 加载项 Open Service Mesh (OSM) 管理新的应用程序

## <a name="before-you-begin"></a>开始之前

本演练中详细介绍的步骤假定你已创建 AKS 群集（Kubernetes `1.19+` 和更高版本，并且已启用 Kubernetes RBAC），与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- JSON 处理器 jq 版本 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本演练中，我们将使用具有以下 Kubernetes 服务的 OSM bookstore 应用程序：

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

为每个应用程序组件创建命名空间。

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

应该会看到以下输出：

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，此操作将允许 OSM 控制器自动将 Envoy Sidecar 代理容器与应用程序一起注入。 运行以下命令，加入 OSM bookstore 应用程序命名空间。

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

应该会看到以下输出：

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>将 bookstore 应用程序部署到 AKS 群集

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
```

下面汇总了所有部署输出。

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="checkpoint-what-got-installed"></a>检查点：安装了哪些组件？

Bookstore 应用程序是一个示例多层应用程序，非常适合测试服务网格功能。 该应用程序包含 4 个服务，即 `bookbuyer`、`bookthief`、`bookstore` 和 `bookwarehouse`。 `bookbuyer` 和 `bookthief` 服务都与 `bookstore` 服务通信，来从 `bookstore` 服务检索书籍。 `bookstore` 服务从 `bookwarehouse` 服务中检索书籍来提供给 `bookbuyer` 和 `bookthief`。 这是一个简单的多层应用程序，可以很好地演示如何使用服务网格来保护和授权应用程序服务之间的通信。 在本演练中，我们将启用和禁用服务网格接口 (SMI) 策略，以允许和禁止服务通过 OSM 进行通信。 下面是一个体系结构示意图，显示了已为 bookstore 应用程序安装的组件。

![OSM bookbuyer 应用程序体系结构](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>验证 bookstore 应用程序是否正在 AKS 群集中运行

到目前为止，我们部署了 `bookstore` 多容器应用程序，但只能从 AKS 群集中访问它。 后面的教程将帮助你通过入口控制器在群集外部公开此应用程序。 现在，我们将使用端口转发来访问 AKS 群集中的 `bookbuyer` 应用程序，以验证它是否从 `bookstore` 服务购买书籍。

为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 `bookbuyer` 和 `bookthief` 组件 UI。

首先，让我们获取 `bookbuyer` Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 `bookbuyer` Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

获取 Pod 的名称后，现在可以使用 port-forward 命令来设置从本地系统到 AKS 群集中的应用程序的隧道。 运行以下命令，为本地系统端口 8080 设置端口转发。 再次使用指定的 bookbuyer Pod 名称。

> [!NOTE]
> 对于所有端口转发命令，最好使用另一个终端，以便可以继续执行本演练，而不会断开隧道的连接。 此外，最好在 Azure Cloud Shell 外部建立端口转发隧道。

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

应会看到如下所示的输出：

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 `bookbuyer` 应用程序 UI，如下图所示。

![OSM bookbuyer 应用程序 UI 图像](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

你还会注意到，购买的书籍总数将持续增加到 `bookstore` v1 服务。 `bookstore` v2 服务尚未部署。 在演示 SMI 流量拆分策略时，我们将部署 `bookstore` v2 服务。

你还可查看 `bookthief` 服务的这些信息。

```azurecli-interactive
kubectl get pod -n bookthief
```

应该会看到与下面类似的输出。 `bookthief` Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

端口转发到 `bookthief` Pod。

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

从浏览器导航到以下 URL：`http://localhost:8080`。 你应该会看到 `bookthief` 当前正在从 `bookstore` 服务盗窃书籍！ 稍后，我们将实现流量策略来停止 `bookthief`。

![OSM bookthief 应用程序 UI 图像](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>为网格禁用 OSM 宽松流量模式

现在我们将禁用宽松流量模式策略，OSM 需要将显式 [SMI](https://smi-spec.io/) 策略部署到群集，以允许网格中的每个服务进行通信。 若要禁用宽松流量模式，请运行以下命令，将值从 `true` 更改为 `false` 并更新 OSM MeshConfig 资源属性。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

应该会看到与下面类似的输出。

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

若要验证宽松流量模式是否已禁用，请将端口转发回 `bookbuyer` Pod 或 `bookthief` Pod，以在浏览器中查看其 UI，并查看已购买或已失窃的书籍是否不再增加。 请确保刷新浏览器。 如果这两个书籍数量已停止增加，则表明已正确应用策略。 你已成功阻止 `bookthief` 盗窃书籍，但 `bookbuyer` 无法从 `bookstore` 购买书籍，`bookstore` 也无法从 `bookwarehouse` 检索书籍。 接下来，我们将实现 [SMI](https://smi-spec.io/) 策略，以便只允许网格中的相关服务进行通信。 有关宽松流量模式的更多详细信息，请访问并阅读[宽松流量策略模式](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)一文。

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>应用服务网格接口 (SMI) 流量访问策略

现在我们已禁用网格中的所有通信，接下来我们允许 `bookbuyer` 服务与 `bookstore` 服务通信来购买书籍，并允许 `bookstore` 服务与 `bookwarehouse` 服务通信来检索要销售的书籍。

部署以下 [SMI](https://smi-spec.io/) 策略。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

应该会看到与下面类似的输出。

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

你现在可以在 `bookbuyer` Pod 或 `bookstore` Pod 上设置端口转发会话，并且可以看到书籍购买数量和书籍销售数量这两个指标重新增加。 你还可对 `bookthief` Pod 执行相同的操作，验证它是否仍然无法再盗窃书籍。

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>应用服务网格接口 (SMI) 流量拆分策略

对于我们的最终演示，我们将创建一个 [SMI](https://smi-spec.io/) 流量拆分策略，以配置从一个服务到多个后端服务的通信权重。 利用流量拆分功能，可以通过按 0 到 100 的比例调整流量权重，逐步将连接从一个服务转移到另一个服务。

下图是要部署的 [SMI](https://smi-spec.io/) 流量拆分策略的示意图。 我们将部署另一个 `Bookstore` 应用程序作为版本 2，然后拆分来自 `bookbuyer` 的传入流量，其中 `bookstore` v1 服务的流量权重为 25%，`bookstore` v2 服务的流量权重为 75%。

![OSM bookbuyer 流量拆分示意图](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

部署 `bookstore` v2 服务。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

应该会看到以下输出。

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

现在部署流量拆分策略，在 `bookbuyer` v1 服务和 `bookstore` v2 服务之间拆分流量。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

应该会看到以下输出。

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

设置到 `bookbuyer` Pod 的端口转发隧道后，现在你应该会看到从 `bookstore` v2 服务购买的书籍。 如果你继续观察购买数量的增加，应该会注意到通过 `bookstore` v2 服务购买的书籍数量增加速度更快。

![OSM bookbuyer 书籍购买数量 UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)