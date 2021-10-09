---
title: 使用 Open Service Mesh 管理现有应用程序
description: 如何使用 Open Service Mesh 管理现有应用程序
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 092e42a5f9c1779fc5968b9fc733d260d405a90a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625898"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>使用 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 加载项管理现有应用程序

## <a name="before-you-begin"></a>开始之前

本演练中详细介绍的步骤假定你之前已为 AKS 群集启用了 OSM AKS 加载项。 如果未启用，请在继续操作前查看[部署 OSM AKS 加载项](./open-service-mesh-deploy-addon-az-cli.md)一文。 此外，你的 AKS 群集必须是 Kubernetes `1.19+` 和更高版本，已启用 Kubernetes RBAC，与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- JSON 处理器“jq”版本 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>验证 Open Service Mesh (OSM) 宽松流量模式策略

在 OSM 宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

若要验证群集的当前 OSM 宽松流量模式，请运行以下命令：

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

OSM MeshConfig 的输出应如下所示：

```Output
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

如果 enablePermissiveTrafficPolicyMode 配置为 true，则可以安全地加入命名空间，而不会中断任何服务间通信 。 如果 enablePermissiveTrafficPolicyMode 配置为 false，则需确保已部署正确的 [SMI](https://smi-spec.io/) 流量访问策略清单 。 你还需确保有一个表示命名空间中部署的每个服务的服务帐户。 有关宽松流量模式的更多详细信息，请访问并阅读[宽松流量策略模式](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)一文。

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>在 Open Service Mesh (OSM) 宽松流量策略配置为 true 的情况下加入现有已部署应用程序

我们要做的第一件事就是将已部署的应用程序命名空间添加到 OSM 进行管理。 下面的示例将 bookstore 命名空间加入到 OSM。

```azurecli-interactive
osm namespace add bookstore
```

应会看到以下输出：

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

接下来，我们看一下命名空间中的当前 Pod 部署。 运行以下命令，查看 `bookbuyer` 命名空间中的 Pod。

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出：

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

我们可以看到 READY 列显示 1/1，表示应用程序 Pod 只有一个容器。  接下来，我们需要重启应用程序部署，以便 OSM 可以将 Envoy 挎斗代理容器与应用程序 Pod 一起注入。 让我们获取命名空间中的部署列表。

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

应该会看到以下输出：

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

现在，我们将重启部署，以将 Envoy 挎斗代理容器与应用程序 Pod 一起注入。 运行以下命令。

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

应该会看到以下输出：

```Output
deployment.apps/bookbuyer restarted
```

如果我们再次查看命名空间中的 Pod：

```azurecli-interactive
kubectl get pod -n bookbuyer
```

现在你会看到 READY 列显示 2/2，表示有两个容器已准备就绪并可供 Pod 使用。  第二个容器是 Envoy 挎斗代理。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

通过运行以下 describe 命令以查看配置，我们可以进一步检查 Pod 来查看 Envoy 代理。

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

验证应用程序在注入 Envoy 挎斗代理后是否仍正常工作。

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>在 Open Service Mesh (OSM) 宽松流量策略配置为 false 的情况下加入现有已部署应用程序

如果 OSM 宽松流量策略配置设置为 `false`，OSM 将需要部署显式 [SMI](https://smi-spec.io/) 流量访问策略，群集内的服务间通信才能进行。 目前，OSM 还使用 Kubernetes 服务帐户来授权服务间通信。 为确保现有已部署应用程序由 OSM 网格管理时能够通信，我们现在验证要使用的服务帐户是否存在，使用服务帐户信息更新应用程序部署，并应用 [SMI](https://smi-spec.io/) 流量访问策略。

### <a name="verify-kubernetes-service-accounts"></a>验证 Kubernetes 服务帐户

验证应用程序部署到的命名空间中是否有 kubernetes 服务帐户。

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

下面的输出显示了 `bookbuyer` 命名空间中有一个名为 `bookbuyer` 的服务帐户。

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

如果列出的服务帐户并非默认帐户之外的帐户，则需为应用程序创建一个。 例如，使用以下命令在应用程序的已部署命名空间中创建服务帐户。

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

### <a name="view-your-applications-current-deployment-specification"></a>查看应用程序的当前部署规范

如果你必须创建上一节中的服务帐户，则可能是因为未使用部署规范中的特定 `serviceAccountName` 对应用程序部署进行配置。可以使用以下命令来查看应用程序的部署规范：

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

部署列表将在输出中列出。

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

我们将对部署运行 describe 命令，以查看“Pod Template”部分是否列出了服务帐户。

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

在此特定部署中，你可以看到，“Pod Template”部分列出了与部署关联的服务帐户。 此部署使用服务帐户 `bookbuyer`。 如果看不到“Service Account:”属性，则表明部署未配置为使用服务帐户。

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

可以通过多种方法来更新部署，以添加 kubernetes 服务帐户。 请参阅[更新部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)或[为 Pod 配置服务帐户](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)内联 Kubernetes 文档。 使用服务帐户更新部署规范后，将部署重新部署到群集 (kubectl apply -f your-deployment.yaml)。

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>部署所需的服务网格接口 (SMI) 策略

要允许获得授权的流量流入网格，所需执行的最后一步是为应用程序部署所需的 [SMI](https://smi-spec.io/) 流量访问策略。 使用 [SMI](https://smi-spec.io/) 流量访问策略可实现的配置量超出了本演练的范围，但我们将详细介绍该规范的一些常见组成部分，并演示如何配置简单的 TrafficTarget 和 HTTPRouteGroup 策略来为应用程序启用服务间通信。

用户可以使用 [SMI](https://smi-spec.io/) [**流量访问控制**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control)规范为其应用程序定义访问控制策略。 我们将重点介绍 TrafficTarget 和 HTTPRoutGroup API 资源。 

TrafficTarget 资源由三个主要配置设置目标、规则和源组成。 下面显示了一个示例 TrafficTarget。

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
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
```

