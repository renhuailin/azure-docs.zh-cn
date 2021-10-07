---
title: 对 Web 服务使用 Azure AD 标识
titleSuffix: Azure Machine Learning
description: 对 Azure Kubernetes 服务中的 Web 服务使用 Azure AD 标识，以便在评分期间访问云资源。
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 11/16/2020
ms.topic: how-to
ms.openlocfilehash: 2ebc7681ebe0c3ed8ea0aa5045d554ebdf5f1fd7
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425125"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>对 Azure Kubernetes 服务中的机器学习 Web 服务使用 Azure AD 标识

本操作指南介绍如何将 Azure Active Directory (Azure AD) 标识分配到 Azure Kubernetes 服务中部署的机器学习模型。 [Azure AD Pod 标识](https://github.com/Azure/aad-pod-identity)项目允许应用程序使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)和 Kubernetes 基元通过 Azure AD 安全访问云资源。 这样，Web 服务就可以安全访问你的 Azure 资源，而无需在 `score.py` 脚本中嵌入凭据或直接在其中管理令牌。 本文解释在 Azure Kubernetes 服务群集中创建和安装 Azure 标识，并将该标识分配到已部署的 Web 服务的步骤。

## <a name="prerequisites"></a>先决条件

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-setup-vs-code.md)。

- 使用 `kubectl` 命令访问 AKS 群集。 有关详细信息，请参阅[连接到群集](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- 已部署到 AKS 群集的 Azure 机器学习 Web 服务。

## <a name="create-and-install-an-azure-identity"></a>创建并安装 Azure 标识

1. 若要确定是否为 AKS 群集启用了 Kubernetes RBAC，请使用以下命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    如果启用了 Kubernetes RBAC，此命令将返回 `true` 值。 此值确定了要在下一步骤中使用的命令。

1. 在 AKS 群集上安装 [Azure AD Pod 标识](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/)。

1. 按照 Azure AD Pod 标识项目页中所述的步骤[创建 Azure 标识](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure)。

1. 按照 Azure AD Pod 标识项目页中所述的步骤[部署 Azure 标识](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity)。

1. 按照 Azure AD Pod 标识项目页中所述的步骤[部署 Azure 标识绑定](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)。

1. 如果在上一步骤中创建的 Azure 标识不在 AKS 群集所在的节点资源组中，请遵循 Azure AD Pod 标识项目页中所述的[角色分配](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group)步骤。

## <a name="assign-azure-identity-to-web-service"></a>将 Azure 标识分配到 Web 服务

以下步骤使用上一部分中创建的 Azure 标识，并通过 **选择器标签** 将其分配到 AKS Web 服务。

首先，在要将 Azure 标识分配到的 AKS 群集中，确定部署的名称和命名空间。 可运行以下命令获取此信息。 命名空间应是 Azure 机器学习工作区名称，部署名称应是门户中所示的终结点名称。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

通过编辑部署规范将 Azure 标识选择器标签添加到部署。选择器值应是在[部署 Azure 标识绑定](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)的步骤 5 中定义的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

编辑部署以添加 Azure 标识选择器标签。 转到 `/spec/template/metadata/labels` 下面的以下节。 应会看到类似于 `isazuremlapp: “true”` 的值。 按如下所示添加 aad-pod-identity 标签。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

若要验证是否正确添加了该标签，请运行以下命令。 还应看到新创建的 Pod 的状态。

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

启动并运行 Pod 后，此部署的 Web 服务现在可以通过 Azure 标识访问 Azure 资源，而无需在代码中嵌入凭据。

## <a name="assign-roles-to-your-azure-identity"></a>将角色分配到 Azure 标识

[为 Azure 托管标识分配适当的角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，以访问其他 Azure 资源。 确保分配的角色具有正确的 **数据操作**。 例如，[存储 Blob 数据读取者角色](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)对存储 Blob 拥有读取权限，而普通的[读取者角色](../role-based-access-control/built-in-roles.md#reader)可能没有这些权限。

## <a name="use-azure-identity-with-your-web-service"></a>对 Web 服务使用 Azure 标识

将模型部署到 AKS 群集。 `score.py` 脚本可以包含指向 Azure 标识有权访问的 Azure 资源的操作。 确保为你要尝试访问的资源安装了所需的客户端库依赖项。 下面几个示例演示如何使用 Azure 标识从服务访问不同的 Azure 资源。

### <a name="access-key-vault-from-your-web-service"></a>从 Web 服务访问 Key Vault

如果为 Azure 标识授予了对 **Key Vault** 中某个机密的读取访问权限，则 `score.py` 可以使用以下代码访问该机密。

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> 本例使用 DefaultAzureCredential。 若要使用特定访问策略向标识授予访问权限，请参阅[使用 Azure CLI 分配密钥保管库访问策略](../key-vault/general/assign-access-policy-cli.md)。

### <a name="access-blob-from-your-web-service"></a>从 Web 服务访问 Blob

如果为 Azure 标识授予了对 **存储 Blob** 中的数据的读取访问权限，则 `score.py` 可以使用以下代码访问此数据。

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>后续步骤

* 有关如何使用 Python Azure 标识客户端库的详细信息，请参阅 GitHub 上的[存储库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)。
* 有关将模型部署到 Azure Kubernetes 服务群集的详细指导，请参阅[操作指南](how-to-deploy-azure-kubernetes-service.md)。