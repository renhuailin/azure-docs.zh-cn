---
title: 管理工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 基于角色的访问控制 (Azure RBAC) 来访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: Blackmist
ms.author: johwu
author: johnwu0604
ms.date: 03/26/2021
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 1089589dabd91d9f273b71f0b89bbf0ad3488e95
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427773"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问权限

本文介绍了如何管理对 Azure 机器学习工作区的访问权限（授权）。 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 用于管理对 Azure 资源的访问权限，例如，创建新资源或使用现有资源的权限。 Azure Active Directory (Azure AD) 中的用户会获得特定角色，这些角色授予对资源的访问权限。 Azure 提供内置角色和创建自定义角色的功能。

> [!TIP]
> 虽然本文着重介绍的是 Azure 机器学习，但 Azure ML 依赖的单个服务也提供了它们自己的 RBAC 设置。 例如，使用本文中的信息，可以配置谁能向 Azure Kubernetes 服务上部署为 Web 服务的模型提交评分请求。 但 Azure Kubernetes 服务具有它自己的一组 Azure 角色。 有关对于 Azure 机器学习可能有用的服务特定的 RBAC 信息，请参阅以下链接：
>
> * [控制对 Azure Kubernetes 群集资源的访问权限](../aks/azure-ad-rbac.md)
> * [使用 Azure RBAC 进行 Kubernetes 授权](../aks/manage-azure-rbac.md)
> * [使用 Azure RBAC 来管理对 blob 数据的访问权限](../storage/blobs/assign-azure-role-data-access.md)

> [!WARNING]
> 应用某些角色可能会限制 Azure 机器学习工作室中针对其他用户的 UI 功能。 例如，如果用户的角色无法创建计算实例，工作室中就不会提供创建计算实例的选项。 此行为是正常的，可以防止用户尝试会返回“拒绝访问”错误的操作。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区具有四个默认可用的内置角色。 将用户添加到工作区时，可以为他们分配下述内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **AzureML 数据科学家** | 可以在 Azure 机器学习工作区中执行所有操作，但创建或删除计算资源及修改工作区本身除外。 |
| **读者** | 工作区中的只读操作。 读取者可以在工作区中列出和查看资产，包括[数据存储](how-to-access-data.md)凭据。 读取者不能创建或更新这些资产。 |
| **参与者** | 在工作区中查看、创建、编辑或删除（如果适用）资产。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 Web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括能够在工作区中查看、创建、编辑或删除（如果适用）资产。 此外，还可以更改角色分配。 |

