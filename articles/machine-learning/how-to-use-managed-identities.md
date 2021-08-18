---
title: 将托管标识用于访问控制（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用托管标识控制从 Azure 机器学习工作区对 Azure 资源的访问。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: fa41d81192a84b0b5b3c15f2c965c914a1ba42eb
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294312"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>将托管标识与 Azure 机器学习结合使用（预览版）

通过[托管标识](../active-directory/managed-identities-azure-resources/overview.md)，可以使用访问资源所需的最低权限配置工作区。 

以可信方式配置 Azure 机器学习工作区时，务必确保与工作区关联的不同服务具有正确的访问级别。 例如，在机器学习工作流期间，工作区需要访问用于 Docker 映像的 Azure 容器注册表 (ACR) 和用于训练数据的存储帐户。 

此外，托管标识支持对权限进行精细控制，例如，可以授予或撤消从特定的计算资源对特定 ACR 的访问权限。

本文介绍如何使用托管标识执行以下操作：

 * 为 Azure 机器学习工作区配置和使用 ACR，无需让管理员用户访问 ACR。
 * 访问工作区外部的专有 ACR，以拉取用于训练或推理的基础映像。
 * 创建具有用户分配的托管标识的工作区以访问关联的资源。

> [!IMPORTANT]
> 通过 Azure 机器学习使用托管标识控制对资源的访问这一功能当前处于预览阶段。 预览功能按原样提供，不保证支持或服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- [用于机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)
- [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro)。
- 若要分配角色，Azure 订阅登录名必须具有[托管标识操作员](../role-based-access-control/built-in-roles.md#managed-identity-operator)角色或授予所需操作的其他角色（如所有者）。
- 必须熟悉如何创建和使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="configure-managed-identities"></a>配置托管标识

在某些情况下，必须禁止管理员用户对 Azure 容器注册表的访问。 例如，可能共享了 ACR，你需要禁止其他用户进行管理访问。 或者，订阅级别策略不允许创建启用了管理员用户的 ACR。

> [!IMPORTANT]
> 使用 Azure 机器学习在 Azure 容器实例 (ACI) 上进行推理时，需要 ACR 上的管理员用户访问权限。 如果计划将模型部署到 ACI 进行推理，请不要禁用此功能。

在不启用管理员用户访问权限的情况下创建 ACR 时，将使用托管标识来访问 ACR 以生成和拉取 Docker 映像。

创建工作区时，可以在禁用管理员用户的情况下自带 ACR。 或者，让 Azure 机器学习创建工作区 ACR，以后再禁用管理员用户。

### <a name="bring-your-own-acr"></a>自带 ACR

如果订阅策略不允许使用 ACR 管理员用户，则应首先创建无管理员用户的 ACR，然后将其与工作区关联。 此外，如果有已禁用管理员用户的现有 ACR，可以将其附加到工作区。

[从 Azure CLI 创建 ACR](../container-registry/container-registry-get-started-azure-cli.md)，无需设置 ```--admin-enabled``` 参数，或从 Azure 门户创建，无需启用管理员用户。 然后，在创建 Azure 机器学习工作区时，指定 ACR 的 Azure 资源 ID。 下面的示例演示如何创建使用现有 ACR 的新 Azure ML 工作区：

> [!TIP]
> 若要获取 `--container-registry` 参数的值，请使用 [az acr show](/cli/azure/acr#az_acr_show) 命令显示 ACR 的信息。 `id` 字段包含 ACR 的资源 ID。

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>让 Azure 机器学习服务创建工作区 ACR

如果没有自带 ACR，Azure 机器学习服务将在执行需要 ACR 的操作时创建一个 ACR。 例如，将训练运行提交到机器学习计算、生成环境或部署 web 服务终结点。 工作区创建的 ACR 将启用管理员用户，你需要手动禁用管理员用户。

1. 创建新的工作区

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. 执行需要 ACR 的操作。 例如，[训练模型教程](tutorial-train-models-with-aml.md)。

1. 获取由群集创建的 ACR 名称：

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    此命令返回类似于以下文本的值。 只需要文本的最后一部分，即 ACR 实例名称：

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. 更新 ACR 以禁用管理员用户：

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>使用托管标识创建计算以访问用于训练的 Docker 映像

若要访问工作区 ACR，请创建启用了系统分配的托管标识的机器学习计算群集。 可以在创建计算力时从 Azure 门户或工作室启用该标识，也可以通过以下方法从 Azure CLI 启用。 有关详细信息，请参阅[将托管标识用于计算群集](how-to-create-attach-compute-cluster.md#managed-identity)。

# <a name="python"></a>[Python](#tab/python)

使用 [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration) 创建计算群集时，请使用 `identity_type` 参数设置托管标识类型。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

有关在工作室中创建计算群集时配置托管标识的信息，请参阅[设置托管标识](how-to-create-attach-compute-studio.md#managed-identity)。

---

托管标识在工作区 ACR 上自动被授予 ACRPull 角色，以允许拉取 Docker 映像进行训练。

> [!NOTE]
> 如果首先创建计算，则必须手动分配 ACRPull 角色，才能创建工作区 ACR。

## <a name="access-base-images-from-private-acr"></a>从专用 ACR 访问基础映像

默认情况下，Azure 机器学习使用来自 Microsoft 托管的公共存储库中的 Docker 基础映像。 然后，在这些映像上生成训练或推理环境。 有关详细信息，请参阅[什么是 ML 环境？](concept-environments.md)。

若要在企业内部使用自定义基础映像，可以使用托管标识访问专用 ACR。 下面是两个用例：

 * 使用基础映像按原样进行训练。
 * 使用自定义映像作为基础生成 Azure 机器学习托管映像。

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>将 Docker 基础映像拉取到机器学习计算群集按原样进行训练

如前面所述，创建启用了系统分配的托管标识的机器学习计算群集。 然后，确定托管标识的主体 ID。

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

或者，可以更新计算群集来分配用户分配的托管标识：

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

若要允许计算群集拉取基础映像，请在专用 ACR 上授予托管服务标识 ACRPull 角色

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

最后，在提交训练运行时，请在[环境定义](how-to-use-environments.md#use-existing-environments)中指定基础映像位置。

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> 若要确保基础映像直接拉取到计算资源，请设置 `user_managed_dependencies = True`，并且不要指定 Dockerfile。 否则 Azure 机器学习服务将尝试生成新的 Docker 映像并失败，因为只有计算群集才能从 ACR 中拉取基础映像。

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>从专用 ACR 生成 Azure 机器学习托管环境到基础映像以进行训练或推理

在此场景中，Azure 机器学习服务在从专用 ACR 提供的基础映像之上生成训练或推理环境。 由于映像生成任务是在工作区 ACR 上使用 ACR 任务来执行的，因此需要执行更多步骤来允许访问。

1. 创建用户分配的托管标识并向该标识授予对专用 ACR 的 ACRPull 访问权限 。  
1. 向工作区系统分配的托管标识授予上一步中用户分配的托管标识上的托管标识操作员角色 。 此角色允许工作区将用户分配的托管标识分配给 ACR 任务用于生成托管环境。 

    1. 获取工作区系统分配的托管标识的主体 ID：

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. 授予托管标识操作员角色：

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI 资源 ID 是用户分配的标识的 Azure 资源 ID，格式为 `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>`。

1. 使用 [ 方法](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-)在工作区连接中指定用户分配的托管标识的外部 ACR 和客户端 ID：

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

完成配置后，可以在生成用于训练或推理的环境时使用专用 ACR 中的基础映像。 下面的代码片段演示如何在环境定义中指定基础映像 ACR 和映像名称：

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

或者，可以使用 [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity) 在环境定义本身中指定托管标识资源 URL 和客户端 ID。 如果显式使用注册表标识，则它会替代前面指定的任何工作区连接：

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>"
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>使用 Docker 映像进行推理

如前面所述，在没有管理员用户的情况下配置 ACR 后，可以访问 Docker 映像进行推理，无需 Azure Kubernetes service (AKS) 中的管理密钥。 创建 AKS 或将其附加到工作区时，会自动为该群集的服务主体分配对工作区 ACR 的 ACRPull 访问权限。

> [!NOTE]
> 如果自带 AKS 群集，则群集必须已启用服务主体而不是托管标识。

## <a name="create-workspace-with-user-assigned-managed-identity"></a>创建具有用户分配的托管标识的工作区

创建工作区时，可以指定用户分配的托管标识，该标识将用于访问关联的资源：ACR、KeyVault、存储和 App Insights。

首先[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)，并记下托管标识的 ARM 资源 ID。

然后，使用 Azure CLI 或 Python SDK 创建工作区。 使用 CLI 时，使用 `--primary-user-assigned-identity` 参数指定 ID。 使用 SDK 时，请使用 `primary_user_assigned_identity`。 以下是使用 Azure CLI 和 Python 通过以下参数创建新工作区的示例：

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

还可以使用 [ARM 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced)创建具有用户分配的托管标识的工作区。

> [!IMPORTANT]
> 若要引入自己的关联资源，需要为这些资源授予托管标识角色，而不是让 Azure 机器学习服务创建它们。 使用[角色分配 ARM 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-dependencies-role-assignment)进行分配。

对于具有[用于加密的客户管理的密钥](concept-data-encryption.md)的工作区，可以传入用户分配的托管标识以从存储向密钥保管库进行身份验证。 使用参数 user-assigned-identity-for-cmk-encryption (CLI) 或 user_assigned_identity_for_cmk_encryption (SDK) 来传递托管标识 。 此托管标识可与工作区主要用户分配的托管标识相同，也可不同。

如果已有工作区，则可以使用 ```az ml workspace update``` CLI 命令或 ```Workspace.update``` Python SDK 方法将其从系统分配的托管标识更新为用户分配的托管标识。


## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 机器学习的企业安全性](concept-enterprise-security.md)。
