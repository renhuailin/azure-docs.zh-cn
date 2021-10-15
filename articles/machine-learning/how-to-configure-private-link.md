---
title: 配置专用终结点。
titleSuffix: Azure Machine Learning
description: 使用专用终结点从虚拟网络安全地访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/07/2021
ms.openlocfilehash: 9bb40b02df8de4bbd5fc7e59f55c7f31052b3480
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429882"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>为 Azure 机器学习工作区配置专用终结点

本文档介绍如何为 Azure 机器学习工作区配置专用终结点。 有关创建 Azure 机器学习虚拟网络的信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用终结点有助于降低数据泄露风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)一文。

> [!WARNING]
> 使用专用终结点保护工作区本身不能确保端到端安全。 必须保护解决方案的所有组件。 例如，如果为工作区使用专用终结点，但 Azure 存储帐户不在 VNet 之后，则工作区和存储之间的流量出于安全原因不会使用 VNet。
>
> 若要详细了解如何保护 Azure 机器学习所用的资源，请参阅以下文章：
>
> * [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。
> * [保护工作区资源](how-to-secure-workspace-vnet.md)。
> * [保护训练环境](how-to-secure-training-vnet.md)。
> * [保护推理环境](how-to-secure-inferencing-vnet.md)。
> * [在 VNet 中使用 Azure 机器学习工作室](how-to-enable-studio-virtual-network.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* 必须具有用于创建专用终结点的现有虚拟网络。 
* 在添加专用终结点之前[禁用专用终结点的网络策略](../private-link/disable-private-endpoint-network-policy.md)。

## <a name="limitations"></a>限制

* 如果为使用专用终结点保护的工作区启用公共访问，并通过公共 Internet 使用 Azure 机器学习工作室，则设计器等某些功能可能无法访问你的数据。 如果数据存储在 VNet 保护的服务中，则会出现此问题。 例如 Azure 存储帐户。
* 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中 HTTPS 上的 DNS 有关。 建议使用 Microsoft Edge 或 Google Chrome 来避开此问题。
* 使用专用终结点不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。 数据平面操作（如使用 Azure 机器学习工作室）、API（包括已发布管道）或 SDK 使用专用终结点。
* 在具有专用终结点的工作区中创建计算实例或计算群集时，计算实例和计算群集必须与工作区位于同一 Azure 区域。
* 创建 Azure Kubernetes 服务群集或将其附加到具有专用终结点的工作区时，群集必须与工作区位于同一区域。
* 使用具有多个专用终结点（预览版）的工作区时，必须有一个专用终结点与以下依赖项服务位于同一 VNet：

    * 为工作区提供默认存储的 Azure 存储帐户
    * 工作区的 Azure 密钥保管库
    * 工作区的 Azure 容器注册表。

    例如，一个 VNet（“服务”VNet）包含用于依赖项服务和工作区的专用终结点。 此配置使工作区能够与服务进行通信。 另一个 VNet（“客户端”）可能仅包含工作区的专用终结点，并且仅用于客户端开发计算机和工作区之间的通信。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

使用以下某个方法创建具有专用终结点的工作区。 上述每种方法都需要一个现有的虚拟网络：

> [!TIP]
> 如果要同时创建工作区、专用终结点和虚拟网络，请参阅[使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

# <a name="python"></a>[Python](#tab/python)

Azure 机器学习 Python SDK 提供 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 类，此类可与 [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 配合使用来创建具有专用终结点的工作区。 此类需要现有虚拟网络。

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[用于机器学习的 Azure CLI 扩展 1.0](reference-azure-machine-learning-cli.md) 提供了 [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create) 命令。 此命令的以下参数可用于创建具有专用网络的工作区，但它需要现有虚拟网络：

* `--pe-name`：创建的专用终结点的名称。
* `--pe-auto-approval`：是否应自动批准专用终结点与工作区的连接。
* `--pe-resource-group`：用于创建专用终结点的资源组。 必须是包含虚拟网络的同一个组。
* `--pe-vnet-name`：用于创建专用终结点的现有虚拟网络。
* `--pe-subnet-name`：用于创建专用终结点的子网的名称。 默认值为 `default`。

除了 create 命令所需的参数外，还需要这些参数。 例如，以下命令使用现有的资源组和 VNet 在美国西部区域创建一个新的工作区：

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 机器学习工作室中的“网络”选项卡可以配置专用终结点。 但是，它需要现有虚拟网络。 有关详细信息，请参阅[在门户中创建工作区](how-to-manage-workspace.md)。

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>向工作区添加专用终结点

使用以下方法之一将专用终结点添加到现有工作区：

> [!WARNING]
>
> 如果有与此工作区关联的任何现有计算目标，并且这些目标不在创建专用终结点的同一虚拟网络的后面，这些目标将不起作用。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

有关此示例中使用的类和方法的详细信息，请参阅 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 和 [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[用于机器学习的 Azure CLI 扩展 1.0](reference-azure-machine-learning-cli.md) 提供了 [az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) 命令。

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

在门户的 Azure 机器学习工作区中，选择“专用终结点连接”，然后选择“+ 专用终结点” 。 使用字段创建新的专用终结点。

* 选择“区域”时，请选择与虚拟网络相同的区域。 
* 选择“资源类型”时，请使用“Microsoft.MachineLearningServices/workspaces” 。 
* 将“资源”设置为工作区名称。

最后，选择“创建”以创建专用终结点。

---

## <a name="remove-a-private-endpoint"></a>删除专用终结点

使用以下某个方法从工作区删除专用终结点：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) 删除专用终结点。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[用于机器学习的 Azure CLI 扩展 1.0](reference-azure-machine-learning-cli.md) 提供了 [az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) 命令。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在门户的 Azure 机器学习工作区中，选择“专用终结点连接”，然后选择要删除的专用终结点。 最后，选择“删除”。

---

## <a name="securely-connect-to-your-workspace"></a>安全连接到工作区

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="multiple-private-endpoints-preview"></a>多个专用终结点（预览版）

作为一项预览版功能，Azure 机器学习支持对一个工作区使用多个专用终结点。 如果你希望不同的环境相互保持独立，则通常会使用多个专用终结点。 下面是可以使用多个专用终结点实现的一些方案：

* 客户端开发环境放在单独的 VNet 中。
* Azure Kubernetes 服务 (AKS) 群集放在单独的 VNet 中。
* 其他 Azure 服务放在单独的 VNet 中。 例如，Azure Synapse 和 Azure 数据工厂可以使用 Microsoft 托管的虚拟网络。 无论采用哪种方案，都可以将工作区的专用终结点添加到这些服务使用的托管 VNet。 有关将托管虚拟网络与这些服务结合使用的详细信息，请参阅以下文章：

    * [Synapse 托管专用终结点](/azure/synapse-analytics/security/synapse-workspace-managed-private-endpoints)
    * [Azure 数据工厂托管虚拟网络](/azure/data-factory/managed-virtual-network-private-endpoint)。

    > [!IMPORTANT]
    > Azure 机器学习不支持 [Synapse 的数据外泄保护](/azure/synapse-analytics/security/workspace-data-exfiltration-protection)。

> [!IMPORTANT]
> 每个包含工作区专用终结点的 VNet 还必须能够访问该工作区使用的 Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表。 例如，可为每个 VNet 中的服务创建一个专用终结点。

添加多个终结点的过程使用[将专用终结点添加到工作区](#add-a-private-endpoint-to-a-workspace)部分中所述的相同步骤。

### <a name="scenario-isolated-clients"></a>方案：隔离的客户端

若要隔离开发客户端，使其无法直接访问 Azure 机器学习使用的计算资源，请使用以下步骤：

> [!NOTE]
> 这些步骤假设你具备现有的工作区、Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表。 其中的每个服务在现有 VNet 中有一个专用终结点。

1. 为客户端创建另一个 VNet。 此 VNet 可以包含充当客户端的 Azure 虚拟机，或者可以包含由本地客户端用来连接 VNet 的 VPN 网关。
1. 为工作区使用的 Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表添加新的专用终结点。 这些专用终结点应存在于客户端 VNet 中。
1. 如果工作区使用额外的存储，请为该存储添加新的专用终结点。 专用终结点应存在于客户端 VNet 中，并已启用专用 DNS 区域集成。
1. 将新的专用终结点添加到工作区。 此专用终结点应存在于客户端 VNet 中，并已启用专用 DNS 区域集成。
1. 使用[在虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account)一文中的步骤，使工作室能够访问存储帐户。

下图演示了此配置。 “工作负载”VNet 包含工作区创建的用于训练和部署的计算资源。 “客户端”VNet 包含客户端或客户端 ExpressRoute/VPN 连接。 这两个 VNet 都包含用于工作区、Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表的专用终结点。

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-client.png" alt-text="隔离的客户端 VNet 示意图":::

### <a name="scenario-isolated-azure-kubernetes-service"></a>方案：隔离的 Azure Kubernetes 服务

若要创建工作区使用的隔离 Azure Kubernetes 服务，请使用以下步骤：

> [!NOTE]
> 这些步骤假设你具备现有的工作区、Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表。 其中的每个服务在现有 VNet 中有一个专用终结点。

1. 创建 Azure Kubernetes 服务实例。 在创建期间，AKS 会创建包含 AKS 群集的 VNet。
1. 为工作区使用的 Azure 存储帐户、Azure 密钥保管库和 Azure 容器注册表添加新的专用终结点。 这些专用终结点应存在于客户端 VNet 中。
1. 如果工作区使用其他存储，请为该存储添加新的专用终结点。 专用终结点应存在于客户端 VNet 中，并已启用专用 DNS 区域集成。
1. 将新的专用终结点添加到工作区。 此专用终结点应存在于客户端 VNet 中，并已启用专用 DNS 区域集成。
1. 将 AKS 群集附加到 Azure 机器学习工作区。 有关详细信息，请参阅[创建并附加 Azure Kubernetes 服务群集](how-to-create-attach-kubernetes.md#attach-an-existing-aks-cluster)。

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-aks.png" alt-text="隔离的 AKS VNet 示意图":::

## <a name="enable-public-access"></a>启用公共访问

在某些情况下，你可能希望允许某人通过公共终结点（而不是通过 VNet）连接到受保护的工作区。 使用专用终结点配置工作区后，可以选择启用对工作区的公共访问。 执行此操作不会删除专用终结点。 VNet 后面的组件之间的所有通信仍受到保护。 除了通过 VNet 进行专用访问以外，还可实现仅对工作区的公共访问。

> [!WARNING]
> 通过公共终结点进行连接时：
> * 工作室的某些功能将无法访问你的数据。 如果数据存储在受 VNet 保护的服务中，则会出现此问题。 例如 Azure 存储帐户。 
> * 不支持在计算实例（包括正在运行的笔记本）上使用 Jupyter、JupyterLab 和 RStudio。

若要对启用了专用终结点的工作区启用公共访问，请使用以下步骤：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) 删除专用终结点。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[用于机器学习的 Azure CLI 扩展 1.0](reference-azure-machine-learning-cli.md) 提供了 [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) 命令。 若要启用对工作区的公共访问，请添加参数 `--allow-public-access true`。

# <a name="portal"></a>[Portal](#tab/azure-portal)

当前无法使用门户启用此功能。

---

## <a name="next-steps"></a>后续步骤

* 有关确保 Azure 机器学习工作区安全的详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)一文。

* 如果计划在虚拟网络中使用自定义 DNS 解决方案，请参阅[如何结合使用自定义 DNS 服务器和工作区](how-to-custom-dns.md)。
