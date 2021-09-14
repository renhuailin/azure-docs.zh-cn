---
title: 使用机密存储 CSI 驱动程序保护 Azure Kubernetes 服务机密
description: 了解如何使用机密存储 CSI 驱动程序将机密存储与 Azure Kubernetes 服务 (AKS) 集成。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: 6b971849501eb4229dd4db8a58f7fc59843aef4b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429039"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>在 Azure Kubernetes 服务 (AKS) 群集中使用适用于 Kubernetes 的机密存储 CSI 驱动程序（预览）

适用于 Kubernetes 的机密存储 CSI 驱动程序允许通过 [CSI 卷][kube-csi]将 Azure Key Vault 作为机密存储与 Kubernetes 群集集成。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在开始之前，请安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli-windows) 和 aks-preview 扩展。

### <a name="supported-kubernetes-versions"></a>支持的 Kubernetes 版本

要使用此功能，建议采用的最低 Kubernetes 版本为 1.18。 

## <a name="features"></a>功能

- 使用 CSI 卷将机密、密钥和/或证书装载到 Pod
- 支持 CSI 内联卷（Kubernetes v1.15+ 版）
- 支持以单卷形式装载多个机密存储对象
- 支持使用 SecretProviderClass CRD 完成 Pod 移植
- 支持窗口容器（Kubernetes v1.18+ 版）
- 与 Kubernetes 机密同步（机密存储 CSI 驱动程序 v0.0.10+）
- 支持自动轮换已装载内容和已同步的 Kubernetes 机密（机密存储 CSI 驱动程序 v0.0.15+）

## <a name="register-the-aks-azurekeyvaultsecretsprovider-preview-feature"></a>注册 `AKS-AzureKeyVaultSecretsProvider` 预览版功能

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

若要创建可使用机密存储 CSI 驱动程序的 AKS 群集，必须在订阅中启用 `AKS-AzureKeyVaultSecretsProvider` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `AKS-AzureKeyVaultSecretsProvider` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-AzureKeyVaultSecretsProvider"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzureKeyVaultSecretsProvider')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

还需要 aks-preview Azure CLI 扩展 0.5.9 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 如果已安装扩展，请使用 [az extension update][az-extension-update] 命令更新到最新的可用版本。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>使用机密存储 CSI 驱动程序支持创建 AKS 群集

首先，创建 Azure 资源组：

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

若要使用机密存储 CSI 驱动程序功能创建 AKS 群集，请将 [az aks create][az-aks-create] 命令与加载项 `azure-keyvault-secrets-provider` 配合使用。

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

该加载项会创建一个用户分配的托管标识，名为 `azurekeyvaultsecretsprovider-*`，以用于访问 Azure 资源。 我们可以使用此标识来连接到将会存储机密的 Azure Key Vault。 请记下输出中该标识的 `clientId`：

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>使用机密存储 CSI 驱动程序支持升级现有的 AKS 群集

若要使用机密存储 CSI 驱动程序功能升级现有的 AKS 群集，请搭配使用 [az aks enable-addons][az-aks-enable-addons] 命令与加载项 `azure-keyvault-secrets-provider`：

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

如上所述，该加载项会创建一个用户分配的托管标识，该标识可用于对 Azure Key Vault 进行身份验证。

## <a name="verify-secrets-store-csi-driver-installation"></a>验证机密存储 CSI 驱动程序安装

上述命令将会在节点上安装机密存储 CSI 驱动程序和 Azure Key Vault 提供程序。 通过列出 kube-system 命名空间中所有带有 secrets-store-csi-driver 和 secrets-store-provider-azure 标签的 Pod 来验证完成情况，并确保输出如下所示：

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

## <a name="enabling-and-disabling-autorotation"></a>启用和禁用自动旋转

> [!NOTE]
> 启用后，机密存储 CSI 驱动程序将通过每两分钟轮询一次更改来更新在 SecretProviderClass 的 secretObjects 中定义的 Pod 装载和 Kubernetes 机密。

若要启用自动轮换机密，可在创建群集时使用 `enable-secret-rotation` 标志：

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

或者更新已经启用了加载项的现有群集：

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

若要禁用，请使用 `disable-secret-rotation` 标志：

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>创建或使用现有的 Azure Key Vault

除 AKS 群集外，还需要包含机密内容的 Azure Key Vault 资源。 请记住，Key Vault 的名称必须是全局唯一的。

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault 可以存储密钥、机密和证书。 在此示例中，我们将会设置名为 `ExampleSecret` 的纯文本机密：

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

请注意下一节中使用的下列属性：

- 密钥保管库中机密对象的名称
- 对象类型（机密、密钥或证书）
- Azure Key Vault 资源的名称
- 订阅所属的 Azure 租户 ID

## <a name="provide-identity-to-access-azure-key-vault"></a>提供标识以访问 Azure Key Vault

使用前面步骤中的值来设置权限，允许加载项创建的托管标识访问 keyvault 对象：

```azurecli
az keyvault set-policy -n <keyvault-name> --<object-type>-permissions get --spn <client-id>
```

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>创建与应用个人的 SecretProviderClass 对象

若要为 AKS 群集使用和配置机密存储 CSI 驱动程序，请创建 SecretProviderClass 自定义资源。 确保 `objects` 数组与 Azure Key Vault 实例中存储的对象匹配：

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: <keyvault-name>
spec:
  provider: azure
  parameters:
    keyvaultName: "<keyvault-name>"       # The name of the Azure Key Vault
    useVMManagedIdentity: "true"         
    userAssignedIdentityID: "<client-id>" # The clientId of the addon-created managed identity
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: <secret-name>       # In this example, 'ExampleSecret'   
          objectType: secret              # Object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
    tenantId: "<tenant-id>"               # the tenant ID containing the Azure Key Vault instance
```

有关详细信息，请参阅[创建 SecretProviderClass 对象][sample-secret-provider-class]。 请确保使用你在上文中记下的值。

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>将 SecretProviderClass 应用到群集

接下来，部署你创建的 SecretProviderClass。 例如：

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>更新与应用群集的部署 YAML

若要确保群集使用新的自定义资源，请更新部署 YAML。 例如：

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
  - name: busybox
    image: k8s.gcr.io/e2e-test-images/busybox:1.29
    command:
      - "/bin/sleep"
      - "10000"
    volumeMounts:
    - name: secrets-store-inline
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "<keyvault-name>"
```

将更新的部署应用到群集：

```bash
kubectl apply -f ./my-deployment.yaml
```

## <a name="validate-the-secrets"></a>验证机密

Pod 启动后，在部署 YAML 中指定的卷路径上装载的内容便可用了。

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="disable-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>在现有 AKS 群集上禁用机密存储 CSI 驱动程序

若要在现有群集中禁用机密存储 CSI 驱动程序功能，请搭配使用 [az aks disable-addons][az-aks-disable-addons] 命令与 `azure-keyvault-secrets-provider` 标志：

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

## <a name="next-steps"></a>后续步骤
<!-- Add a context sentence for the following links -->
了解如何配合使用 CSI 机密存储驱动程序与 AKS 群集后，请参阅以下资源：

- [在 AKS 上为 Azure 磁盘和 Azure 文件存储启用 CSI 驱动程序][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
