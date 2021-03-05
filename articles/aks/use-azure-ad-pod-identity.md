---
title: '在 Azure Kubernetes Service (预览版中使用 Azure Active Directory pod 托管标识) '
description: '了解如何在 Azure Kubernetes Service (AKS 中使用 AAD pod 托管的托管标识) '
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: e7c8a96ad012afdcd724a4a242c27018563f3a10
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176308"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>在 Azure Kubernetes Service (预览版中使用 Azure Active Directory pod 托管标识) 

Azure Active Directory pod 管理的标识使用 Kubernetes 基元将 Azure Active Directory (AAD) 中的 Azure 资源和标识的 [托管标识][az-managed-identities] 与 pod 关联起来。 管理员创建标识和绑定作为 Kubernetes 基元，以允许 pod 访问依赖 AAD 作为标识提供者的 Azure 资源。

> [!NOTE]
> 如果已有 AADPODIDENTITY 的现有安装，则必须删除现有安装。 启用此功能意味着无需 MIC 组件。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>准备阶段

必须安装了以下资源：

* Azure CLI 版本 2.8.0 或更高版本
* `azure-preview`扩展版本0.4.68 或更高版本

### <a name="limitations"></a>限制

* 对于群集，最多允许50盒标识。
* 对于群集，最多允许 50 pod 标识例外。
* Pod 托管标识仅适用于 Linux 节点池。

### <a name="register-the-enablepodidentitypreview"></a>注册 `EnablePodIdentityPreview`

注册 `EnablePodIdentityPreview` 功能：

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>安装 `aks-preview` Azure CLI

还需要 *aks-preview* Azure CLI 扩展版本0.4.64 或更高版本。 使用 [az extension add][az-extension-add]命令安装 *aks-preview* Azure CLI 扩展。 或使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>创建具有托管标识的 AKS 群集

创建启用了托管标识和 pod 托管标识的 AKS 群集。 以下命令使用 [az group create][az-group-create]创建名为 *myResourceGroup* 的资源组，并使用 [Az aks create][az-aks-create]命令在 *MyResourceGroup* 资源组中创建名为 *myAKSCluster* 的 aks 群集。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

使用 [az aks get-credentials][az-aks-get-credentials] 登录到 AKS 群集。 此命令还会 `kubectl` 在开发计算机上下载并配置客户端证书。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
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

## <a name="assign-permissions-for-the-managed-identity"></a>分配托管标识的权限

*IDENTITY_CLIENT_ID* 托管标识必须具有资源组的 "读取者" 权限，该资源组包含 AKS 群集的虚拟机规模集。

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>创建 pod 标识

使用创建群集的 pod 标识 `az aks pod-identity add` 。

> [!IMPORTANT]
> 你必须在订阅上具有相应的权限，如 `Owner` ，才能创建标识和角色绑定。

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> 在 AKS 群集上启用 pod 托管标识后，将向 *kube* 命名空间添加一个名为 *AKS* 的 AzurePodIdentityException。 AzurePodIdentityException 允许带有特定标签的 pod 访问 Azure 实例元数据服务 (IMDS) 终结点，而不会被节点管理的标识 (NMI) 服务器截获。 *Aks-exception* 允许 aks 第一方加载项，如 AAD pod 托管标识，无需手动配置 AzurePodIdentityException 即可运行。 （可选）可以使用 `az aks pod-identity exception add` 、、或添加、删除和更新 AzurePodIdentityException `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` 。

## <a name="run-a-sample-application"></a>运行示例应用程序

若要使用 AAD pod 托管标识，pod 需要 *aadpodidbinding* 标签，其值与 *AzureIdentityBinding* 中的选择器匹配。 若要使用 AAD pod 托管标识运行示例应用程序，请创建 `demo.yaml` 包含以下内容的文件。 将 *POD_IDENTITY_NAME*、 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 替换为前面步骤中的值。 将 *SUBSCRIPTION_ID* 替换为你的订阅 ID。

> [!NOTE]
> 在前面的步骤中，您创建了 *POD_IDENTITY_NAME*、 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 变量。 例如，可以使用命令（如） `echo` 显示为变量设置的值 `echo $IDENTITY_NAME` 。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
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

请注意，pod 定义具有一个 *aadpodidbinding* 标签，其值与你 `az aks pod-identity add` 在上一步中运行的 pod 标识的名称相匹配。

`demo.yaml`使用以下内容部署到与你的 pod 标识相同的命名空间 `kubectl apply` ：

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

使用验证示例应用程序是否成功运行 `kubectl logs` 。

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

验证日志显示已成功获取令牌，并且 *GET* 操作成功。
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>清除

若要从群集中删除 AAD pod 托管标识，请从群集中删除示例应用程序和 pod 标识。 然后删除标识。

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>后续步骤

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识][az-managed-identities]。

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
