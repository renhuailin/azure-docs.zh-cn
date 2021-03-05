---
title: 创建和管理计算实例
titleSuffix: Azure Machine Learning
description: 了解如何创建和管理 Azure 机器学习计算实例。 用作开发环境，或用作其用途为开发/测试的计算目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: da6285b7a4b12e65a82475fb53e972067e1c4fe4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214858"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>创建和管理 Azure 机器学习计算实例

了解如何在 Azure 机器学习工作区中创建和管理[计算实例](concept-compute-instance.md)。

可将计算实例用作云中的完全配置、完全托管的开发环境。 对于开发和测试，还可将该实例用作[训练计算目标](concept-compute-target.md#train)或用于[推理目标](concept-compute-target.md#deploy)。   计算实例可以并行运行多个作业，它有一个作业队列。 作为开发环境，不能与工作区中的其他用户共享计算实例。

在本文中，学习如何：

* 创建计算实例 
* 管理（启动、停止、重启、删除）计算实例
* 访问终端窗口 
* 安装 R 或 Python 包
* 创建新环境或 Jupyter 内核

计算实例可以在[虚拟网络环境](how-to-secure-training-vnet.md)中安全地运行作业，无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。 

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

## <a name="create"></a>创建

**时间估计**：大约 5 分钟。

对于工作区而言，创建计算实例是一次性过程。 你可以使用计算作为开发工作站或计算目标来进行定型。 可将多个计算实例附加到工作区。

对于每个区域每个虚拟机 (VM) 系列配额和创建计算实例时应用的区域总配额，专用内核数一致，且该数量与 Azure 机器学习训练计算群集配额共享。 停止计算实例不会释放配额，因此无法确保你能够重启计算实例。 注意创建计算实例后，不能更改其虚拟机大小。

以下示例演示如何创建计算实例：

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [ComputeInstance 类](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?preserve-view=true&view=azure-ml-py)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

有关详细信息，请查看 [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) 参考文档。

# <a name="studio"></a>[工作室](#tab/azure-studio)

在 Azure 机器学习工作室的工作区中，当你准备好运行某个笔记本时，请从“计算”部分或“笔记本”部分创建新的计算实例。

若要了解如何在工作室中创建计算实例，请参阅[在 Azure 机器学习工作室中创建计算目标](how-to-create-attach-compute-studio.md#compute-instance)。

---

还可使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)创建计算实例。 

### <a name="create-on-behalf-of-preview"></a>代表他人创建（预览版）

作为管理员，你可代表数据科学家创建计算实例，并通过以下方式将实例分配给他们：
* [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)。  若要详细了解如何查找此模板中所需的 TenantID 和 ObjectID，请参阅[查找身份验证配置的标识对象 ID](../healthcare-apis/find-identity-object-ids.md)。  也可在 Azure Active Directory 门户中找到这些值。
* REST API

你为其创建计算实例的数据科学家需要拥有针对以下项的 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 权限： 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

数据科学家可启动、停止和重启计算实例。 他们可将计算实例用于：
* Jupyter
* JupyterLab
* RStudio
* 集成式笔记本

## <a name="manage"></a>管理

启动、停止、重新启动和删除计算实例。 计算实例不会自动纵向缩减，因此请确保停止该资源以免产生费用。

# <a name="python"></a>[Python](#tab/python)

在下例中，计算实例的名称均为“实例”

* 获取状态

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* 停止

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* 开始

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* 重启

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Delete

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在下例中，计算实例的名称均为“实例”

* 停止

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)。

* 开始 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)。

* 重启 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)。

* Delete

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete)。

# <a name="studio"></a>[工作室](#tab/azure-studio)

在 Azure 机器学习工作室中的工作区内选择“计算”，然后在顶部选择“计算实例”。 

![管理计算实例](./media/concept-compute-instance/manage-compute-instance.png)

可执行以下操作：

* 新建计算实例 
* 刷新“计算实例”选项卡。
* 启动、停止和重启计算实例。  只要实例在运行，你就需要为其付费。 不使用计算实例时，请将其停止，以便降低成本。 停止计算实例会将其解除分配。 然后在需要时重启。
* 删除计算实例。
* 筛选计算实例列表，以仅显示已创建的实例。

对于你创建的（或为你创建的）工作区中的每个计算实例，你可以：

* 访问计算实例上的 Jupyter、JupyterLab、RStudio
* 通过 SSH 连接到计算实例。 默认已禁用 SSH 访问，但可以在创建计算实例时启用。 SSH 访问是通过公钥/私钥机制实现的。 选项卡中将提供 IP 地址、用户名和端口号等 SSH 连接详细信息。
* 获取有关特定计算实例的详细信息，例如 IP 地址和区域。

---

使用 [Azure RBAC](../role-based-access-control/overview.md) 可以对工作区中的哪些用户能够创建、删除、启动、停止、重启计算实例进行控制。 充当工作区参与者和所有者角色的所有用户可以在整个工作区中创建、删除、启动、停止和重启计算实例。 但是，只有特定计算实例的创建者或分配的用户（如果该计算实例是以其名义创建的）可在该计算实例上访问 Jupyter、JupyterLab 和 RStudio。 计算实例专用于具有 root 用户访问权限的单个用户，并且可通过 Jupyter/JupyterLab/RStudio 进行终端访问。 计算实例将具有单用户登录，并且所有操作都将使用该用户在 Azure RBAC 中的标识和试验运行的归属。 SSH 访问是通过公钥/私钥机制控制的。

可以通过 Azure RBAC 来控制这些操作：
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>后续步骤

* [访问计算实例终端](how-to-access-terminal.md)
* [创建和管理文件](how-to-manage-files.md)
* [提交训练运行](how-to-set-up-training-targets.md)
