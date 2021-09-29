---
title: 在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 托管标识（预览）
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 AAD Pod 托管标识
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: df893949214fc73813bb1b45a663f052ae3ed3c8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124829081"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 托管标识（预览）

Azure Active Directory Pod 托管标识使用 Kubernetes 基元将[Azure 资源标识托管][az-managed-identities]和 Azure Active Directory (AAD) 中的标识与 Pod 关联到一起。 管理员创建 Kubernetes 基元形式的标识和绑定，使 Pod 能够以标识提供者的身份访问依赖于 AAD 的 Azure 资源。

> [!NOTE]
>本文档中介绍的 Pod 托管标识（预览）功能将被替换为 Pod 托管标识 V2（预览）。
> 如果已经安装 AADPODIDENTITY，那么将有一个适用于 V2 的迁移选项。 随着 2022 年第 2 季度的公共预览版的发布，将提供有关迁移的更多详细信息。 启用此功能意味着不需要 MIC 组件。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>准备阶段

必须安装了以下资源：

* Azure CLI 2.20.0 或更高版本
* `aks-preview` 扩展 0.5.5 或更高版本

### <a name="limitations"></a>限制

* 对于一个群集，最多允许 200 个 Pod 标识。
* 对于一个群集，最多允许 200 个 Pod 标识例外。
* 只能在 Linux 节点池中使用 Pod 托管标识。

### <a name="register-the-enablepodidentitypreview"></a>注册 `EnablePodIdentityPreview`

注册 `EnablePodIdentityPreview` 功能：

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>安装 `aks-preview` Azure CLI

还需要 aks-preview Azure CLI 扩展 0.5.5 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>使用 Azure 容器网络接口 (CNI) 创建 AKS 群集

> [!NOTE]
> 这是建议的默认配置

