---
title: Open Service Mesh（预览版）
description: Azure Kubernetes 服务 (AKS) 中的 Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b0a37400d02ff324e7191bb0b04243d7f16090bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877149"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS 附加产品（预览版）

## <a name="overview"></a>概述

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。

OSM 在 Kubernetes 上运行基于 Envoy 的控制平面，可以使用 [SMI](https://smi-spec.io/) API 进行配置，并通过将 Envoy 代理作为挎斗容器注入到每个应用程序实例旁边来运行。 Envoy 代理包含并执行有关访问控制策略的规则，实现路由配置，并捕获指标。 控制平面会持续配置代理，以确保策略和路由规则是最新的，并确保代理处于正常状态。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>功能和特性

OSM 提供以下一组功能和特性，以便为 Azure Kubernetes 服务 (AKS) 群集提供云原生服务网格：

- 通过启用 mTLS 来保护服务与服务之间的通信

- 通过启用 Envoy 挎斗代理自动注入，轻松地将应用程序加入到网格中

- 轻松透明地为在部署中转移的流量进行配置

- 能够为服务定义并执行细化的访问控制策略

- 提供对应用程序指标的观察和见解，用于调试和监视服务

- 通过可插入接口与外部证书管理服务/解决方案集成

## <a name="scenarios"></a>方案

OSM 可在以下方案中为 AKS 部署提供帮助：

- 在群集中部署的服务终结点之间提供加密通信

- 为网格中的 HTTP/HTTPS 流量和 TCP 流量授权

- 在 A/B 部署或 Canary 部署的两个或多个服务之间配置加权流量控制

- 从应用程序流量收集 KPI 和查看 KPI

## <a name="osm-service-quotas-and-limits-preview"></a>OSM 服务配额和限制（预览版）

可在 AKS [配额和区域限制页](https://docs.microsoft.com/azure/aks/quotas-skus-regions)中找到 OSM 预览版的服务配额和限制。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> 不要尝试使用 `osm install` 通过二进制文件安装 OSM。 这将导致 OSM 安装未作为附加产品与 AKS 集成。

### <a name="register-the-aks-openservicemesh-preview-feature"></a>注册 `AKS-OpenServiceMesh` 预览版功能

若要创建可使用 Open Service Mesh 附加产品的 AKS 群集，必须在订阅中启用 `AKS-OpenServiceMesh` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `AKS-OpenServiceMesh` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>为新的 AKS 群集安装 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品

对于新的 AKS 群集部署方案，你将从 AKS 群集的全新部署开始，然后在创建群集时启用 OSM 附加产品。

### <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus2 位置（区域）创建名为 myOsmAksGroup 的资源组： 

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>部署启用了 OSM 附加产品的 AKS 群集

现在将部署启用了 OSM 附加产品的新 AKS 群集。

> [!NOTE]
> 请注意，以下 AKS 部署命令利用操作系统临时磁盘。 可在此处找到有关 [AKS 的临时 OS 磁盘](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)的详细信息

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>获取 AKS 群集访问凭据

获取新的托管 Kubernetes 群集的访问凭据。

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>为现有 AKS 群集启用 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品

对于现有 AKS 群集方案，你将为已部署的现有 AKS 群集启用 OSM 附加产品。

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>为现有 AKS 群集启用 OSM 附加产品

若要启用 AKS OSM 附加产品，你需要运行 `az aks enable-addons --addons` 命令并传递 `open-service-mesh` 参数

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

你应该会看到如下所示的输出，用于确认是否已安装 AKS OSM 附加产品。

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>验证 AKS OSM 附加产品安装

你需要运行几个命令来检查 AKS OSM 附加产品的所有组件是否均已启用并正在运行：

首先，我们可以查询群集的附加产品配置文件，以检查已安装附加产品的启用状态。 以下命令应返回“true”。

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

以下 `kubectl` 命令将报告 osm-controller 的状态。

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>访问 AKS OSM 附加产品

目前，可以通过 configmap 访问和配置 OSM 控制器配置。 若要查看 OSM 控制器配置设置，请通过 `kubectl` 查询 osm-config configmap 来查看其配置设置。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap 的输出应如下所示：

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

请注意，permissive_traffic_policy_mode 配置为 true。  在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

> [!WARNING]
> 在继续操作之前，请验证宽松流量策略模式是否设置为 true，如果不是，请使用以下命令将其更改为 true

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>部署要由 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品管理的新应用程序

### <a name="before-you-begin"></a>开始之前

本演练中详细介绍的步骤假定你已创建 AKS 群集（Kubernetes `1.19+` 和更高版本，并且已启用 Kubernetes RBAC），与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本演练中，我们将使用具有以下 Kubernetes 服务的 OSM bookstore 应用程序：

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，OSM 控制器将能自动将 Envoy 挎斗代理容器与应用程序一起注入。 运行以下命令，加入 OSM bookstore 应用程序命名空间。

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>将 bookstore 应用程序部署到 AKS 群集

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
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

### <a name="checkpoint-what-got-installed"></a>检查点：安装了哪些组件？

示例 bookstore 应用程序是一个多层应用程序，其中包含四个服务：bookbuyer、bookthief、bookstore 和 bookwarehouse。 bookbuyer 服务和 bookthief 服务都与 bookstore 服务通信，以从中检索书籍。 bookstore 服务从 bookwarehouse 服务检索书籍，以提供给 bookbuyer 和 bookthief。 这是一个简单的多层应用程序，可以很好地演示如何使用服务网格来保护和授权应用程序服务之间的通信。 在本演练中，我们将启用和禁用服务网格接口 (SMI) 策略，以允许和禁止服务通过 OSM 进行通信。 下面是一个体系结构示意图，显示了已为 bookstore 应用程序安装的组件。

![OSM bookbuyer 应用程序体系结构](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>验证 bookstore 应用程序是否正在 AKS 群集中运行

到目前为止，我们部署了 bookstore 多容器应用程序，但只能从 AKS 群集中访问它。 后面的教程将帮助你通过入口控制器在群集外部公开此应用程序。 现在，我们将使用端口转发来访问 AKS 群集中的 bookbuyer 应用程序，以验证它是否从 bookstore 服务购买书籍。

为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 bookbuyer 和 bookthief 组件 UI。

首先，让我们获取 bookbuyer Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 bookbuyer Pod 会附加一个唯一名称。

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

应该会看到与下面类似的输出。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 bookbuyer 应用程序 UI，如下图所示。

![OSM bookbuyer 应用程序 UI 图像](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

你还会注意到，购买的书籍总数将持续增加到 bookstore v1 服务。 bookstore v2 服务尚未部署。 在演示 SMI 流量拆分策略时，我们将部署 bookstore v2 服务。

你还可以查看 bookthief 服务的这些信息。

```azurecli-interactive
kubectl get pod -n bookthief
```

应该会看到与下面类似的输出。 bookthief Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

端口转发到 bookthief Pod。

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

从浏览器导航到以下 URL：`http://localhost:8080`。 你应该会看到 bookthief 当前正在从 bookstore 服务盗窃书籍！ 稍后，我们将实现流量策略来停止 bookthief。

![OSM bookthief 应用程序 UI 图像](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>为网格禁用 OSM 宽松流量模式

如前文所述，查看 OSM 群集配置时，OSM 配置默认为启用宽松流量模式策略。 在此模式下，系统会跳过流量策略强制执行，而且 OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

现在我们将禁用宽松流量模式策略，OSM 需要将显式 [SMI](https://smi-spec.io/) 策略部署到群集，以允许网格中的每个服务进行通信。 若要禁用宽松流量模式，请运行以下命令，将值从 `true` 更改为 `false` 并更新 configmap 属性。

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

应该会看到与下面类似的输出。 bookthief Pod 会附加一个唯一名称。

```Output
configmap/osm-config patched
```

若要验证宽松流量模式是否已禁用，请端口转发回 bookbuyer Pod 或 bookthief Pod，以在浏览器中查看其 UI，并查看已购买或已失窃的书籍是否不再增加。 请确保刷新浏览器。 如果这两个书籍数量已停止增加，则表明已正确应用策略。 你已成功阻止 bookthief 盗窃书籍，但 bookbuyer 无法从 bookstore 购买书籍，bookstore 也无法从 bookwarehouse 检索书籍。 接下来，我们将实现 [SMI](https://smi-spec.io/) 策略，以便只允许网格中的相关服务进行通信。

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>应用服务网格接口 (SMI) 流量访问策略

现在我们已禁用网格中的所有通信，接下来我们允许 bookbuyer 服务与 bookstore 服务通信以购买书籍，并允许 bookstore 服务与 bookwarehouse 服务通信以检索要销售的书籍。

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

你现在可以在 bookbuyer Pod 或 bookstore Pod 上设置端口转发会话，并且可以看到书籍购买数量和书籍销售数量这两个指标重新增加。 你还可以对 bookthief Pod 执行相同的操作，以验证它是否仍然无法盗窃书籍。

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>应用服务网格接口 (SMI) 流量拆分策略

对于我们的最终演示，我们将创建一个 [SMI](https://smi-spec.io/) 流量拆分策略，以配置从一个服务到多个后端服务的通信权重。 利用流量拆分功能，可以通过按 0 到 100 的比例调整流量权重，逐步将连接从一个服务转移到另一个服务。

下图是要部署的 [SMI](https://smi-spec.io/) 流量拆分策略的示意图。 我们将部署附加的 bookstore v2，然后拆分来自 bookbuyer 的传入流量，其中 bookstore v1 服务的流量权重为 25%，bookstore v2 服务的流量权重为 75%。

![OSM bookbuyer 流量拆分示意图](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

部署 bookstore v2 服务。

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

你会看到以下输出。

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

现在我们部署流量拆分策略，以在 bookstore v1 服务和 bookstore v2 服务之间拆分流量。

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

你会看到以下输出。

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

设置到 bookbuyer Pod 的端口转发隧道后，现在你应该会看到从 bookstore v2 服务购买书籍。 如果你继续观察购买数量的增加，应该会注意到通过 bookstore v2 服务购买的书籍数量增加速度更快。

![OSM bookbuyer 书籍购买数量 UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>管理要由 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品管理的现有已部署应用程序

### <a name="before-you-begin"></a>开始之前

本演练中详细介绍的步骤假定你之前已为 AKS 群集启用了 OSM AKS 附加产品。 否则，请参阅[为现有 AKS 群集启用 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster)一节，然后再继续操作。 此外，你的 AKS 群集必须是 Kubernetes `1.19+` 和更高版本，已启用 Kubernetes RBAC，与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>验证 Open Service Mesh (OSM) 宽松流量模式策略

在 OSM 宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

若要验证群集的当前 OSM 宽松流量模式，请运行以下命令：

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap 的输出应如下所示：

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

如果 permissive_traffic_policy_mode 配置为 true，则可以安全地加入命名空间，而不会中断任何服务间通信。  如果 permissive_traffic_policy_mode 配置为 false，则需要确保已部署正确的 [SMI](https://smi-spec.io/) 流量访问策略清单，并确保具有表示命名空间中部署的每个服务的服务帐户。  请按照[在 Open Service Mesh (OSM) 宽松流量策略配置为 false 的情况下加入现有已部署应用程序](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)的指导进行操作

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>在 Open Service Mesh (OSM) 宽松流量策略配置为 true 的情况下加入现有已部署应用程序

我们要做的第一件事就是将已部署的应用程序命名空间添加到 OSM 进行管理。

```azurecli-interactive
osm namespace add bookstore
```

应该会看到以下输出：

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

接下来，我们将看一下命名空间中的当前 Pod 部署。 运行以下命令，查看指定命名空间中的 Pod。

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

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>在 Open Service Mesh (OSM) 宽松流量策略配置为 false 的情况下加入现有已部署应用程序

如果 OSM 宽松流量策略配置设置为 `false`，OSM 将需要部署显式 [SMI](https://smi-spec.io/) 流量访问策略，群集内的服务间通信才能进行。 目前，OSM 还使用 Kubernetes 服务帐户来授权服务间通信。 为确保现有已部署应用程序由 OSM 网格管理时能够正常通信，我们需要验证要使用的服务帐户是否存在，还需要使用服务帐户信息更新应用程序部署并应用 [SMI](https://smi-spec.io/) 流量访问策略。

#### <a name="verify-kubernetes-service-accounts"></a>验证 Kubernetes 服务帐户

验证应用程序部署到的命名空间中是否有 kubernetes 服务帐户。

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

下面的输出显示了 bookbuyer 命名空间中有一个名为 `bookbuyer` 的服务帐户。

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

如果列出的服务帐户并非默认帐户，则需要为应用程序创建一个服务帐户。 例如，使用以下命令在应用程序的已部署命名空间中创建服务帐户。

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>查看应用程序的当前部署规范

如果你必须创建上一节中的服务帐户，原因可能是应用程序部署的部署规范中未配置特定的 `serviceAccountName`。可以使用以下命令来查看应用程序的部署规范：

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

部署列表将在输出中列出。

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

现在，我们将对部署运行 describe 命令，以查看“Pod Template”部分中是否列出了服务帐户。

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

在此特定部署中，你可以看到，“Pod Template”部分下方列出了与部署关联的服务帐户。 此部署使用服务帐户 bookbuyer。 如果看不到“Serivce Account:”属性，则表明部署未配置为使用服务帐户。

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

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>部署所需的服务网格接口 (SMI) 策略

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

在上面的 TrafficTarget 规范中，`destination` 表示为目标源服务配置的服务帐户。 请记住，先前添加到部署的服务帐户将用于授予对其附加到的部署的访问权限。 在此特定示例中，`rules` 部分定义了允许通过连接的 HTTP 流量类型。 可以为 HTTP 标头配置精细的正则表达式模式，以具体定义允许通过 HTTP 的流量。 `sources` 部分表示发起通信的服务。 此规范读取 bookbuyer 需求以与 bookstore 通信。

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

### <a name="manage-the-applications-namespace-with-osm"></a>使用 OSM 管理应用程序的命名空间

接下来，我们将配置 OSM 以管理命名空间，并重启部署以获取随应用程序一起注入的 Envoy 挎斗代理。

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

如果查看 `azure-vote` 命名空间的 Pod，我们可以看到 `azure-vote-front` 和 `azure-vote-back` 的 READY 列都显示 2/2，表示 Envoy 挎斗代理已随应用程序一起注入。

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>教程：使用 NGINX 入口部署由 Open Service Mesh (OSM) 管理的应用程序

Open Service Mesh (OSM) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。

在本教程中，将：

> [!div class="checklist"]
>
> - 查看当前 OSM 群集配置
> - 为 OSM 创建命名空间，以在命名空间中管理已部署的应用程序
> - 加入要由 OSM 管理的命名空间
> - 部署示例应用程序
> - 验证应用程序是否正在 AKS 群集中运行
> - 创建用于应用程序的 NGINX 入口控制器
> - 通过 Azure 应用程序网关入口在 Internet 上公开服务

### <a name="before-you-begin"></a>开始之前

本文中详细介绍的步骤假定你已创建 AKS 群集（Kubernetes `1.19+` 和更高版本，并且已启用 Kubernetes RBAC），与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>查看并验证当前 OSM 群集配置

在 AKS 群集上启用适用于 AKS 的 OSM 附加产品后，可以在 osm-config Kubernetes ConfigMap 中查看当前的配置参数。 运行以下命令，查看 ConfigMap 属性：

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

输出将显示群集的当前 OSM 配置。

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

请注意，permissive_traffic_policy_mode 配置为 true。  在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

### <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本教程中，我们将使用具有以下应用程序组件的 OSM bookstore 应用程序：

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，OSM 控制器将能自动将 Envoy 挎斗代理容器与应用程序一起注入。 运行以下命令，加入 OSM bookstore 应用程序命名空间。

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>将 bookstore 应用程序部署到 AKS 群集

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
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

### <a name="update-the-bookbuyer-service"></a>更新 bookbuyer 服务

将 bookbuyer 服务更新为具有以下服务清单的正确入站端口配置。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>验证 bookstore 应用程序是否正在 AKS 群集中运行

到目前为止，我们部署了 bookstore 多容器应用程序，但只能从 AKS 群集中访问它。 稍后我们将添加 Azure 应用程序网关入口控制器，以在 AKS 群集外公开应用程序。 为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 bookbuyer 组件 UI。

首先，让我们获取 bookbuyer Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 bookbuyer Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

获取 Pod 的名称后，现在可以使用 port-forward 命令来设置从本地系统到 AKS 群集中的应用程序的隧道。 运行以下命令，为本地系统端口 8080 设置端口转发。 再次使用指定的 bookbuyer Pod 名称。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

应该会看到与下面类似的输出。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 bookbuyer 应用程序 UI，如下图所示。

![用于 NGINX 的 OSM bookbuyer 应用程序的 UI 图像](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建 NGINX 入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

我们将利用入口控制器在 Internet 上公开由 OSM 管理的应用程序。 若要创建入口控制器，请使用 Helm 来安装 nginx-ingress。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 _ingress-basic_ 的入口资源创建 Kubernetes 命名空间。 根据需要为你自己的环境指定一个命名空间。

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

为 NGINX 入口控制器创建 Kubernetes 负载均衡器服务时，会分配动态公共 IP 地址，如以下示例输出中所示：

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

由于尚未创建入口规则，如果浏览到该内部 IP 地址，则会显示 NGINX 入口控制器的默认 404 页面。 入口规则是通过以下步骤配置的。

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>在 Internet 上公开 bookbuyer 服务

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

应该会看到以下输出：

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>查看 NGINX 日志

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

如果已成功应用入口规则，输出将显示 NGINX 入口控制器状态：

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>在外部查看 NGINX 服务和 bookbuyer 服务

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

由于入口清单中的主机名是用于测试的伪名称，因此 DNS 名称在 Internet 上将不可用。 或者，我们可以使用 curl 程序，将主机名标头传递到 NGINX 公共 IP 地址并接收 200 代码，以确认我们已成功连接到 bookbuyer 服务。

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

应该会看到以下输出：

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>教程：使用 Azure 应用程序网关入口 AKS 附加产品部署由 Open Service Mesh (OSM) 管理的应用程序

Open Service Mesh (OSM) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。

在本教程中，将：

> [!div class="checklist"]
>
> - 查看当前 OSM 群集配置
> - 为 OSM 创建命名空间，以在命名空间中管理已部署的应用程序
> - 加入要由 OSM 管理的命名空间
> - 部署示例应用程序
> - 验证应用程序是否正在 AKS 群集中运行
> - 创建要用作应用程序的入口控制器的 Azure 应用程序网关
> - 通过 Azure 应用程序网关入口在 Internet 上公开服务

### <a name="before-you-begin"></a>开始之前

本文中详细介绍的步骤假定你已创建 AKS 群集（Kubernetes `1.19+` 和更高版本，并且已启用 Kubernetes RBAC），与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），安装了 AKS OSM 附加产品，并且将为入口创建新的 Azure 应用程序网关。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- 使用 Azure CNI 网络（连接到 Azure Vnet）的 AKS 群集（版本 1.19 和更高版本）
- OSM 版本 v0.8.0 或更高版本
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>查看并验证当前 OSM 群集配置

在 AKS 群集上启用适用于 AKS 的 OSM 附加产品后，可以在 osm-config Kubernetes ConfigMap 中查看当前的配置参数。 运行以下命令，查看 ConfigMap 属性：

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

输出将显示群集的当前 OSM 配置。

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

请注意，permissive_traffic_policy_mode 配置为 true。  在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

### <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本教程中，我们将使用具有以下应用程序组件的 OSM bookstore 应用程序：

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，OSM 控制器将能自动将 Envoy 挎斗代理容器与应用程序一起注入。 运行以下命令，加入 OSM bookstore 应用程序命名空间。

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>将 bookstore 应用程序部署到 AKS 群集

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
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

### <a name="update-the-bookbuyer-service"></a>更新 bookbuyer 服务

将 bookbuyer 服务更新为具有以下服务清单的正确入站端口配置。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>验证 bookstore 应用程序是否正在 AKS 群集中运行

到目前为止，我们部署了 bookstore 多容器应用程序，但只能从 AKS 群集中访问它。 稍后我们将添加 Azure 应用程序网关入口控制器，以在 AKS 群集外公开应用程序。 为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 bookbuyer 组件 UI。

首先，让我们获取 bookbuyer Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 bookbuyer Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

获取 Pod 的名称后，现在可以使用 port-forward 命令来设置从本地系统到 AKS 群集中的应用程序的隧道。 运行以下命令，为本地系统端口 8080 设置端口转发。 再次使用特定 bookbuyer Pod 名称。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

应该会看到与下面类似的输出。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 bookbuyer 应用程序 UI，如下图所示。

![用于应用程序网关 的 OSM bookbuyer 应用程序的 UI 图像](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>创建 Azure 应用程序网关，以在 AKS 群集外公开 bookbuyer 应用程序

> [!NOTE]
> 按照以下说明创建用于入口的 Azure 应用程序网关的新实例。 如果要使用现有 Azure 应用程序网关，请跳到有关启用应用程序网关入口控制器附加产品一节。

#### <a name="deploy-a-new-application-gateway"></a>部署新的应用程序网关

> [!NOTE]
> 我们将参考现有文档来为现有 AKS 群集启用应用程序网关入口控制器附加产品。 我们已根据 OSM 材料对现有文档进行了一些修改。 可在[此处](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)找到有关该主题的更详细文档。

现在，你将部署新的应用程序网关，以模拟你有一个现有的应用程序网关且需要使用它对发往 AKS 群集 _myCluster_ 的流量进行负载均衡的情况。 应用程序网关的名称将是 _myApplicationGateway_，但你需要首先创建一个名为 _myPublicIp_ 的公共 IP 资源、一个名为 _myVnet_ 且地址空间为 11.0.0.0/8 的新虚拟网络、一个名为 _mySubnet_ 且地址空间为 11.1.0.0/16 的子网，然后使用 _myPublicIp_ 在 _mySubnet_ 中部署你的应用程序网关。

在不同虚拟网络中使用 AKS 群集和应用程序网关时，两个虚拟网络的地址空间不得重叠。 AKS 群集在其中进行部署的默认地址空间为 10.0.0.0/8，因此，我们将应用程序网关虚拟网络地址前缀设置为 11.0.0.0/8。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 应用程序网关入口控制器 (AGIC) 加载项 **仅** 支持应用程序网关 v2 SKU（标准版和 WAF 版），**不** 支持应用程序网关 v1 SKU。

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>通过 Azure CLI 为现有 AKS 群集启用 AGIC 附加产品

如果要继续使用 Azure CLI，可以继续在已创建的 AKS 群集“myCluster”中启用 AGIC 加载项，并指定该 AGIC 加载项使用已创建的现有应用程序网关“myApplicationGateway” 。

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

可以通过以下命令验证 Azure 应用程序网关 AKS 附加产品是否已启用。

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

此命令的输出应显示 `true`。

#### <a name="peer-the-two-virtual-networks-together"></a>将两个虚拟网络对等互连在一起

由于我们已将 AKS 群集部署在其自己的虚拟网络中，并将应用程序网关部署在另一个虚拟网络中，因此你需要将这两个虚拟网络对等互连在一起，以便流量从应用程序网关流向群集中的 Pod。 将两个虚拟网络对等互连需要两次运行 Azure CLI 命令，以确保连接是双向的。 第一个命令将创建从应用程序网关虚拟网络到 AKS 虚拟网络的对等连接；第二个命令将创建另一个方向的对等连接。

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>在 Internet 上公开 bookbuyer 服务

将以下入口清单应用到 AKS 群集，通过 Azure 应用程序网关在 Internet 上公开 bookbuyer 服务。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

你应会看到以下输出

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

由于入口清单中的主机名是用于测试的伪名称，因此 DNS 名称在 Internet 上将不可用。 或者，我们可以使用 curl 程序，将主机名标头传递到 Azure 应用程序网关公共 IP 地址并接收 200 代码，以确认我们已成功连接到 bookbuyer 服务。

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

你应会看到以下输出

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>疑难解答

- [AGIC 故障排除文档](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [AGIC 的 GitHub 存储库中提供了其他故障排除工具](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>使用 Azure Monitor 和 Azure Application Insights 的 Open Service Mesh (OSM) 监视和可观测性

Azure Monitor 和 Azure Application Insights 都提供用于收集、分析和处理来自云环境与本地环境的遥测数据的综合解决方案，可帮助你将应用程序和服务的可用性和性能最大化。

OSM AKS 附加产品将深度集成到这两个 Azure 服务，并为查看和响应 OSM 指标提供的关键 KPI 提供无缝的 Azure 体验。 有关如何为 OSM AKS 附加产品启用和配置这些服务的详细信息，请访问[适用于 OSM 的 Azure Monitor](https://aka.ms/azmon/osmpreview) 页。

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>教程：手动部署 Prometheus、Grafana 和 Jaeger，查看用于观察状况的 Open Service Mesh (OSM) 指标

> [!WARNING]
> 我们提供 Prometheus、Grafana 和 Jaeger 的安装作为常规指导，以说明如何使用这些工具来查看 OSM 的指标数据。 安装指南不用于生产设置。 请参阅每个工具的文档，了解如何根据需求优化这些安装。 需要注意的最重要一点是缺少永久性存储，这意味着，一旦终止 Prometheus Pod、Grafana Pod 和/或 Jaeger Pod，所有数据都将丢失。

Open Service Mesh (OSM) 将生成与网格中所有流量相关的详细指标。 这些指标使用户能够深入了解网格中的应用程序的行为，从而帮助用户维护和分析应用程序以及进行故障排除。

到目前为止，OSM 直接从挎斗代理 (Envoy) 收集指标。 OSM 为网格中所有服务的传入流量和传出流量提供丰富的指标。 通过这些指标，用户可以获取有关流量总量、流量中的错误和请求的响应时间的信息。

OSM 使用 Prometheus 为网格中运行的所有应用程序收集并存储一致的流量指标和统计信息。 Prometheus 是一个开源监视和警报工具包，在 Kubernetes 环境和服务网格环境（但不限于）中普遍使用。

属于网格一部分的每个应用程序都在 Pod 中运行，其中所包含的 Envoy 挎斗以 Prometheus 格式公开指标（代理指标）。 此外，属于网格一部分的每个 Pod 都具有 Prometheus 注释，使 Prometheus 服务器可以动态抓取应用程序。 只要向网格中添加新的命名空间/Pod/服务，此机制就会自动启用指标抓取。

可以使用开源可视化和分析软件 Grafana 来查看 OSM 指标。 它用于查询指标、可视化指标、针对指标发出警报以及浏览指标。

在本教程中，将：

> [!div class="checklist"]
>
> - 创建并部署 Prometheus 实例
> - 将 OSM 配置为允许 Prometheus 抓取
> - 更新 Prometheus Configmap
> - 创建并部署 Grafana 实例
> - 为 Grafana 配置 Prometheus 数据源
> - 导入用于 Grafana 的 OSM 仪表板
> - 创建并部署 Jaeger 实例
> - 为 OSM 配置 Jaeger 跟踪

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>为 OSM 部署并配置 Prometheus 实例

我们将使用 Helm 来部署 Prometheus 实例。 运行以下命令，通过 Helm 安装 Prometheus：

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

如果安装成功，你应该会看到与下面类似的输出。 请记下 Prometheus 服务器端口和群集 DNS 名称。 稍后会使用这些信息将 Prometheus 配置为 Grafana 的数据源。

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>将 OSM 配置为允许 Prometheus 抓取

要确保为 OSM 组件配置了 Prometheus 抓取，我们需要检查 osm-config 配置文件中的 prometheus_scraping 配置。 使用以下命令查看该配置：

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

如果为 OSM 配置了 Prometheus 抓取，前一个命令的输出应返回 `true`。 如果返回值为 `false`，则需要将配置更新为 `true`。 运行以下命令以启用 OSM Prometheus 抓取：

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

你会看到以下输出。

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>更新 Prometheus Configmap

Prometheus 的默认安装将包含两个 Kubernetes configmap。 可以使用以下命令查看 Prometheus configmap 的列表。

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

我们需要将 stable-prometheus-server configmap 中的 prometheus.yml 配置替换为以下 OSM 配置。 有多种文件编辑方法可用于完成此任务。 一种简单而安全的方法是导出 configmap，为其创建备份副本，然后使用 Visual Studio Code 等编辑器对其进行编辑。

> [!NOTE]
> 如果尚未安装 Visual Studio Code，可以在[此处](https://code.visualstudio.com/Download)下载并安装它。

首先导出 stable-prometheus-server configmap，然后创建备份副本。

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

接下来，让我们使用 Visual Studio Code 打开该文件进行编辑。

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

在 Visual Studio Code 编辑器中打开 configmap 后，将 prometheus.yml 文件替换为以下 OSM 配置并保存文件。

> [!WARNING]
> 务必保留 yaml 文件的缩进结构，这一点非常重要。 对 yaml 文件结构所做的任何更改都可能导致无法重新应用 configmap。

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

使用以下命令应用更新后的 configmap yaml 文件。

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 你可能会收到一条消息，提示缺少所需的 kubernetes 注释。 目前可以忽略该消息。

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>验证是否已将 Prometheus 配置为抓取 OSM 网格和 API 终结点

为了验证 Prometheus 是否已正确配置为抓取 OSM 网格和 API 终结点，我们将使用端口转发来转到 Prometheus Pod 并查看目标配置。 运行以下命令。

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

在浏览器中打开 `http://localhost:9090/targets`

如果向下滚动，你应该会看到所有 SMI 指标终结点状态为 UP，并且会看到已定义的其他 OSM 指标，如下图所示。

![OSM Prometheus 目标指标 UI 图像](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>为 OSM 部署并配置 Grafana 实例

我们将使用 Helm 来部署 Grafana 实例。 运行以下命令，通过 Helm 安装 Grafana：

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

接下来，我们将检索用于登录到 Grafana 站点的默认 Grafana 密码。

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

记下 Grafana 密码。

接下来，我们将检索 Grafana Pod，以使用端口转发来转到用于登录的 Grafana 仪表板。

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

在浏览器中打开 `http://localhost:3000`

在下图所示的登录屏幕中，输入用户名 admin 并使用先前捕获的 Grafana 密码。

![OSM Grafana 登录页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>配置 Grafana Prometheus 数据源

成功登录到 Grafana 后，下一步是添加 Prometheus 作为 Grafana 的数据源。 为此，请导航到左侧菜单上的“配置”图标并选择“数据源”，如下所示。

![OSM Grafana 数据源页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

单击“添加数据源”按钮，然后选择“时序数据库”下的“Prometheus”。

![OSM Grafana 数据源选择页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

在“请在下面配置 Prometheus 数据源”页中，为“HTTP URL 设置”输入 Prometheus 服务的 Kubernetes 群集 FQDN。 默认 FQDN 应是 `stable-prometheus-server.default.svc.cluster.local`。 输入 Prometheus 服务终结点后，滚动到页面底部并选择“保存并测试”。 你应该会看到一个绿色复选框，指示数据源正在工作。

#### <a name="importing-osm-dashboards"></a>导入 OSM 仪表板

OSM 仪表板通过以下两个途径提供：

- [我们的存储库](https://github.com/grafana/grafana)，可通过 Web 管理门户作为 json blob 导入
- [在线提供，网址为 Grafana.com](https://grafana.com/grafana/dashboards/14145)

若要导入仪表板，请在左侧菜单中查找 `+` 符号，然后选择`import`。
可以直接在 `Grafana.com` 上按 ID 导入仪表板。 例如，我们的`OSM Mesh Details`仪表板使用 ID `14145`，你可以直接在表格中使用该 ID 并选择`import`：

![OSM Grafana 仪表板导入页 UI 图像](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

选择“导入”后，系统会自动定向到导入的仪表板。

![OSM Grafana 面板网格详细信息页 UI 图像](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>在适用于 OSM 的 Kubernetes 上部署并配置 Jaeger Operator

[Jaeger](https://www.jaegertracing.io/) 是一个开源跟踪系统，用于监视分布式系统以及对其进行故障排除。 它可以作为新实例与 OSM 一起部署，你也可以创建自己的实例。 按照以下说明将 Jaeger 的新实例部署到 AKS 群集上的 `jaeger` 命名空间。

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>将 Jaeger 部署到 AKS 群集

应用以下清单以安装 Jaeger：

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>为 OSM 附加产品启用跟踪

接下来，我们将需要为 OSM 附加产品启用跟踪。

> [!NOTE]
> 到目前为止，跟踪属性在 osm-config configmap 中不可见。 跟踪属性将在 OSM AKS 附加产品的新版本中可见。

运行以下命令，为 OSM 附加产品启用跟踪：

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>使用端口转发查看 Jaeger UI

Jaeger 的 UI 正在端口 16686 上运行。 若要查看 Web UI，可以使用 kubectl port-forward：

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

在浏览器中，你应该会看到“服务”下拉菜单，可以从中选择由 bookstore 演示部署的各个应用程序。 选择一个服务以查看其中的所有时间跨度。 例如，如果选择“bookbuyer”并为“回溯”选择一小时，将显示该服务与 bookstore v1 和 bookstore v2 的交互并按时间排序。

![OSM Jaeger 跟踪页 UI 图像](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

选择任何项可查看其更多详细信息。 选择多个项可比较跟踪。 例如，你可以实时比较 bookbuyer 在特定时刻与 bookstore v1 和 bookstore v2 的交互。

你还可以选择“系统体系结构”选项卡，查看各个应用程序的交互/通信情况的图表。 该图表可让你了解流量在应用程序之间的流动情况。

![OSM Jaeger“系统体系结构”UI 映像](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS 附加产品故障排除指南

部署 OSM AKS 附加产品时，可能偶尔会遇到问题。 以下指南将帮助你了解如何排查错误并解决常见问题。

### <a name="verifying-and-troubleshooting-osm-components"></a>验证 OSM 组件并对其进行故障排除

#### <a name="check-osm-controller-deployment"></a>检查 OSM 控制器部署

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

正常运行的 OSM 控制器将如下所示：

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>检查 OSM 控制器 Pod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

正常运行的 OSM Pod 将如下所示：

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

即使在某个时间点逐出了一个控制器，也有另一个控制器的 READY 列显示 1/1、STATUS 列显示 Running 且 RESTARTS 列显示 0。 如果 READY 列并非显示 1/1，则表示服务网格处于中断状态。
如果 READY 列显示 0/1，表示控制平面容器崩溃，我们需要获取日志。 请参阅下面的“从 Azure 支持中心获取 OSM 控制器日志”一节。 如果 READY 列下的 / 后面显示大于 1 的数字，表示安装了挎斗。 OSM 控制器很可能无法与附加到它的任何挎斗配合工作。

> [!NOTE]
> 从版本 v0.8.2 开始，OSM 控制器不在 HA 模式下运行，并且将在已部署的单个 Pod 中运行（副本计数为 1）。 Pod 具有运行状况探测，并将由 kubelet 根据需要重启。

#### <a name="check-osm-controller-service"></a>检查 OSM 控制器服务

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

正常运行的 OSM 控制器服务将如下所示：

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> CLUSTER-IP 将有所不同。 服务的 NAME 和 PORT(S) 必须与上面的示例相同。

#### <a name="check-osm-controller-endpoints"></a>检查 OSM 控制器终结点

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

正常运行的 OSM 控制器终结点将如下所示：

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>检查 OSM 注入程序部署

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常运行的 OSM 注入程序部署将如下所示：

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>检查 OSM 注入程序 Pod

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常运行的 OSM 注入程序 Pod 将如下所示：

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>检查 OSM 注入程序服务

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

正常运行的 OSM 注入程序服务将如下所示：

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>检查 OSM 终结点

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

正常运行的 OSM 终结点将如下所示：

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>检查验证 Webhook 和改变 Webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

正常运行的 OSM 验证 Webhook 将如下所示：

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

正常运行的 OSM 改变 Webhook 将如下所示：

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>检查验证 Webhook 的服务和 CA 捆绑包

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

正确配置的验证 Webhook 配置将如下所示：

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>检查改变 Webhook 的服务和 CA 捆绑包

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

正确配置的改变 Webhook 配置将如下所示：

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>检查 OSM 控制器是否为验证（或改变）Webhook 提供了 CA 捆绑包

> [!NOTE]
> 必须知道的一点是：从 v0.8.2 开始，AKS RP 将安装验证 Webhook，AKS 协调器会确保它存在，而 CA 捆绑包由 OSM 控制器填充。

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

此数字表示字节数或 CA 捆绑包的大小。 如果此值为空 (0)，或者为小于 1000 的某个数字，则表示未正确预配 CA 捆绑包。 如果没有正确的 CA 捆绑包，验证 Webhook 将发生错误，并禁止用户更改 kube-system 命名空间中的 osm-config ConfigMap。

CA 捆绑包不正确时的示例错误：

- 尝试更改 osm-config ConfigMap：

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- 错误：

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

验证 Webhook 配置具有错误的证书时的解决方法：

- 方案 1 - 重启 OSM 控制器：这将重启 OSM 控制器。 启动时，它将覆盖改变 Webhook 和验证 Webhook 的 CA 捆绑包。

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- 方案 2 - 删除验证 Webhook：如果删除验证 Webhook，会使 `osm-config` ConfigMap 的改变不再经过验证。 将进行任何修补。 AKS 协调器将在某个时间点确保验证 Webhook 存在，并将重新创建它。 可能必须重启 OSM 控制器，以快速重写 CA 捆绑包。

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- 方案 3 - 删除并修补：以下命令将删除验证 Webhook 以使我们能够添加任何值，并且会立即尝试应用补丁。 AKS 协调器很可能没有足够的时间来协调并还原验证 Webhook，我们可采取最后的措施是应用更改：

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>检查 `osm-config` ConfigMap

> [!NOTE]
> OSM 控制器不要求 kube-system 命名空间中存在 `osm-config` ConfigMap。 控制器具有合理的默认配置值，即使没有该 ConfigMap 也能正常运行。

检查该 ConfigMap 是否存在：

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

检查 osm-config ConfigMap 的内容

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap 值：

| 密钥                              | 类型   | 允许的值                                          | 默认值                          | 函数                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| egress                           | bool   | true、false                                             | `"false"`                              | 在网格中启用出口。                                                                                                                                                                                                             |
| enable_debug_server              | bool   | true、false                                             | `"true"`                               | 在 osm-controller Pod 上启用调试终结点，以列出有关网格的信息（例如代理连接、证书和 SMI 策略）。                                                                                    |
| enable_privileged_init_container | bool   | true、false                                             | `"false"`                              | 为网格中的 Pod 启用特权 Init 容器。 如果设置为 false，Init 容器只有 NET_ADMIN。                                                                                                                                   |
| envoy_log_level                  | string | trace、debug、info、warning、warn、error、critical 和 off | `"error"`                              | 设置 Envoy 代理挎斗的日志记录详细程度，仅适用于加入网格的新创建的 Pod。 若要更新现有 Pod 的日志级别，请使用 `kubectl rollout restart` 重启部署。                            |
| outbound_ip_range_exclusion_list | string | 以逗号分隔的 IP 范围列表，格式为 a.b.c.d/x | `-`                                    | 要从挎斗代理的出站流量截获中排除的 IP 地址范围的全局列表。                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | true、false                                             | `"false"`                              | 如果设置为 `true`，则会在网格中启用“全部允许”模式，即不在网格中强制执行流量策略。 如果设置为 `false`，则会在网格中启用“全部拒绝”流量策略，即服务需要 `SMI Traffic Target`才能进行通信。 |
| prometheus_scraping              | bool   | true、false                                             | `"true"`                               | 在挎斗代理上启用 Prometheus 指标抓取。                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24h、1h30m（任何持续时间）                          | `"24h"`                                | 设置服务证书有效期，以十进制数字的序列表示，其中每个数字都有可选的分数和单位后缀。                                                                                             |
| tracing_enable                   | bool   | true、false                                             | `"false"`                              | 为网格启用 Jaeger 跟踪。                                                                                                                                                                                                    |
| tracing_address                  | string | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Jaeger 部署的地址（如果已启用跟踪）。                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/spans                                           | /api/v2/spans                          | 跟踪数据的终结点（如果已启用跟踪）。                                                                                                                                                                                          |
| tracing_port                     | int    | 任何非零整数值                              | `"9411"`                               | 启用跟踪的端口。                                                                                                                                                                                                       |
| use_https_ingress                | bool   | true、false                                             | `"false"`                              | 在网格上启用 HTTPS 入口。                                                                                                                                                                                                      |
| config_resync_interval           | string | 如果值小于 1 分钟，将禁用此功能                            | 0（禁用）                           | 如果提供的值大于 1m (60s)，则 OSM 控制器会按给定的时间间隔将所有可用的配置发送到每个已连接的 Envoy                                                                                                    |

#### <a name="check-namespaces"></a>检查命名空间

> [!NOTE]
> kube-system 命名空间永远不会加入服务网格，并且永远不会使用下面的键/值进行标记和/或标注。

可以使用 `osm namespace add` 命令将命名空间加入到给定的服务网格。
如果 k8s 命名空间是网格的一部分（或者要使其成为网格的一部分），则必须满足以下条件：

使用以下命令查看注释

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

必须存在以下注释：

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

使用以下命令查看标签

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

必须存在以下标签：

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

如果命名空间不带有 `"openservicemesh.io/sidecar-injection": "enabled"` 注释或 `"openservicemesh.io/monitored-by": "osm"` 标签，则 OSM 注入程序不会添加 Envoy 挎斗。

> 注意：只有在调用 `osm namespace add` 之后，新的 Pod 才会与 Envoy 挎斗一起注入。 必须使用 `kubectl rollout restart deployment ...` 重启现有 Pod

#### <a name="verify-the-smi-crds"></a>验证 SMI CRD：

检查群集是否具有所需的 CRD：

```azurecli-interactive
kubectl get crds
```

必须在群集上安装以下各项：

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

使用以下命令获取已安装的 CRD 版本：

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

预期输出：

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM 控制器 v0.8.2 需要以下版本：

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io-不支持
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

如果缺少 CRD，请使用以下命令在群集上安装它们：

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>为 AKS 群集禁用 Open Service Mesh (OSM) 附加产品

若要禁用 OSM 附加产品，请运行以下命令：

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
