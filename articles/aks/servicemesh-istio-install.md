---
title: 在 Azure Kubernetes 服务 (AKS) 中安装 Istio
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和使用 Istio 来创建服务网格
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683822"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中安装和使用 Istio

[Istio][istio-github] 是跨 Kubernetes 群集中的微服务提供关键功能集的开源服务网格。 这些功能包括流量管理、服务标识和安全性、策略执行以及可观察性。 有关 Istio 的详细信息，请参阅官方文档[什么是 Istio？][istio-docs-concepts]。

本文介绍如何安装 Istio。 Istio `istioctl` 客户端二进制文件已安装到客户端计算机上，Istio 组件将安装到 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 以下说明引用 Istio 版本 `1.7.3`。
>
> Istio 团队已针对 Kubernetes 版本 `1.16+` 测试了 Istio `1.7.x` 发行版。 可以在 [GitHub - Istio 版本][istio-github-releases]中找到其他 Istio 版本，在 [Istio 新闻][istio-release-notes]中找到有关每个版本的信息，在[一般的 Istio 常见问题解答][istio-faq]中找到支持的 Kubernetes 版本。

在本文中，学习如何：

> [!div class="checklist"]
> * 下载并安装 Istio istioctl 客户端二进制文件
> * 在 AKS 上安装 Istio
> * 验证 Istio 安装
> * 访问加载项
> * 从 AKS 中卸载 Istio

## <a name="before-you-begin"></a>准备阶段

本文中详细介绍的步骤假设你已创建 AKS 群集 (Kubernetes `1.16` 和更高版本，启用 KUBERNETES RBAC) 并已建立 `kubectl` 与群集的连接。 如果需要帮助完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]。