创建使用 Azure CNI 并已启用 Pod 托管标识的 AKS 群集。 以下命令使用 [az group create][az-group-create] 创建名为 *myResourceGroup* 的资源组，并使用 [az aks create][az-aks-create] 命令在 *myResourceGroup* 资源组中创建名为 *myAKSCluster* 的 AKS 群集。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```
> [!NOTE]
> Azure Active Directory Pod 标识支持 2 种操作模式：
> 
> 1. 标准模式：在此模式下，将以下 2 个组件部署到 AKS 群集： 
>     * [托管标识控制器 (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/)：一种 Kubernetes 控制器，通过 Kubernetes API 服务器监视对 Pod、[AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 和 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) 所做的更改。 检测到相关更改时，MIC 会根据需要添加或删除 [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/)。 具体而言，如果计划了 Pod，MIC 会将 Azure 上的托管标识分配给节点池在创建阶段使用的基础 VMSS。 删除所有使用该标识的 Pod 时，MIC 会从节点池的 VMSS 中删除标识，除非其他 Pod 使用相同的托管标识。 创建或删除 AzureIdentity 或 AzureIdentityBinding 时，MIC 会执行类似的操作。
>     * [节点托管标识 (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/)：是在 AKS 群集中每个节点上作为 DaemonSet 运行的 Pod。 NMI 截获每个节点上对 [Azure 实例元数据服务](../virtual-machines/linux/instance-metadata-service.md?tabs=linux)的安全令牌请求，将这些请求重定向到自身并验证 Pod 是否有权访问它为其请求令牌的标识，并代表应用程序从 Azure Active Directory 租户中提取令牌。
> 2. 托管模式：在此模式下，只有 NMI。 标识需要由用户手动分配和管理。 有关详细信息，请参阅[托管模式下的 Pod 标识](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/)。
>
>如[安装指南](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/)中所示通过 Helm 图表或 YAML 清单安装 Azure Active Directory Pod 标识时，可以在 `standard` 和 `managed` 模式之间进行选择。 如果决定改为如本文所述使用 AKS 群集加载项安装 Azure Active Directory Pod 标识，安装程序将使用 `managed` 模式。

使用 [az aks get-credentials][az-aks-get-credentials] 登录到 AKS 群集。 此命令还会在开发计算机上下载并配置 `kubectl` 客户端证书。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>更新使用 Azure CNI 的现有 AKS 群集

更新使用 Azure CNI 的现有 AKS 群集以包含 Pod 托管标识。

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>将 Kubenet 网络插件与 Azure Active Directory Pod 托管标识配合使用 

> [!IMPORTANT]
> 在使用 Kubenet 的群集中运行 aad-pod-identity 不是建议的配置，因为这会给安全造成影响。 在使用 Kubenet 的群集中启用 aad-pod-identity 之前，请遵循缓解步骤并配置策略。

## <a name="mitigation"></a>缓解措施

为了缓解群集级别的漏洞，可以使用 Azure 内置策略“Kubernetes 群集容器应仅使用允许的功能”来限制 CAP_NET_RAW 攻击。  

将 NET_RAW 添加到“需要删除功能”

![image](https://user-images.githubusercontent.com/50749048/118558790-206b8880-b735-11eb-9e48-236b81116812.png)

如果没有使用 Azure Policy，可以将 OpenPolicyAgent 许可控制器与验证 Webhook 的 Gatekeeper 一起使用。 如果已在群集中安装了 Gatekeeper，请添加 K8sPSPCapabilities 类型的 ConstraintTemplate：

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
添加一个模板，以通过 NET_RAW 功能限制 Pod 繁生：

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>创建使用 Kubenet 网络插件的 AKS 群集

创建一个使用 Kubenet 网络插件并已启用 Pod 托管标识的 AKS 群集。

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>更新使用 Kubenet 网络插件的现有 AKS 群集

更新使用 Kubenet 网络插件的现有 AKS 群集以包含 Pod 托管标识。

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>创建标识

使用 [az identity create][az-identity-create] 创建标识，并设置 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_ID* 变量。

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>为托管标识分配权限

若要运行演示，IDENTITY_CLIENT_ID 托管标识必须在包含 AKS 群集虚拟机规模集的资源组中拥有“虚拟机参与者”权限。

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>创建 Pod 标识

使用 `az aks pod-identity add` 创建群集的 Pod 标识。

> [!IMPORTANT]
> 必须具有订阅上的相关权限（例如，所有者），以创建标识和将角色绑定到群集标识。
> 
> 群集标识必须具有适用于要分配的标识的“托管标识操作员权限”。

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> 在 AKS 群集上启用 Pod 托管标识时，会将名为 *aks-addon-exception* 的 AzurePodIdentityException 添加到 *kube-system* 命名空间。 AzurePodIdentityException 允许带有特定标签的 Pod 访问 Azure 实例元数据服务 (IMDS) 终结点，而不会被节点托管标识 (NMI) 服务器拦截。 *aks-addon-exception* 允许 AKS 第一方加载项（例如 AAD Pod 托管标识）正常运行，而无需手动配置 AzurePodIdentityException。 （可选）可以使用 `az aks pod-identity exception add`、`az aks pod-identity exception delete`、`az aks pod-identity exception update` 或 `kubectl` 添加、删除和更新 AzurePodIdentityException。
> “POD_IDENTITY_NAME”必须是有效的 [DNS 子域名称]，如 [RFC 1123] 中定义的那样。 

> [!NOTE]
> 使用 `pod-identity add` 分配 pod 标识时，Azure CLI 尝试通过 pod 标识 (IDENTITY_RESOURCE_ID) 向群集标识授予“托管标识操作员”角色。

## <a name="run-a-sample-application"></a>运行示例应用程序

要使某个 Pod 能够使用 AAD Pod 托管标识，该 Pod 需要一个 *aadpodidbinding* 标签，其值与 *AzureIdentityBinding* 中的选择器相匹配。 若要使用 AAD Pod 托管标识运行示例应用程序，请创建包含以下内容的 `demo.yaml` 文件。 请将 *POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 替换为前面步骤中使用的值。 请将 SUBSCRIPTION_ID 替换为你的订阅 ID。

> [!NOTE]
> 在前面的步骤中，你已创建 *POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 变量。 可以使用诸如 `echo` 的命令来显示你为变量设置的值（例如 `echo $IDENTITY_NAME`）。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: $POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

请注意，Pod 定义包含一个 *aadpodidbinding* 标签，其值与你在前一步骤中运行 `az aks pod-identity add` 时使用的 Pod 标识的名称相匹配。

使用 `kubectl apply` 将 `demo.yaml` 部署到 Pod 标识所在的同一命名空间：

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

使用 `kubectl logs` 确认示例应用程序是否成功运行。

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

确认日志中是否显示已成功获取令牌并且 *GET* 操作成功。
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
## <a name="run-an-application-with-multiple-identities"></a>运行具有多个标识的应用程序

## <a name="create-multiple-identities"></a>创建多个标识

使用 [az identity create][az-identity-create] 创建标识，并设置 IDENTITY_CLIENT_ID 和 IDENTITY_RESOURCE_ID 变量。

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME_1="application-identity_1"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_1}
export IDENTITY_NAME_2="application-identity_2"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_2}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query id -otsv)"
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identities"></a>为托管标识分配权限

*IDENTITY_CLIENT_ID* 托管标识必须在包含 AKS 群集虚拟机规模集的资源组中拥有“读取者”权限。

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_1" --scope $NODES_RESOURCE_ID
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_2" --scope $NODES_RESOURCE_ID
```

