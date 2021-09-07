---
title: 重置群集的凭据
titleSuffix: Azure Kubernetes Service
description: 了解如何为 Azure Kubernetes 服务 (AKS) 群集更新或重置服务主体或 AAD 应用程序凭据
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 72cc0c6ab369b035df8c4a29c89be74fa9102cc5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101512"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>更新或轮换 Azure Kubernetes 服务 (AKS) 的凭据

使用服务主体创建的 AKS 群集具有为期一年的有效期。 在有效期即将结束时，你可以重置凭据来将服务主体延长额外的一段时间。 作为已定义安全策略的一部分，还可能要更新或轮换凭据。 本文详细介绍如何为 AKS 群集更新这些凭据。

还可以[将 AKS 群集与 Azure Active Directory 集成][aad-integration]，并将其用作群集的身份验证提供程序。 在这种情况下，你将为群集、AAD 服务器应用和 AAD 客户端应用创建另外 2 个标识，还可以重置这些凭据。

或者，可以使用托管标识而不是服务主体来获得权限。 托管标识比服务主体更易于管理，并且不需要更新或轮换。 有关详细信息，请参阅[使用托管标识](use-managed-identity.md)。

## <a name="before-you-begin"></a>准备阶段

需要安装并配置 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>为 AKS 群集更新或创建新的服务主体

要为 AKS 群集更新凭据时，可以选择以下任一操作：

* 为现有服务主体更新凭据。
* 创建新服务主体并更新群集以使用这些新凭据。 

> [!WARNING]
> 如果选择创建新的服务主体，请等待大约 30 分钟，让服务主体权限在所有区域中进行传播。 更新大型 AKS 群集以使用这些凭据可能需要很长时间才能完成。

### <a name="check-the-expiration-date-of-your-service-principal"></a>检查服务主体的到期日期

若要检查服务主体的到期日期，请使用 [az ad sp credential list][az-ad-sp-credential-list] 命令。 以下示例使用 [az aks show][az-aks-show] 命令获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的服务主体 ID 。 将服务主体 ID 设置为名为“SP_ID”的变量，以便与 [az ad sp credential list][az-ad-sp-credential-list] 命令一起使用。

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id "$SP_ID" --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>重置现有的服务主体凭据

若要为现有服务主体更新凭据，请使用 [az aks show][az-aks-show] 命令获取群集的服务主体 ID。 以下示例获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的 ID 。 服务主体 ID 设置为名为“SP_ID”变量以供在其他命令中使用。 这些命令使用 Bash 语法。

> [!WARNING]
> 在使用 Azure 虚拟机规模集的 AKS 群集上重置群集凭据时，将执行[节点映像升级][node-image-upgrade]以使用新凭据信息更新节点。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

借助包含服务主体 ID 的变量集，现在使用 [az ad sp credential reset][az-ad-sp-credential-reset] 重置凭据。 下面的示例可让 Azure 平台为服务主体生成新安全密码。 此新安全密码也存储为变量。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name "$SP_ID" --query password -o tsv)
```

现在继续浏览[使用新的服务主体凭据更新 AKS 群集](#update-aks-cluster-with-new-service-principal-credentials)。 若要在 AKS 群集上反映服务主体更改，必须执行此步骤。

### <a name="create-a-new-service-principal"></a>创建新服务主体

如果在上一部分中选择更新现有服务主体凭据，请跳过此步骤。 继续浏览[使用新的服务主体凭据更新 AKS 群集](#update-aks-cluster-with-new-service-principal-credentials)。

若要创建服务主体，然后更新 AKS 群集以使用这些新凭据，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 下一步会使用这些值。

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

现在使用自己的 [az ad sp create-for-rbac][az-ad-sp-create] 命令的输出为服务主体 ID 和客户端密码定义变量，如下面的示例所示。 SP_ID 是 appId，SP_SECRET 是 password：

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

现在继续浏览[使用新的服务主体凭据更新 AKS 群集](#update-aks-cluster-with-new-service-principal-credentials)。 若要在 AKS 群集上反映服务主体更改，必须执行此步骤。

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>使用新的服务主体凭据更新 AKS 群集

> [!IMPORTANT]
> 对于大型群集，使用新服务主体更新 AKS 群集可能需要较长时间才能完成。 请考虑查看和自定义[节点激增升级设置][node-surge-upgrade]，以最大程度地减少群集更新和升级期间的中断。

无论是选择为现有服务主体更新凭据还是创建服务主体，现在都通过 [az aks update-credentials][az-aks-update-credentials] 命令，使用新凭据更新 AKS 群集。 会使用 --service-principal 和 --client-secret 的变量：

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal "$SP_ID" \
    --client-secret "$SP_SECRET"
```

对于中小型群集，在 AKS 中更新服务主体凭据需要一段时间。

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>使用新的 AAD 应用程序凭据更新 AKS 群集

可以按照 [AAD 集成步骤][create-aad-app]创建新的 AAD 服务器和客户端应用程序。 或按照[与服务主体重置相同的方法](#reset-the-existing-service-principal-credential)重置现有的 AAD 应用程序。 之后，只需使用相同的 [az aks update-credentials][az-aks-update-credentials] 命令（但使用 *--reset-aad* 变量）更新群集 AAD 应用程序凭据即可。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>后续步骤

在本文中，我们更新了 AKS 群集本身的服务主体和 AAD 集成应用程序。 有关如何为群集中的工作负荷管理标识的详细信息，请参阅 [AKS 中的身份验证和授权的最佳做法][best-practices-identity]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
[node-surge-upgrade]: upgrade-cluster.md#customize-node-surge-upgrade