确保你已阅读 [Istio 性能和可伸缩性](https://istio.io/docs/concepts/performance-and-scalability/)文档，以了解在 AKS 群集中运行 Istio 时的其他资源要求。 核心和内存要求将因特定工作负荷而异。 选择适当数量的节点和 VM 大小以适合你的设置。

本文将 Istio 安装指南分为多个独立步骤。 最终结果的结构与官方 Istio 安装[指南][istio-install-istioctl]相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>在 AKS 上安装 Istio Operator

Istio 提供了一个 [Operator][istio-install-operator]，用于管理 AKS 群集中 Istio 组件的安装和更新。 我们将使用 `istioctl` 客户端二进制文件来安装 Istio Operator。

```bash
istioctl operator init
```

你应该会看到类似于以下输出的内容，从而可以确认已经安装了 Istio Operator。

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio Operator 会安装到 `istio-operator` 命名空间中。 请查询该命名空间。

```bash
kubectl get all -n istio-operator
```

你应该会看到已经部署了以下组件。

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

可以通过 [kubernetes.io][kubernetes-operator] 来详细了解 Operator 模式以及该模式如何帮助自动执行复杂任务。


### <a name="install-istio-components"></a>安装 Istio 组件

我们已经在 AKS 群集中成功安装了 Istio Operator，现在来安装 Istio 组件。 

我们将利用 `default` [Istio 配置文件][istio-configuration-profiles]来构建 [Istio Operator 规范][istio-control-plane]。

可以运行下面的 `istioctl` 命令来查看 `default` Istio 配置文件的配置。

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio 目前必须计划在 Linux 节点上运行。 如果群集中有 Windows Server 节点，则必须确保 Istio Pod 仅计划在 Linux 节点上运行。 我们将使用[节点选择器][kubernetes-node-selectors]来确保将 Pod 安排到正确的节点。

> [!CAUTION]
> [Istio CNI][istio-feature-cni] Istio 功能目前以 [Alpha][istio-feature-stages] 版提供，因此，在启用这些功能之前应该谨慎。 

使用以下内容创建名为 `istio.aks.yaml` 的文件。 此文件将保留 [Istio Operator 规范][istio-control-plane]以用于配置 Istio。

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

请创建 `istio-system` 命名空间，并将 Istio Operator 规范部署到该命名空间。 Istio Operator 将会监视 Istio Operator 规范，并且将会使用该规范在 AKS 群集中安装和配置 Istio。

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

此时，已将 Istio 部署到 AKS 群集。 为确保成功部署 Istio，让我们转到[验证 Istio 安装](#validate-the-istio-installation)部分。

## <a name="validate-the-istio-installation"></a>验证 Istio 安装

请查询 `istio-system` 命名空间，Istio Operator 已在其中安装了 Istio 和附加产品组件：

```bash
kubectl get all -n istio-system
```

你应该会看到以下组件：

- `istio*` - Istio 组件
- `jaeger-*`、`tracing` 和 `zipkin` - 用于跟踪的附加产品
- `prometheus` - 指标附加产品
- `grafana` - 分析和监视仪表板附加产品
- `kiali` - 服务网格仪表板附加产品

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

也可以通过监视 Istio Operator 的日志来进一步了解安装情况。

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

如果 `istio-ingressgateway` 显示 `<pending>` 的外部 IP，请在 Azure 网络分配 IP 地址之前等待几分钟。

所有 pod 应显示 `Running` 状态。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 Pod 报告问题，请使用 [kubectl describe pod][kubectl-describe] 命令查看其输出和状态。

## <a name="accessing-the-add-ons"></a>访问加载项

Istio Operator 安装了多种提供附加功能的附加产品。 加载项 Web 应用程序 **不** 通过外部 IP 地址公开。 

若要访问加载项用户界面，请使用 `istioctl dashboard` 命令。 此命令使用 [kubectl port-forward][kubectl-port-forward] 和一个随机端口在客户端计算机与 AKS 群集中的相关 Pod 之间建立安全连接。 然后，它会在默认浏览器中自动打开加载项 Web 应用程序。

### <a name="grafana"></a>Grafana

Istio 的分析和监视仪表板由 [Grafana][grafana] 提供。 出现提示时，请记得使用前面通过 Grafana 机密创建的凭据。 以安全方式打开 Grafana 仪表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio 的指标由 [Prometheus][prometheus] 提供。 以安全方式打开 Prometheus 仪表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 中的跟踪由 [Jaeger][jaeger] 提供。 以安全方式打开 Jaeger 仪表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服务网格可观察性仪表板由 [Kiali][kiali] 提供。 出现提示时，请记得使用前面通过 Kiali 机密创建的凭据。 以安全方式打开 Kiali 仪表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

提供一个简单的连接 [Envoy][envoy] 代理的接口。 它提供 Envoy 代理在指定 Pod 中运行所需的配置信息和指标。 以安全方式打开 Envoy 接口，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>从 AKS 中卸载 Istio

> [!WARNING]
> 从正在运行的系统中删除 Istio 可能会导致服务之间出现流量相关的问题。 在继续之前，请确保对系统进行预配，以便在没有 Istio 的情况下系统仍可正常运行。

### <a name="remove-istio"></a>删除 Istio

若要从 AKS 群集中删除 Istio，请删除我们在之前的操作中添加的名为 `istio-control-plane` 的 `IstioOperator` 资源。 Istio Operator 将会识别出 Istio Operator 规范已删除，然后会删除所有关联的 Istio 组件。

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

删除了所有 Istio 组件后，可运行以下命令进行检查。

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>删除 Istio Operator

成功卸载了 Istio 之后，还可以删除 Istio Operator。

```bash
istioctl operator remove
```

最后，请删除 `istio-` 命名空间。

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>后续步骤

若要了解 Istio 的更多安装和配置选项，请参阅以下官方 Istio 指南：

- [Istio - 安装指南][istio-installation-guides]

也可以使用以下示例应用程序按照其他方案操作：

- [Istio Bookinfo 应用程序示例][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