> [!IMPORTANT]
> 在 Azure 中，角色访问的作用域可以限定为多个级别。 例如，对工作区具有所有者访问权限的人可能没有对包含工作区的资源组的所有者访问权限。 有关详细信息，请参阅 [Azure RBAC 工作原理](../role-based-access-control/overview.md#how-azure-rbac-works)。


## <a name="manage-workspace-access"></a>管理工作区访问权限

如果你是工作区的所有者，则可以为工作区添加和删除角色。 还可以将角色分配给用户。 使用以下链接了解如何管理访问权限：
- [Azure 门户 UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager 模板](../role-based-access-control/role-assignments-template.md)

## <a name="create-custom-role"></a>创建自定义角色

如果内置角色不够，可以创建自定义角色。 自定义角色可能具有该工作区中的读取、写入、删除和计算资源权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。

> [!NOTE]
> 必须是该级别资源的所有者，才能在该资源中创建自定义角色。

要创建自定义角色，请首先构造角色定义 JSON 文件，指定角色的权限和作用域。 以下示例定义了名为“Data Scientist Custom”的自定义角色，其作用域为特定工作区级别：

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> 可以更改 `AssignableScopes` 字段，以在订阅级别、资源组级别或特定工作区级别设置此自定义角色的作用域。
> 上述自定义角色只是一个示例，请参阅我们建议的一些 [Azure 机器学习服务自定义角色](#customroles)。

此自定义角色可以在工作区中执行除以下操作之外的所有操作：

- 创建或更新计算资源。
- 删除计算资源。
- 添加、删除或更改角色分配。
- 删除工作区。

要部署此自定义角色，请使用以下 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署后，此角色在指定工作区中可用。 现在，可以在 Azure 门户中添加和分配此角色。

有关自定义角色的详细信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。 

### <a name="azure-machine-learning-operations"></a>Azure 机器学习操作

有关可用于自定义角色的操作（Actions 和 NotActions）的详细信息，请参阅[资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)。 还可以使用以下 Azure CLI 命令来列出操作：

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>列出自定义角色

在 Azure CLI 中运行以下命令：

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

若要查看特定自定义角色的角色定义，请使用以下 Azure CLI 命令。 `<role-name>` 的格式应与上述命令返回的格式相同：

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>更新自定义角色

在 Azure CLI 中运行以下命令：

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

你需要对新角色定义的整个作用域具有权限。 例如，如果此新角色的作用域跨三个订阅，则你需要对所有三个订阅都具有权限。 

> [!NOTE]
> 角色更新可能需要花费 15 分钟到一小时才能应用于该作用域中的所有角色分配。

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>使用 Azure 资源管理器模板实现可重复性

如果预计需要重新创建复杂的角色分配，则使用 Azure 资源管理器模板可能会很有帮助。 [machine-learning-dependencies-role-assignment 模板](https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices/machine-learning-dependencies-role-assignment)显示了如何在源代码中指定角色分配以供重用。 

## <a name="common-scenarios"></a>常见方案

下表汇总了 Azure 机器学习活动以及在最小作用域内执行它们所需的权限。 例如，如果可以使用某个工作区作用域（第 4 列）执行某个活动，自然也可以使用具有该权限的所有更高的作用域：

> [!IMPORTANT]
> 此表中以 `/` 开头的所有路径都是相对于 `Microsoft.MachineLearningServices/` 的 **相对路径**：

| 活动 | 订阅级作用域 | 资源组级作用域 | 工作区级作用域 |
| ----- | ----- | ----- | ----- |
| 创建新工作区 | 不是必需 | 所有者或参与者 | 不适用（在创建后成为所有者或继承更高作用域角色） |
| 请求订阅级别 Amlcompute 配额或设置工作区级别配额 | 所有者、参与者或自定义角色 </br>允许 `/locations/updateQuotas/action`</br> （在订阅范围内） | 未授权 | 未授权 |
| 新建计算群集 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |
| 新建计算实例 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |
| 提交任何类型的运行 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 发布管道和终结点 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| 在 AKS/ACI 资源上部署已注册的模型 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 针对已部署的 AKS 终结点进行评分 | 不是必需 | 不是必需 | 允许以下权限的“所有者”角色、“参与者”角色或自定义角色：`"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"`（未使用 Azure Active Directory 身份验证时）或 `"/workspaces/read"`（使用令牌身份验证时） |
| 使用交互式笔记本访问存储 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listStorageAccountKeys/action"` |
| 创建新的自定义角色 | 所有者、参与者或自定义角色允许 `Microsoft.Authorization/roleDefinitions/write` | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |

> [!TIP]
> 如果第一次尝试创建工作区时遇到失败，请确保角色允许 `Microsoft.MachineLearningServices/register/action`。 可以通过此操作将 Azure 机器学习资源提供程序注册到 Azure 订阅。

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>用于 Azure ML 计算群集的用户分配的托管标识

若要为 Azure 机器学习计算群集分配用户分配的标识，需要有写入权限来创建该计算，并且需要[托管标识操作员角色](../role-based-access-control/built-in-roles.md#managed-identity-operator)。 若要详细了解如何将 Azure RBAC 与托管标识配合使用，请阅读[如何管理用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>MLflow 操作

若要在 Azure 机器学习工作区中执行 MLflow 操作，请使用自定义角色的以下范围：

| MLflow 操作 | 范围 |
| --- | --- |
| 列出工作区跟踪存储中的所有试验，按 ID 获取试验，按名称获取试验 | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| 创建试验并命名，为实验设置标签，还原标记为删除的试验| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| 删除试验 | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| 获取运行以及相关的数据和元数据，获取指定运行的指定指标的所有值的列表，列出运行的项目 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| 在试验中新建运行，删除运行，还原已删除的运行，记录当前运行下的指标，为运行设置标签，删除运行上的标签，记录运行所使用的参数（键值对），记录运行的一批指标、参数和标签，更新运行状态 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| 按名称获取已注册的模型，获取注册表中所有已注册模型的列表，搜索每个请求阶段的已注册模型、最新版模型，获取已注册模型的版本，搜索模型版本，获取 URI（其中存储了模型版本的项目），按试验 ID 搜索运行 | `Microsoft.MachineLearningServices/workspaces/models/read` |
| 创建新的已注册模型，更新已注册模型的名称/说明，重命名现有的已注册模型，创建新版本的模型，更新模型版本的说明，将已注册模型转换到其中一个阶段 | `Microsoft.MachineLearningServices/workspaces/models/write` |
| 删除已注册模型及其所有版本，删除已注册模型的特定版本 | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>自定义角色示例

### <a name="data-scientist"></a>数据科学家

允许数据科学家在工作区中执行所有操作，但以下操作 **除外**：

* 创建计算
* 将模型部署到生产 AKS 群集
* 在生产环境中部署管道终结点

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>受限制的数据科学家

一个限制性更强的角色定义，允许的操作中不包含通配符。 它可以在工作区中执行所有操作，但以下操作 **除外**：

* 创建计算
* 将模型部署到生产 AKS 群集
* 在生产环境中部署管道终结点

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow 数据科学家

允许数据科学家执行 MLflow AzureML 支持的所有操作，但以下操作除外：

* 创建计算
* 将模型部署到生产 AKS 群集
* 在生产环境中部署管道终结点

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

允许将角色分配给服务主体，并使用该角色自动执行 MLOps 管道。 例如，若要针对已发布的管道提交运行，可使用以下代码：

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>工作区管理员

允许在工作区范围中执行所有操作，但以下操作 **除外**：

* 创建一个新工作区
* 分配订阅或工作区级别配额

工作区管理员也无法创建新的角色， 而只能在其工作区的作用域内分配现有内置角色或自定义角色：

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>数据标记员

允许你定义一个只能在作用域内标记数据的角色：

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="labeling-team-lead"></a>标记团队主管

允许评审和拒绝已标记的数据集并查看标记见解。 除此之外，此角色还允许执行标记者的任务。

`labeling_team_lead_custom_role.json` :
```json
{
    "properties": {
        "roleName": "Labeling Team Lead",
        "description": "Team lead for Labeling Projects",
        "assignableScopes": [
            "/subscriptions/<subscription_id>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.MachineLearningServices/workspaces/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/write",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/reject/action",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
                ],
                "notActions": [
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/write",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/delete",
                    "Microsoft.MachineLearningServices/workspaces/labeling/export/action"
                ],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="troubleshooting"></a>疑难解答

使用 Azure 基于角色的访问控制 (Azure RBAC) 时，请注意以下几点：

- 你在 Azure 中创建资源（例如工作区）时，并不会直接成为该资源的所有者。 你的角色继承自你在该订阅中获得相应授权的最高范围角色。 例如，如果你是网络管理员，有权创建机器学习工作区，则会为你分配该工作区的网络管理员角色，而不是所有者角色。

- 若要在工作区中执行配额操作，需要订阅级别的权限。 这意味着，只有当你在订阅作用域具有写入权限时，才能为你的托管计算资源设置订阅级配额或工作区级配额。

- 针对同一 Azure Active Directory 用户的两个角色分配具有冲突的 Actions/NotActions 部分时，如果操作在某个角色的 NotActions 中列出，但也在另一个角色中作为 Actions 列出，则此类操作可能不会生效。 若要详细了解 Azure 如何分析角色分配，请参阅 [Azure RBAC 如何确定用户是否有权访问资源](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- 若要在 VNet 中部署计算资源，需要显式拥有以下操作的权限：
    - VNet 资源上的 `Microsoft.Network/virtualNetworks/*/read`。
    - 子网资源上的 `Microsoft.Network/virtualNetworks/subnets/join/action`。
    
    若要详细了解如何将 Azure RBAC 与网络配合使用，请参阅[网络内置角色](../role-based-access-control/built-in-roles.md#networking)。

- 新的角色分配有时可能需要长达 1 小时才能生效，覆盖整个堆栈的缓存权限。
- Azure 机器学习目前不支持[条件访问](../role-based-access-control/conditional-access-azure-management.md)。

## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
- [教程：训练模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
