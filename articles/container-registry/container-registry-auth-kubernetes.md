---
title: 使用 Kubernetes 拉取机密通过 Azure 容器注册表进行身份验证
description: 了解如何通过使用服务主体创建拉取机密来为 Kubernetes 群集提供对 Azure 容器注册表中映像的访问权限。
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 149035de0fc84c75cdcaa73c91d6cd5379c53498
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439613"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster-using-a-pull-secret"></a>使用拉取机密将映像从 Azure 容器注册表拉取到 Kubernetes 群集

你可以将 Azure 容器注册表用作容器映像的来源，并将其用于任何 Kubernetes 群集，包括“本地”Kubernetes 群集，例如 [minikube](https://minikube.sigs.k8s.io/) 和 [kind](https://kind.sigs.k8s.io/)。 本文演示如何使用 Azure 容器注册表的凭据创建 Kubernetes 拉取机密。 然后，在 Pod 部署中使用该机密从 Azure 容器注册表中拉取映像。

此示例使用 Azure Active Directory [服务主体凭据](container-registry-auth-service-principal.md)创建拉取机密。 还可以使用其他 Azure 容器注册表凭据（例如[存储库范围访问令牌](container-registry-repository-scoped-permissions.md)）配置拉取机密。

> [!NOTE]
> 虽然会经常用到拉取机密，但会带来额外的管理开销。 如果使用的是 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)，建议使用[其他选项](authenticate-kubernetes-options.md)（例如，使用群集的托管标识或服务主体）来安全地拉取映像，而无需在每个 Pod 上额外设置 `imagePullSecrets`。

## <a name="prerequisites"></a>先决条件

本文假设你已创建了一个专用 Azure 容器注册表。 你还需要确保 Kubernetes 群集正在运行并可通过 `kubectl` 命令行工具访问。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果你未保存或记住服务主体密码，则可以使用 [az ad sp credential reset][az-ad-sp-credential-reset] 命令重置密码：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令为你的服务主体返回一个新的有效密码。

## <a name="create-an-image-pull-secret"></a>创建映像拉取机密

Kubernetes 使用“映像拉取机密”  来存储向注册表证明身份所需的信息。 若要为 Azure 容器注册表创建拉取机密，请提供服务主体 ID、密码和注册表 URL。 

使用以下 `kubectl` 命令创建映像拉取机密：

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
其中：

| 值 | 说明 |
| :--- | :--- |
| `secret-name` | 映像拉取机密的名称，例如 *acr-secret* |
| `namespace` | 用来放置机密的 Kubernetes 命名空间 <br/> 仅当要将机密置于默认命名空间之外的命名空间中时才需要此项 |
| `container-registry-name` | Azure 容器注册表的名称，例如 myregistry<br/><br/>`--docker-server` 是注册表登录服务器的完全限定名称  |
| `service-principal-ID` | Kubernetes 用来访问注册表的服务主体的 ID |
| `service-principal-password` | 服务主体密码 |

## <a name="use-the-image-pull-secret"></a>使用映像拉取机密

创建映像拉取机密后，可以使用它来创建 Kubernetes Pod 和部署。 在部署文件中，在 `imagePullSecrets` 下提供机密名称。 例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

在前面的示例中，`my-awesome-app:v1` 是要从 Azure 容器注册表中拉取的映像的名称，`acr-secret` 是你创建的用于访问注册表的拉取密钥的名称。 部署 Pod 时，如果群集上尚无映像，则 Kubernetes 会自动从注册表中拉取映像。


## <a name="next-steps"></a>后续步骤

* 若要详细了解如何使用服务主体和 Azure 容器注册表，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中详细了解映像拉取机密


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset