---
title: 创建计算群集
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习工作区中创建计算群集。 使用计算群集充当用于训练或推理的计算目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 07/09/2021
ms.openlocfilehash: d36d7e91afc4b0bade9f3da08d1324aa7f56cba9
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594132"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>创建 Azure 机器学习计算群集

了解如何在 Azure 机器学习工作区中创建和管理[计算群集](concept-compute-target.md#azure-machine-learning-compute-managed)。

可以使用 Azure 机器学习计算群集在云中的 CPU 或 GPU 计算节点群集之间分配训练或批量推理过程。 有关包括 GPU 的 VM 大小的详细信息，请参阅 [GPU 优化的虚拟机大小](../virtual-machines/sizes-gpu.md)。 

在本文中，将学习以下内容：

* 创建计算群集
*  降低计算群集成本
* 为群集设置[托管标识](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-setup-vs-code.md)。

* 如果使用 Python SDK，请[使用工作区设置开发环境](how-to-configure-environment.md)。  设置环境后，附加到 Python 脚本中的工作区：

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>什么是计算群集？

Azure 机器学习计算群集是一个托管的计算基础结构，可让你轻松创建单节点或多节点计算。 计算群集是可以与工作区中的其他用户共享的资源。 提交作业时，计算会自动扩展，并可以放入 Azure 虚拟网络。 计算在容器化环境中执行，将模型的依赖项打包在 [Docker 容器](https://www.docker.com/why-docker)中。

计算群集可以在[虚拟网络环境](how-to-secure-training-vnet.md)中安全地运行作业，无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。 

## <a name="limitations"></a>限制

* 本文档中列出的某些场景标记为“预览”。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* 可以在你的工作区所在区域以外的区域中创建计算集群。 此功能处于预览版阶段，只可用于计算群集，不可用于计算实例 。 如果使用启用了专用终结点的工作区，则此预览版不可用。 

    > [!WARNING]
    > 在使用工作区或数据存储所在区域以外的区域中的计算群集时，可能会出现网络延迟加重和数据传输成本增加的情况。 在创建群集以及在该群集上运行作业时，可能会存在这样的延迟和成本。

* 目前只支持通过 [ARM 模板](/azure/templates/microsoft.machinelearningservices/workspaces/computes)来创建（而非更新）群集。 对于更新计算，目前建议使用 SDK、Azure CLI 或 UX。

* Azure 机器学习计算对可以分配的核心数等属性实施默认限制。 有关详细信息，请参阅[管理和请求 Azure 资源的配额](how-to-manage-quotas.md)。

* Azure 允许你在资源上放置锁，这样这些资源就无法被删除，或者会处于只读状态。 __请勿将资源锁应用于包含工作区的资源组__。 将锁应用于包含工作区的资源组会阻止对 Azure ML 计算群集进行缩放操作。 若要详细了解如何锁定资源，请参阅[锁定资源以防止意外更改](../azure-resource-manager/management/lock-resources.md)。

> [!TIP]
> 一般情况下，只要所需核心数方面的配额足够，群集就可以扩展到多达 100 个节点。 默认情况下，设置群集时会启用群集节点之间的通信（例如，为了支持 MPI 作业）。 但是，可以将群集扩展到数千个节点，只需[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)并请求将你的订阅、工作区或特定群集加入允许列表以禁用节点间通信即可。


## <a name="create"></a>创建

**时间估计**：大约 5 分钟。

可在不同的运行中重复使用 Azure 机器学习计算。 计算可与工作区中的其他用户共享，并在每次运行之后保留，它会根据提交的运行数以及群集上设置的 max_nodes 自动纵向扩展或缩减节点。 min_nodes 设置控制可用节点数的下限。

每个区域每个 VM 系列配额和创建计算群集时应用的区域总配额的专用内核是统一的，并与 Azure 机器学习训练计算实例配额共享。 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

计算在不使用时自动缩减为零个节点。   按需创建专用 VM 来运行作业。
    
# <a name="python"></a>[Python](#tab/python)


若要在 Python 中创建持久性 Azure 机器学习计算资源，请指定 **vm_size** 和 **max_nodes** 属性。 然后，Azure 机器学习将对其他属性使用智能默认值。
    
* **vm_size**：Azure 机器学习计算创建的节点的 VM 系列。
* **max_nodes**：在 Azure 机器学习计算中运行作业时自动扩展到的最大节点数。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

还可以在创建 Azure 机器学习计算时配置多个高级属性。 使用这些属性可以创建固定大小的持久性群集，或者在订阅中的现有 Azure 虚拟网络内创建持久性群集。  有关详细信息，请参阅 [AmlCompute 类](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)。

> [!WARNING]
> 在设置 `location` 参数时，如果它是你的工作区或数据存储所在区域以外的区域，则可能会出现网络延迟加重和数据传输成本增加的情况。 在创建群集以及在该群集上运行作业时，可能会存在这样的延迟和成本。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2 --location westus2
```

> [!WARNING]
> 在使用工作区或数据存储所在区域以外的区域中的计算群集时，可能会出现网络延迟加重和数据传输成本增加的情况。 在创建群集以及在该群集上运行作业时，可能会存在这样的延迟和成本。

有关详细信息，请参阅 [az ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute)。

# <a name="studio"></a>[工作室](#tab/azure-studio)

有关在工作室中创建计算群集的信息，请参阅[在 Azure 机器学习工作室中创建计算目标](how-to-create-attach-compute-studio.md#amlcompute)。

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 降低计算群集成本

也可以选择使用[低优先级 VM](how-to-manage-optimize-cost.md#low-pri-vm) 来运行部分或全部工作负载。 这些 VM 的可用性未得到保证，在使用时可能会被占用。 必须重启已占用的作业。 

请使用以下任一方法指定低优先级 VM：
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

设置 `vm-priority`：
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[工作室](#tab/azure-studio)

在工作室中，在创建 VM 时选择“低优先级”。

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 设置托管标识

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* 在预配配置中配置托管标识：  

    * 在名为 `ws` 的工作区中创建的系统分配的托管标识
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * 在名为 `ws` 的工作区中创建的用户分配的托管标识
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* 将托管标识添加到名为 `cpu_cluster` 的现有计算群集
    
    * 系统分配的托管标识：
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * 用户分配的托管标识：
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 创建具有托管标识的新托管计算群集

  * 用户分配的托管标识

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * 系统分配的托管标识

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 将托管标识添加到现有群集：

    * 用户分配的托管标识
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * 系统分配的托管标识

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[工作室](#tab/azure-studio)

请参阅[在工作室中设置托管标识](how-to-create-attach-compute-studio.md#managed-identity)。

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>托管标识用法

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>疑难解答

如果用户在 GA 发布之前已通过 Azure 门户创建了自己的 Azure 机器学习工作区，则他们可能无法在该工作区中创建 AmlCompute。 可对服务提出支持请求，也可通过门户或 SDK 创建新的工作区以立即解除锁定。

如果 Azure 机器学习计算群集在根据节点状态重设大小时卡住 (0 -> 0)，可能是由于 Azure 资源锁定而导致的。

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>后续步骤

使用计算群集执行以下操作：

* [提交训练运行](how-to-set-up-training-targets.md) 
* [运行批量推理](./tutorial-pipeline-batch-scoring-classification.md)。
