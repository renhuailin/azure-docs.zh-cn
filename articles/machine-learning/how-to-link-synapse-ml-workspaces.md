---
title: 使用 Synapse 和 Azure 机器学习工作区创建链接服务（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何链接 Azure Synapse 和 Azure 机器学习工作区以获得统一的数据整理体验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575728"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>链接 Azure Synapse Analytics 和 Azure 机器学习工作区（预览版）

在本文中，你将了解如何创建一个链接服务来链接 [Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is) 工作区和 [Azure 机器学习工作区](concept-workspace.md)。

通过将 Azure 机器学习工作区与 Azure Synapse 工作区链接起来，你可以将 Apache Spark 池附加为专用计算，用于大规模的数据整理，或完全从相同 Python 笔记本进行模型训练。

可以通过 [Python SDK](#link-sdk) 或 [Azure 机器学习工作室](#link-studio)链接 ML 工作区和 Synapse 工作区。

还可以链接工作区并使用单个 [Azure 资源管理器 (ARM) 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)附加 Synapse Spark 池。

>[!IMPORTANT]
> Azure 机器学习与 Azure Synapse 的集成提供公共预览版。 `azureml-synapse` 包提供的功能是[试验性的](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，并且可能随时更改。

## <a name="prerequisites"></a>先决条件

* [创建 Azure 机器学习工作区](how-to-manage-workspace.md?tabs=python)。

* [在 Azure 门户中创建 Synapse 工作区](/azure/synapse-analytics/quickstart-create-workspace)。

* [使用 Azure 门户、Web 工具或 Synapse Studio 创建 Apache Spark 池](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* 安装 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro)

* 访问 [Azure 机器学习工作室](https://ml.azure.com/)。

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>通过 Python SDK 链接工作区

> [!IMPORTANT]
> 若要成功链接到 Synapse 工作区，必须授予 Synapse 工作区的“所有者”角色。 查看 [Azure 门户](https://ms.portal.azure.com/)中的访问权限。
>
> 如果你不是 Synapse 工作区的“所有者”，且只是 Synapse 工作区的“参与者”，则只能使用现有的链接服务 。 请参阅如何[检索和使用现有的链接服务](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)。

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

## <a name="next-steps"></a>后续步骤

* [使用 Azure Synapse 附加 Synapse Spark 池进行数据准备（预览版）](how-to-data-prep-synapse-spark-pool.md)。
* [如何通过 Azure Synapse 在机器学习管道中使用 Apache Spark](how-to-use-synapsesparkstep.md)
* [训练模型](how-to-set-up-training-targets.md)。
