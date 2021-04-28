---
title: 配置专用终结点。
titleSuffix: Azure Machine Learning
description: 使用 Azure 专用链接从虚拟网络安全地访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: cb1a1cdf99e94bb4b4dc0cb6ef66da54a1b93ce8
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885787"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>为 Azure 机器学习工作区配置 Azure 专用链接

本文档介绍如何将 Azure 专用链接与 Azure 机器学习工作区配合使用。 有关创建 Azure 机器学习虚拟网络的信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)一文。

> [!IMPORTANT]
> Azure 专用链接不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。 数据平面操作（如使用 Azure 机器学习工作室）、API（包括已发布管道）或 SDK 使用专用终结点。
>
> 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中 HTTPS 上的 DNS 有关。 建议使用 Microsoft Edge 或 Google Chrome 来避开此问题。

## <a name="prerequisites"></a>先决条件

* 如果计划将启用了专用链接的工作区与客户管理的密钥配合使用，需要使用支持票证请求此功能。 有关详细信息，请参阅[管理和增加配额](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

* 必须具有用于创建专用终结点的现有虚拟网络。 在添加专用终结点之前，还需要[对专用终结点禁用网络策略](../private-link/disable-private-endpoint-network-policy.md)。
## <a name="limitations"></a>限制

* Azure 政府区域不支持使用具有专用链接的 Azure 机器学习工作区。
* 如果为使用私有链接保护的工作区启用公共访问，并通过公共 Internet 使用 Azure 机器学习工作室，则设计器等某些功能可能无法访问你的数据。 如果数据存储在 VNet 保护的服务中，则会出现此问题。 例如 Azure 存储帐户。

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

[用于机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了 [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create) 命令。 此命令的以下参数可用于创建具有专用网络的工作区，但它需要现有虚拟网络：

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

[用于机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了 [az ml workspace private-endpoint add](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) 命令。

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
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

[用于机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了 [az ml workspace private-endpoint delete](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) 命令。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在门户的 Azure 机器学习工作区中，选择“专用终结点连接”，然后选择要删除的专用终结点。 最后，选择“删除”。

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>通过专用终结点使用工作区

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 为了避免暂时中断连接，Microsoft 建议你在启用专用链接后在连接到工作区的计算机上刷新 DNS 缓存。 

有关 Azure 虚拟机的信息，请参阅[虚拟机文档](../virtual-machines/index.yml)。

## <a name="enable-public-access"></a>启用公共访问

在某些情况下，你可能希望允许某人通过公共终结点（而不是通过 VNet）连接到受保护的工作区。 使用专用终结点配置工作区后，可以选择启用对工作区的公共访问。 执行此操作不会删除专用终结点。 VNet 后面的组件之间的所有通信仍受到保护。 除了通过 VNet 进行专用访问以外，还可实现仅对工作区的公共访问。

> [!WARNING]
> 通过公共终结点进行连接时，工作室的某些功能将无法访问你的数据。 如果数据存储在 VNet 保护的服务中，则会出现此问题。 例如 Azure 存储帐户。 另请注意，计算实例 Jupyter/JupyterLab/RStudio 功能和正在运行的笔记本将不起作用。

若要对启用了专用链接的工作区启用公共访问，请使用以下步骤：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) 删除专用终结点。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[用于机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了 [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) 命令。 若要启用对工作区的公共访问，请添加参数 `--allow-public-access true`。

# <a name="portal"></a>[Portal](#tab/azure-portal)

当前无法使用门户启用此功能。

---


## <a name="next-steps"></a>后续步骤

* 有关确保 Azure 机器学习工作区安全的详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)一文。

* 如果计划在虚拟网络中使用自定义 DNS 解决方案，请参阅[如何结合使用自定义 DNS 服务器和工作区](how-to-custom-dns.md)。