## <a name="create-pod-identities"></a>创建 Pod 标识

使用 `az aks pod-identity add` 为群集创建 Pod 标识。

> [!IMPORTANT]
> 必须在订阅中拥有相应的权限（例如 `Owner`）才能创建标识和角色绑定。

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector foo
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector foo
```

> [!NOTE]
> 在 AKS 群集上启用 Pod 托管标识时，会将名为 *aks-addon-exception* 的 AzurePodIdentityException 添加到 *kube-system* 命名空间。 AzurePodIdentityException 允许带有特定标签的 Pod 访问 Azure 实例元数据服务 (IMDS) 终结点，而不会被节点托管标识 (NMI) 服务器拦截。 *aks-addon-exception* 允许 AKS 第一方加载项（例如 AAD Pod 托管标识）正常运行，而无需手动配置 AzurePodIdentityException。 （可选）可以使用 `az aks pod-identity exception add`、`az aks pod-identity exception delete`、`az aks pod-identity exception update` 或 `kubectl` 添加、删除和更新 AzurePodIdentityException。

## <a name="run-a-sample-application-with-multiple-identities"></a>运行具有多个标识的示例应用程序

要使某个 Pod 能够使用 AAD Pod 托管标识，该 Pod 需要一个 *aadpodidbinding* 标签，其值与 *AzureIdentityBinding* 中的选择器相匹配。 若要使用 AAD Pod 托管标识运行示例应用程序，请创建包含以下内容的 `demo.yaml` 文件。 请将 *POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 替换为前面步骤中使用的值。 请将 SUBSCRIPTION_ID 替换为你的订阅 ID。

> [!NOTE]
> 在前面的步骤中，你已创建 *POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 变量。 可以使用诸如 `echo` 的命令来显示你为变量设置的值（例如 `echo $IDENTITY_NAME`）。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: foo
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

请注意，Pod 定义包含一个 *aadpodidbinding* 标签，其值与你在前一步骤中运行 `az aks pod-identity add` 时使用的 Pod 标识的名称相匹配。

使用 `kubectl apply` 将 `demo.yaml` 部署到 Pod 标识所在的同一命名空间：

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

使用 `kubectl logs` 确认示例应用程序是否成功运行。

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

确认日志中是否显示已成功获取令牌并且 *GET* 操作成功。
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)" export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## Clean up

To remove AAD pod-managed identity from your cluster, remove the sample application and the pod identity from the cluster. Then remove the identity.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>后续步骤

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识][az-managed-identities]。

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[RFC 1123]: https://tools.ietf.org/html/rfc1123
[DNS 子域名称]: https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names