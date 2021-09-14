---
title: 对 Open Service Mesh 进行故障排除
description: 如何对 Open Service Mesh 进行故障排除
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 0394815b4e75fb8d1f1f277010602839dc6876eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439796"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS 附加产品故障排除指南

部署 OSM AKS 加载项时，可能会遇到与服务网格配置相关的问题。 以下指南将帮助你了解如何排查错误并解决常见问题。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>验证 OSM 组件并对其进行故障排除

### <a name="check-osm-controller-deployment"></a>检查 OSM 控制器部署

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

正常运行的 OSM 控制器将如下所示：

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>检查 OSM 控制器 Pod

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

### <a name="check-osm-controller-service"></a>检查 OSM 控制器服务

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

### <a name="check-osm-controller-endpoints"></a>检查 OSM 控制器终结点

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

正常运行的 OSM 控制器终结点将如下所示：

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>检查 OSM 注入程序部署

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常运行的 OSM 注入程序部署将如下所示：

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>检查 OSM 注入程序 Pod

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常运行的 OSM 注入程序 Pod 将如下所示：

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>检查 OSM 注入程序服务

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

正常运行的 OSM 注入程序服务将如下所示：

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>检查 OSM 终结点

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

正常运行的 OSM 终结点将如下所示：

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>检查验证 Webhook 和改变 Webhook

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

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>检查验证 Webhook 的服务和 CA 捆绑包

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

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>检查改变 Webhook 的服务和 CA 捆绑包

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

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>检查 OSM 控制器是否为验证（或改变）Webhook 提供了 CA 捆绑包

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

### <a name="check-the-osm-mesh-config-resource"></a>检查 `osm-mesh-config` 资源

检查该 ConfigMap 是否存在：

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

检查 OSM MeshConfig 的内容

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

```
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

`osm-mesh-config` 资源值：

| 密钥 | 类型 | 默认值 | Kubectl Patch 命令示例 |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | string | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>检查命名空间

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

> [!NOTE]
> 只有在调用 `osm namespace add` 之后，新的 Pod 才会与 Envoy 挎斗一起注入。 必须使用 `kubectl rollout restart deployment ...` 重启现有 Pod

### <a name="verify-the-smi-crds"></a>验证 SMI CRD：

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