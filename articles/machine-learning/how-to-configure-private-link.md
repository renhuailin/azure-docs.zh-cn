---
title: 配置专用终结点
titleSuffix: Azure Machine Learning
description: 使用 Azure 专用链接从虚拟网络安全地访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 5ba1b9d53255406a73b1b74dbc59fe39e3f9a0d7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979175"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>为 Azure 机器学习工作区配置 Azure 专用链接

本文档介绍如何将 Azure 专用链接与 Azure 机器学习工作区配合使用。 有关创建 Azure 机器学习虚拟网络的信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)一文。

> [!IMPORTANT]
> Azure 专用链接不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。 数据平面操作（如使用 Azure 机器学习 studio、Api (包括) 的已发布管道）或 SDK 使用专用终结点。
>
> 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中的 HTTPS 上的 DNS 有关。 建议使用 Google Chrome 的 Microsoft Edge 作为解决方法。

## <a name="prerequisites"></a>先决条件

如果计划将启用了专用链接的工作区与客户托管的密钥配合使用，则必须使用支持票证请求此功能。 有关详细信息，请参阅[管理和增加配额](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

## <a name="limitations"></a>限制

* Azure 政府区域或 Azure 中国世纪互联区域不支持使用具有专用链接的 Azure 机器学习工作区。
* 如果为使用私有链接保护的工作区启用公共访问，并通过公共 internet 使用 Azure 机器学习 studio，则设计器等某些功能可能无法访问你的数据。 如果数据存储在 VNet 保护的服务中，则会发生此问题。 例如，Azure 存储帐户。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

使用以下方法之一创建具有专用终结点的工作区。 上述每种方法都 __需要一个现有的虚拟网络__：

> [!TIP]
> 如果要同时创建工作区、专用终结点和虚拟网络，请参阅 [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

# <a name="python"></a>[Python](#tab/python)

Azure 机器学习 Python SDK 提供 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 类，此类可与 [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 配合使用来创建具有专用终结点的工作区。 此类需要现有虚拟网络。

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

[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了[az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create)命令。 此命令的以下参数可用于创建具有专用网络的工作区，但它需要现有虚拟网络：

* `--pe-name`：创建的专用终结点的名称。
* `--pe-auto-approval`：是否应自动批准与工作区的专用终结点连接。
* `--pe-resource-group`：要在其中创建专用终结点的资源组。 必须是包含虚拟网络的同一个组。
* `--pe-vnet-name`：要在其中创建专用终结点的现有虚拟网络。
* `--pe-subnet-name`：要在其中创建专用终结点的子网的名称。 默认值是 `default`。

# <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure 机器学习 studio 中的 " __网络__ " 选项卡可以配置专用终结点。 但是，它需要现有虚拟网络。 有关详细信息，请参阅 [在门户中创建工作区](how-to-manage-workspace.md)。

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>向工作区添加专用终结点

使用以下方法之一将专用终结点添加到现有工作区：

> [!IMPORTANT]
>
> 必须具有现有虚拟网络才能在中创建专用终结点。 在添加专用终结点之前，您还必须 [对专用终结点禁用网络策略](../private-link/disable-private-endpoint-network-policy.md) 。

> [!WARNING]
>
> 如果有与此工作区关联的任何现有计算目标，并且这些目标不在在中创建专用终结点的同一虚拟网络后面，则这些目标将不起作用。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

有关此示例中使用的类和方法的详细信息，请参阅 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 和 [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了[az ml workspace private-endpoint add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add)命令。

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[门户](#tab/azure-portal)

在门户的 "Azure 机器学习" 工作区中，选择 " __专用终结点连接__ "，然后选择 " __+ 专用终结点__"。 使用字段创建新的专用终结点。

* 选择 __区域__ 时，请选择虚拟网络所在的区域。 
* 选择 __资源类型__ 时，请使用 __MachineLearningServices/工作区__。 
* 将 __资源__ 设置为你的工作区名称。

最后，选择 " __创建__ " 以创建专用终结点。

---

## <a name="remove-a-private-endpoint"></a>删除专用终结点

使用以下方法之一从工作区中删除专用终结点：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) 删除专用终结点。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了[az ml workspace private-endpoint delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete)命令。

# <a name="portal"></a>[门户](#tab/azure-portal)

在门户的 "Azure 机器学习" 工作区中，选择 " __专用终结点连接__"，然后选择要删除的终结点。 最后，选择 " __删除__"。

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>通过专用终结点使用工作区

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 为了避免暂时中断连接，Microsoft 建议你在启用专用链接后在连接到工作区的计算机上刷新 DNS 缓存。 

有关 Azure 虚拟机的信息，请参阅[虚拟机文档](../virtual-machines/index.yml)。

## <a name="enable-public-access"></a>启用公共访问

使用专用终结点配置工作区后，可以选择启用对工作区的公共访问。 这样做不会删除专用终结点。 除了专用访问，它还支持公共访问。 若要对启用了专用链接的工作区启用公共访问，请使用以下步骤：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) 删除专用终结点。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了[az ml workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_update)命令。 若要启用工作区公共访问权限，请添加参数 `--allow-public-access true` 。

# <a name="portal"></a>[门户](#tab/azure-portal)

当前无法使用门户启用此功能。

---


## <a name="next-steps"></a>后续步骤

* 有关保护 Azure 机器学习工作区的详细信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md) 一文。

* 如果你计划在虚拟网络中使用自定义 DNS 解决方案，请参阅 [如何通过自定义 dns 服务器使用工作区](how-to-custom-dns.md)。
