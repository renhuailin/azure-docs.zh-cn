---
title: 排查已启用 Azure Arc 的 Kubernetes 的常见问题
services: azure-arc
ms.service: azure-arc
ms.date: 05/21/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 排查已启用 Azure Arc 的 Kubernetes 群集的常见问题。
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: f6f29b30f3a62653c032b7aae40cac5afdcf96b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546502"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>已启用 Azure Arc 的 Kubernetes 故障排除

本文档提供了连接性、权限和代理问题的故障排除指南。

## <a name="general-troubleshooting"></a>常规故障排除

### <a name="azure-cli"></a>Azure CLI

在使用 `az connectedk8s` 或 `az k8s-configuration` CLI 命令之前，请检查 Azure CLI 是否设置为针对正确的 Azure 订阅工作。

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 的所有代理在 `azure-arc` 命名空间中部署为 Pod。 所有 Pod 都应该在运行并传递它们的健康状况检查。

首先，请验证 Azure Arc helm 版本：

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

如果找不到或缺少 Helm 版本，请再次尝试[将群集连接到 Azure Arc](./quickstart-connect-cluster.md)。

如果 Helm 版本存在并且 `STATUS: deployed`，请使用 `kubectl` 来检查代理的状态：

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

所有 Pod 都应当将 `STATUS` 显示为 `Running`，并且 `READY` 列下为 `3/3` 或 `2/2`。 提取日志并描述返回 `Error` 或 `CrashLoopBackOff` 的 Pod。 如果任何 Pod 停滞在 `Pending` 状态，则表明群集节点上的资源可能不足。 [纵向扩展群集](https://kubernetes.io/docs/tasks/administer-cluster/)可以使这些 Pod 转换为 `Running` 状态。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>将 Kubernetes 群集连接到 Azure Arc

将群集连接到 Azure 需要对 Azure 订阅的访问权限和对目标群集的 `cluster-admin` 访问权限。 如果你无法访问该群集或权限不足，那么在将群集连接到 Azure Arc 时就会失败。

### <a name="azure-cli-is-unable-to-download-helm-chart-for-azure-arc-agents"></a>Azure CLI 无法下载 Azure Arc 代理的 Helm 图表

如果你使用的是 Helm 版本 3.7.0 或更高版本，则在运行 `az connectedk8s connect` 以将集群连接到 Azure Arc 时会遇到以下错误：

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest

Unable to pull helm chart from the registry 'mcr.microsoft.com/azurearck8s/batch1/stable/azure-arc-k8sagents:1.4.0': Error: unknown command "chart" for "helm"
Run 'helm --help' for usage.
```

在这种情况下，你将需要安装先前版本的 [Helm 3](https://helm.sh/docs/intro/install/)，其中版本 &lt; 3.7.0。 之后，再次运行 `az connectedk8s connect` 命令以将群集连接到 Azure Arc。

### <a name="insufficient-cluster-permissions"></a>群集权限不足

如果提供的 kubeconfig 文件没有足够的权限来安装 Azure Arc 代理，则 Azure CLI 命令将会返回错误。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

将群集连接到 Azure Arc 的用户应该在群集上分配有 `cluster-admin` 角色。


### <a name="unable-to-connect-openshift-cluster-to-azure-arc"></a>无法将 OpenShift 群集连接到 Azure Arc

如果在将 OpenShift 群集连接到 Azure Arc 时 `az connectedk8s connect` 超时并失败，请检查以下各项：

1. OpenShift 群集需要满足版本先决条件：4.5.41+、4.6.35+ 或 4.7.18+。

1. 在运行 `az connectedk8s connnect` 之前，需要在群集上运行以下命令：

    ```console
    oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
    ```

### <a name="installation-timeouts"></a>安装超时

将 Kubernetes 群集连接到已启用 Azure Arc 的 Kubernetes 需要在群集上安装 Azure Arc 代理。 如果该群集通过网速缓慢的 Internet 连接运行，则代理的容器映像拉取可能会需要比 Azure CLI 超时更长的时间。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm 问题

Helm `v3.3.0-rc.1` 版本存在一个[问题](https://github.com/helm/helm/pull/8527)，即，helm install/upgrade（由 `connectedk8s` CLI 扩展使用）导致运行所有挂钩，出现以下错误：

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

如果要从此问题恢复，请执行以下步骤：

1. 在 Azure 门户中删除已启用 Azure Arc 的 Kubernetes 资源。
2. 在计算机上运行以下命令：
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 在计算机上[安装 Helm 3 的稳定版本](https://helm.sh/docs/intro/install/)，而不是候选发布版本。
4. 使用适当的值运行 `az connectedk8s connect` 命令，以将该群集连接到 Azure Arc。

## <a name="configuration-management"></a>配置管理

### <a name="general"></a>常规
为了帮助排查与配置资源相关的问题，请运行 az 命令，同时指定 `--debug` 参数。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>创建配置

已启用 Azure Arc 的 Kubernetes 资源上的写入权限 (`Microsoft.Kubernetes/connectedClusters/Write`) 对于在该群集上创建配置来说必要且充分。

### <a name="configuration-remains-pending"></a>配置保留 `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

## <a name="monitoring"></a>监视

适用于容器的 Azure Monitor 需要在特权模式下运行它的 DaemonSet。 如果要成功设置用于监视的 Canonical Charmed Kubernetes 群集，请运行以下命令：

```console
juju config kubernetes-worker allow-privileged=true
```

## <a name="enable-custom-locations-using-service-principal"></a>使用服务主体启用自定义位置

将群集连接到 Azure Arc 或在现有群集上启用自定义位置功能时，可能会看到以下警告：

```console
Unable to fetch oid of 'custom-locations' app. Proceeding without enabling the feature. Insufficient privileges to complete the operation.
```

使用服务主体登录到 Azure 并且此服务主体没有权限获取 Azure Arc 服务所使用应用程序的信息时，将看到上述警告。 若要避免此错误，请执行以下步骤：

1. 获取 Azure Arc 服务使用的 Azure AD 应用程序的对象 ID：

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 使用上面步骤中的 `<objectId>` 值在群集上启用自定义位置功能：
    - 如果在将群集连接到 Arc 时要启用自定义位置功能，请运行以下命令：

        ```console
        az connectedk8s connect -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId>   
        ```

    - 如果在现有的启用了 Azure Arc 的 Kubernetes 群集上启用自定义位置功能，请运行以下命令：

        ```console
        az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
        ```

授予上述权限后，现在可以继续在群集上[启用自定义位置功能](custom-locations.md#enable-custom-locations-on-cluster)。

## <a name="azure-arc-enabled-open-service-mesh"></a>启用了 Azure Arc 的开放式服务网格

以下故障排除步骤提供了有关验证群集上所有开放服务网格扩展组件的部署的指南。

### <a name="1-check-osm-controller-deployment"></a>1. 检查 OSM 控制器部署
```bash
kubectl get deployment -n arc-osm-system --selector app=osm-controller
```

如果 OSM 控制器运行正常，获得的输出将如下所示：
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="2-check-the-osm-controller-pod"></a>2. 检查 OSM 控制器 Pod
```bash
kubectl get pods -n arc-osm-system --selector app=osm-controller
```

如果 OSM 控制器运行正常，获得的输出将如下所示：
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

即使我们的一个控制器在某个时间点被逐出，也会有另一个控制器（即 `READY 1/1` 和 `Running` 为 `0` 的控制器）重新启动。
如果 `READY` 列并非显示 `1/1`，则表示服务网格处于中断状态。
如果 `READY` 列显示 `0/1`，表示控制平面容器崩溃，我们需要获取日志。 请参阅下面的`Get OSM Controller Logs from Azure Support Center`部分。
如果 `READY` 列下的 `/` 后面显示大于 1 的数字，表示安装了挎斗。 OSM 控制器很可能无法与附加到它的任何挎斗配合工作。

### <a name="3-check-osm-controller-service"></a>3. 检查 OSM 控制器服务
```bash
kubectl get service -n arc-osm-system osm-controller
```

如果 OSM 控制器运行正常，你将获得以下输出：
```
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> 注意：`CLUSTER-IP` 将有所不同。 服务 `NAME` 和 `PORT(S)` 必须与输出中所示的相同。

### <a name="4-check-osm-controller-endpoints"></a>4. 检查 OSM 控制器终结点：
```bash
kubectl get endpoints -n arc-osm-system osm-controller
```

如果 OSM 控制器运行正常，获得的输出将如下所示：
```
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

如果用户的群集针对 `osm-controller` 不包含 `ENDPOINTS`，则表明控制平面运行不正常。 原因可能是 OSM 控制器 pod 崩溃，或者从未正确部署。

### <a name="5-check-osm-injector-deployment"></a>5. 检查 OSM 注入程序部署
```bash
kubectl get deployments -n arc-osm-system osm-injector
```

如果 OSM 注入程序运行正常，获得的输出将如下所示：
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
osm-injector   1/1     1            1           73m
```

### <a name="6-check-osm-injector-pod"></a>6. 检查 OSM 注入程序 Pod
```bash
kubectl get pod -n arc-osm-system --selector app=osm-injector
```

如果 OSM 注入程序运行正常，获得的输出将如下所示：
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

`READY` 列必须是 `1/1`。 任何其他值都表示 osm-injector pod 运行不正常。

### <a name="7-check-osm-injector-service"></a>7. 检查 OSM 注入程序服务
```bash
kubectl get service -n arc-osm-system osm-injector
```

如果 OSM 注入程序运行正常，获得的输出将如下所示：
```
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

确保对于 `osm-injector` 服务列出的 IP 地址为 `9090`。 不应该有 `EXTERNAL-IP`。

### <a name="8-check-osm-injector-endpoints"></a>8. 检查 OSM 注入器终结点
```bash
kubectl get endpoints -n arc-osm-system osm-injector
```

如果 OSM 注入程序运行正常，获得的输出将如下所示：
```
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

要使 OSM 正常工作，`osm-injector` 必须至少有一个终结点。 OSM 注入器终结点的 IP 地址将有所不同。 `9090` 端口必须相同。


### <a name="9-check-validating-and-mutating-webhooks"></a>9. 检查“验证”Webhook 和“变异”Webhook： 
```bash
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

如果“验证”Webhook 运行正常，获得的输出将如下所示：
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      81m
```

```bash
kubectl get MutatingWebhookConfiguration --selector app=osm-controller
```


如果“变异”Webhook 运行正常，获得的输出将如下所示：
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      102m
```

检查“验证”Webhook 的服务和 CA 捆绑包：
```
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

已正确配置的“验证”Webhook 配置具有以下输出：
```json
{
  "name": "osm-config-validator",
  "namespace": "arc-osm-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

检查“变异”Webhook 的服务和 CA 捆绑包：
```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

已正确配置的“变异”Webhook 配置具有以下输出：
```
{
  "name": "osm-injector",
  "namespace": "arc-osm-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```


使用以下命令来检查 OSM 控制器是否为验证（或改变）Webhook 提供了 CA 捆绑包：

```bash
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

示例输出：
```bash
1845
```
输出中的这个数字表示字节数或 CA 捆绑包的大小。 如果这个数字为空、0 或低于 1000 的数字，则表示 CA 包没有正确预配。 如果没有正确的 CA 捆绑包，“验证”Webhook 将引发错误，并禁止你对 `arc-osm-system` 命名空间中的 `osm-config` ConfigMap 进行更改。

我们来看看 CA 捆绑包不正确时的错误示例：
- 尝试更改 `osm-config` ConfigMap：
  ```bash
  kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```
- 错误输出：
  ```bash
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.arc-osm-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

“验证”Webhook 配置具有错误的证书时，请使用以下解决方法之一：
- 选项 1. 重启 OSM 控制器 - 这将重启 OSM 控制器。 启动时，它将覆盖改变 Webhook 和验证 Webhook 的 CA 捆绑包。
  ```bash
  kubectl rollout restart deployment -n arc-osm-system osm-controller
  ```

- 选项 2. 删除“验证”Webhook - 如果删除验证 Webhook，会使 `osm-config` ConfigMap 的改变不再经过验证。 将进行任何修补。 可能必须重启 OSM 控制器，以快速重写 CA 捆绑包。
   ```bash
   kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm
   ```

- 选项 3. 删除并修补：以下命令将删除验证 Webhook 以使你能够添加任何值，并且会立即尝试应用补丁
  ```bash
  kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm; kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```


### <a name="10-check-the-osm-config-configmap"></a>10. 检查 `osm-config` ConfigMap

>[!Note]
>OSM 控制器不要求 `arc-osm-system` 命名空间中存在 `osm-config` ConfigMap。 控制器具有合理的默认配置值，即使没有该 ConfigMap 也能正常运行。

检查该 ConfigMap 是否存在：
```bash
kubectl get ConfigMap -n arc-osm-system osm-config
```

检查 `osm-config` ConfigMap 的内容：
```bash
kubectl get ConfigMap -n arc-osm-system osm-config -o json | jq '.data'     
```
将显示以下输出：
```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false",
}
```

若要了解 `osm-config` ConfigMap 值，请参阅 [OSM ConfigMap 文档](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)。

### <a name="11-check-namespaces"></a>11. 检查命名空间

>[!Note]
>arc-osm-system 命名空间永远不会加入服务网格，并且永远不会使用下面的键/值进行标记和/或标注。

可以使用 `osm namespace add` 命令将命名空间加入到给定的服务网格。
如果 kubernetes 命名空间是网格的一部分，则必须满足以下条件：

查看命名空间 `bookbuyer` 的注释：
```bash
kc get namespace bookbuyer -o json | jq '.metadata.annotations'
```

必须存在以下注释：
```
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```


查看命名空间 `bookbuyer` 的标签：
```bash
kc get namespace bookbuyer -o json | jq '.metadata.labels'
```

必须存在以下标签：
```
{
  "openservicemesh.io/monitored-by": "osm"
}
```
请注意，如果未使用 `osm` CLI，也可以手动将这些注释添加到命名空间。 如果命名空间不带有 `"openservicemesh.io/sidecar-injection": "enabled"` 注释或 `"openservicemesh.io/monitored-by": "osm"` 标签，则 OSM 注入程序不会添加 Envoy 挎斗。

>[!Note]
>只有在调用 `osm namespace add` 之后，新的 Pod 才会与 Envoy 挎斗一起注入。 必须使用 `kubectl rollout restard deployment` 命令重启现有 Pod。


### <a name="12-verify-the-smi-crds"></a>12. 验证 SMI CRD
检查群集是否具有所需的 CRD：
```bash
kubectl get crds
```

确保 CRD 与相同的 OSM 上游版本相对应。 例如 如果使用的是 v0.8.4，请确保 CRD 与 [OSM OSS 项目](https://docs.openservicemesh.io/)的版本分支 0.8.4 中可用的 CRD 匹配。 请参阅 [OSM 发行说明](https://github.com/openservicemesh/osm/releases)。

使用以下命令获取已安装的 CRD 版本：
```bash
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

如果缺少 CRD，请使用以下命令在群集上安装它们。 确保替换命令中的版本。
```bash
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/access.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/specs.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/split.yaml
```