在上面的 TrafficTarget 规范中，`destination` 表示为目标源服务配置的服务帐户。 请记住，先前添加到部署的服务帐户将用于授予对其附加到的部署的访问权限。 在此特定示例中，`rules` 部分定义了允许通过连接的 HTTP 流量类型。 可以为 HTTP 标头配置精细的正则表达式模式，以具体定义允许通过 HTTP 的流量。 `sources` 部分表示发起通信的服务。 此规范读取 `bookbuyer` 需求以与 bookstore 通信。

HTTPRouteGroup 资源包含 HTTP 标头信息的一个或一组匹配项，并且是 TrafficTarget 规范的要求。在以下示例中，你可以看到 HTTPRouteGroup 授权三个 HTTP 操作：两个 GET 和一个 POST。

```HTTPRouteGroup Example Spec
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
```

如果不熟悉前端应用程序向其他应用程序层发送的 HTTP 流量类型，由于 TrafficTarget 规范需要规则，你可以使用以下规范为 HTTPRouteGroup 创建与“全部允许”规则等效的规则。

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

配置 TrafficTarget 和 HTTPRouteGroup 规范后，可以将它们组合成一个 YAML 并进行部署。 下面是 bookstore 的示例配置。

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
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
EOF
```

有关规范的更多详细信息，请访问 [SMI](https://smi-spec.io/) 站点。

## <a name="manage-the-applications-namespace-with-osm"></a>使用 OSM 管理应用程序的命名空间

接下来，我们将确保通过 OSM 来管理命名空间，并重启部署以获取随应用程序一起注入的 Envoy 挎斗代理。

运行以下命令，配置要由 OSM 管理的 `azure-vote` 命名空间。

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

接下来，使用以下命令重启 `azure-vote-front` 和 `azure-vote-back` 部署。

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

如果查看 `azure-vote` 命名空间的 Pod，我们可以看到 `azure-vote-front` 和 `azure-vote-back` 的 READY 列都显示为 2/2，表示 Envoy 挎斗代理已随应用程序一起注入。
