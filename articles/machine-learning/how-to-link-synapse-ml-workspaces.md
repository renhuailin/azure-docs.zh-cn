---
title: 使用 Synapse 和 Azure 机器学习工作区创建链接服务（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何将 Azure Synapse 工作区和 Azure 机器学习工作区相链接并附加 Apache Spark 池，来获得统一的数据整理体验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 7652f1562050b3253b0df7ee16c4eff5dd6f8e85
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376857"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-and-attach-apache-spark-poolspreview"></a>将 Azure Synapse Analytics 工作区和 Azure 机器学习工作区进行链接并附加 Apache Spark 池（预览）

在本文中，你将了解如何创建一个链接服务来链接 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 工作区和 [Azure 机器学习工作区](concept-workspace.md)。 

通过将 Azure 机器学习工作区与 Azure Synapse 工作区进行链接，你可将 Apache Spark 池（由 Azure Synapse Analytics 提供支持）附加为专用计算，用于大规模数据整理，或完全从同一 Python 笔记本训练模型。

可以通过 [Python SDK](#link-sdk) 或 [Azure 机器学习工作室](#link-studio)链接 ML 工作区和 Synapse 工作区。

还可以链接工作区并使用单个 [Azure 资源管理器 (ARM) 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)附加 Synapse Spark 池。

>[!IMPORTANT]
> Azure 机器学习与 Azure Synapse 的集成提供公共预览版。 `azureml-synapse` 包提供的功能是[试验性的](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，并且可能随时更改。

## <a name="prerequisites"></a>先决条件

* [创建 Azure 机器学习工作区](how-to-manage-workspace.md?tabs=python)。

* [在 Azure 门户中创建 Synapse 工作区](../synapse-analytics/quickstart-create-workspace.md)。

* [使用 Azure 门户、Web 工具或 Synapse Studio 创建 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)

* 安装 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro)

* 访问 [Azure 机器学习工作室](https://ml.azure.com/)。

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>通过 Python SDK 链接工作区

> [!IMPORTANT]
> 若要成功链接到 Synapse 工作区，必须授予 Synapse 工作区的“所有者”角色。 查看 [Azure 门户](https://ms.portal.azure.com/)中的访问权限。
>
> 如果你不是 Synapse 工作区的“所有者”，且只是 Synapse 工作区的“参与者”，则只能使用现有的链接服务 。 请参阅如何[检索和使用现有的链接服务](#get-an-existing-linked-service)。

下面的代码使用 [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) 和 [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) 类，

* 将机器学习工作区 `ws` 与 Azure Synapse 工作区链接。
* 将 Synapse 工作区作为链接服务注册到 Azure 机器学习。

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> 为每个链接服务创建一个托管标识 `system_assigned_identity_principal_id`。 在启动 Synapse 会话之前，必须授予此托管标识 Synapse 工作区的“Synapse Apache Spark 管理员”角色。 [将 Synapse Apache Spark 管理员角色分配给 Synapse Studio 中的托管标识](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)。
>
> 若要查找特定链接服务的 `system_assigned_identity_principal_id`，请使用 `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')`。

### <a name="manage-linked-services"></a>管理链接服务

查看与机器学习工作区关联的所有链接服务。

```python
LinkedService.list(ws)
```

若要取消链接工作区，请使用 `unregister()` 方法

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>通过工作室链接工作区

执行以下步骤，通过 Azure 机器学习工作室将机器学习工作区链接到 Synapse 工作区： 

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在左窗格的“管理”部分中选择“链接服务”。
1. 选择“添加集成”。
1. 在“链接工作区”窗体上，填充字段

    |字段| 说明    
    |---|---
    |名称| 为链接服务提供一个名称。 此名称将用于引用此特定链接服务。
    |订阅名称 | 选择与机器学习工作区关联的订阅的名称。 
    |Synapse 工作区 | 选择要链接到的 Synapse 工作区。
    
1. 选择“下一步”，打开“选择 Spark 池(可选)”窗体 。 在此窗体上，你可以选择要附加到 Synapse Spark 池的工作区

1. 选择“下一步”，打开“审阅”窗体并检查你的选择 。
1. 选择“创建”以完成链接服务的创建过程。

## <a name="get-an-existing-linked-service"></a>获取现有链接服务

你必须有一个链接到 Azure Synapse Analytics 工作区的 ML 工作区（称为链接服务），然后才能附加专用计算以进行数据整理。 

若要检索和使用现有链接服务，需要具有 Azure Synapse Analytics 工作区的“用户”或“参与者”权限 。

此示例使用 [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) 方法从工作区 `ws` 检索现有链接服务 `synapselink1`。
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```

## <a name="attach-synapse-spark-pool-as-a-compute"></a>将 Synapse Spark 池附加为计算

检索链接服务后，请将 Synapse Apache Spark 池附加为用于数据整理任务的专用计算资源。 

可以通过以下方式附加 Apache Spark 池：
* Azure 机器学习工作室
* [Azure 资源管理器 (ARM) 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)
* Azure 机器学习 Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>通过工作室附加池
按照以下步骤操作： 

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在左窗格的“管理”部分中选择“链接服务”。
1. 选择 Synapse 工作区。
1. 选择左上方的“附加的 Spark 池”。 
1. 选择“附加”。 
1. 从列表中选择 Apache Spark 池，并为其命名。  
    1. 此列表标识可附加到计算的可用 Synapse Spark 池。 
    1. 若要创建新的 Synapse Spark 池，请参阅[使用 Synapse Studio 创建 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. 选择“附加所选内容”。 

### <a name="attach-a-pool-with-the-python-sdk"></a>使用 Python SDK 附加池

你还可以使用 Python SDK 附加 Apache Spark 池。 

以下代码的用途： 
1. 使用以下项配置 [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute)：

   1. 在上一步中创建或检索的 [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice) `linked_service`。 
   1. 要附加的计算目标的类型 `SynapseSpark`
   1. Apache Spark 池的名称。 该名称必须与 Azure Synapse Analytics 工作区中的现有 Apache Spark 池相同。
   
1. 通过传入以下项来创建机器学习 [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget)： 
   1. 要使用的机器学习工作区 `ws`
   1. 要在 Azure 机器学习工作区中表示计算的名称。 
   1. 配置 Synapse 计算时指定的 attach_configuration。
       1. 对 ComputeTarget () 的调用是异步的，因此在调用完成之前，示例将无法使用。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

验证是否已附加 Apache Spark 池。

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="next-steps"></a>后续步骤

* [如何使用 Azure Synapse 来整理数据（预览）](how-to-data-prep-synapse-spark-pool.md)。
* [如何通过 Azure Synapse 在机器学习管道中使用 Apache Spark](how-to-use-synapsesparkstep.md)
* [训练模型](how-to-set-up-training-targets.md)。