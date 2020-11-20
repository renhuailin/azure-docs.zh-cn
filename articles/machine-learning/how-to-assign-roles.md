---
title: 管理工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用 azure RBAC)  (Azure 基于角色的访问控制访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 11/09/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperfq2
ms.openlocfilehash: 6cd4bbec89e955c398f7cb6e37ba5c3dcc6427ea
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961220"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问权限

本文介绍如何管理 (授权) 到 Azure 机器学习工作区的访问权限。 使用 azure [RBAC)  (azure 基于角色的访问控制](../role-based-access-control/overview.md)来管理对 azure 资源的访问，例如创建新资源或使用现有资源的能力。 Azure Active Directory 中 (Azure AD) 的用户将分配特定角色，这将授予对资源的访问权限。 Azure 提供内置角色和创建自定义角色的功能。

> [!TIP]
> 尽管本文重点介绍了 Azure 机器学习，但 Azure ML 依赖的单个服务提供了其自己的 RBAC 设置。 例如，使用本文中的信息可以配置谁可以将评分请求提交到在 Azure Kubernetes Service 上部署为 web 服务的模型。 但 Azure Kubernetes 服务提供了其自己的 Azure 角色集。 有关 Azure 机器学习的服务特定 RBAC 信息，请参阅以下链接：
>
> * [控制对 Azure Kubernetes 群集资源的访问](../aks/azure-ad-rbac.md)
> * [使用 Azure RBAC 进行 Kubernetes 授权](../aks/manage-azure-rbac.md)
> * [使用 Azure RBAC 访问 blob 数据](/storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> 应用某些角色可能会限制 Azure 机器学习 studio 中其他用户的 UI 功能。 例如，如果用户的角色无法创建计算实例，则创建计算实例的选项将无法在 studio 中使用。 此行为是预期行为，并阻止用户尝试尝试返回 "拒绝访问" 错误的操作。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，当创建新的 Azure 机器学习工作区时，它附带三个默认角色。 可以将用户添加到工作区，并将他们分配给这些内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **读者** | 工作区中的只读操作。 读取者可以在工作区中列出和查看资产，包括[数据存储](how-to-access-data.md)凭据。 读取者不能创建或更新这些资产。 |
| **参与者** | 在工作区中查看、创建、编辑或删除（如果适用）资产。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 Web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括能够在工作区中查看、创建、编辑或删除（如果适用）资产。 此外，还可以更改角色分配。 |
| **自定义角色** | 允许你自定义对工作区中特定控件或数据平面操作的访问权限。 例如，提交运行、创建计算、部署模型或注册数据集。 |

> [!IMPORTANT]
> 在 Azure 中，角色访问的作用域可以限定为多个级别。 例如，对工作区具有所有者访问权限的人可能没有对包含工作区的资源组的所有者访问权限。 有关详细信息，请参阅 [AZURE RBAC 的工作原理](../role-based-access-control/overview.md#how-azure-rbac-works)。

当前没有其他特定于 Azure 机器学习的内置角色。 有关内置角色的详细信息，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="manage-workspace-access"></a>管理工作区访问权限

如果你是工作区的所有者，则可以为工作区添加和删除角色。 还可以将角色分配给用户。 使用以下链接了解如何管理访问权限：
- [Azure 门户 UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager 模板](../role-based-access-control/role-assignments-template.md)

如果已安装 [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)，则可使用 CLI 命令为用户分配角色：

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` 字段是 Azure Active Directory 实例中现有用户的电子邮件地址，该实例中包含工作区父订阅。 下面是此命令的用法示例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> “az ml workspace share”命令对 Azure Active Directory B2B 的联合帐户不起作用。 请使用 Azure UI 门户而不是命令。

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

部署后，此角色在指定工作区中可用。 现在，可以在 Azure 门户中添加和分配此角色。 或者，可以使用 `az ml workspace share` CLI 命令将此角色分配给用户：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

有关自定义角色的详细信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。 

### <a name="azure-machine-learning-operations"></a>Azure 机器学习操作

有关可用于自定义角色的操作（Actions 和 NotActions）的详细信息，请参阅[资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)。 你还可以使用以下 Azure CLI 命令来列出操作：

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>列出自定义角色

在 Azure CLI 中运行以下命令：

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

若要查看特定自定义角色的角色定义，请使用以下 Azure CLI 命令。 的 `<role-name>` 格式应与上述命令返回的格式相同：

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
| 发布管道终结点 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| 在 AKS/ACI 资源上部署已注册的模型 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 针对已部署的 AKS 终结点进行评分 | 不是必需 | 不是必需 | 允许以下权限的“所有者”角色、“参与者”角色或自定义角色：`"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"`（未使用 Azure Active Directory 身份验证时）或 `"/workspaces/read"`（使用令牌身份验证时） |
| 使用交互式笔记本访问存储 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| 创建新的自定义角色 | 所有者、参与者或自定义角色允许 `Microsoft.Authorization/roleDefinitions/write` | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |

> [!TIP]
> 如果第一次尝试创建工作区时遇到失败，请确保角色允许 `Microsoft.MachineLearningServices/register/action`。 可以通过此操作将 Azure 机器学习资源提供程序注册到 Azure 订阅。

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>用户为 Azure ML 计算群集分配的托管标识

若要将用户分配的标识分配给 Azure 机器学习的计算群集，需要具有写入权限才能创建计算和 [托管标识操作员角色](../role-based-access-control/built-in-roles.md#managed-identity-operator)。 有关具有托管标识的 Azure RBAC 的详细信息，请参阅 [如何管理用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>MLflow 操作

若要在 Azure 机器学习工作区中执行 MLflow 操作，请使用自定义角色的以下作用域：

| MLflow 操作 | 范围 |
| --- | --- |
| 列出工作区跟踪存储中的所有试验，按 ID 获取试验，按名称获取试验 | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| 创建试验并命名，为实验设置标签，还原标记为删除的试验| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| 删除试验 | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| 获取运行以及相关的数据和元数据，获取指定运行的指定指标的所有值的列表，列出运行的项目 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| 在试验中新建运行，删除运行，还原已删除的运行，记录当前运行下的指标，为运行设置标签，删除运行上的标签，记录运行所使用的参数（键值对），记录运行的一批指标、参数和标签，更新运行状态 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| 按名称获取注册的模型，提取注册表中所有已注册的模型的列表，搜索已注册的模型，为每个请求阶段搜索最新的版本模型，获取已注册的模型的版本，搜索模型版本，获取用于存储模型版本项目的 URI，并按实验 id 搜索运行。 | `Microsoft.MachineLearningServices/workspaces/models/read` |
| 创建新的已注册模型，更新已注册的模型名称/说明，重命名现有的已注册模型，创建新版本的模型，更新模型版本的说明，将已注册的模型转换为一个阶段 | `Microsoft.MachineLearningServices/workspaces/models/write` |
| 删除已注册模型及其所有版本，删除已注册模型的特定版本 | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>示例自定义角色

### <a name="data-scientist"></a>数据科学家

允许数据科学家执行工作区内的所有操作， **但** 以下情况除外：

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

### <a name="data-scientist-restricted"></a>数据科学家限制

允许的操作中不包含通配符的更受限制的角色定义。 它可以在工作区中执行所有操作，但以下操作 **除外**：

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

允许数据科学家执行所有 MLflow AzureML 支持的操作， **但** 以下情况除外：

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

允许你执行工作区范围内的所有操作，但以下情况 **除外**：

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
### <a name="data-labeler"></a>数据 labeler

允许你定义仅限于标记数据的角色：

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

## <a name="troubleshooting"></a>疑难解答

使用 Azure 基于角色的访问控制 (Azure RBAC) 时，请注意以下几点：

- 在 Azure 中创建资源（如工作区）时，不会直接成为资源的所有者。 你的角色继承自你在该订阅中获得授权的最高作用域角色。 例如，如果你是网络管理员，并且有权创建机器学习工作区，则会为该工作区分配网络管理员角色，而不是所有者角色。

- 若要在工作区中执行配额操作，需要订阅级别权限。 这意味着，只有当你在订阅作用域具有写入权限时，才能为你的托管计算资源设置订阅级配额或工作区级配额。

- 针对同一 Azure Active Directory 用户的两个角色分配具有冲突的 Actions/NotActions 部分时，如果操作在某个角色的 NotActions 中列出，但也在另一个角色中作为 Actions 列出，则此类操作可能不会生效。 若要详细了解 Azure 如何分析角色分配，请参阅 [Azure RBAC 如何确定用户是否有权访问资源](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- 若要在 VNet 中部署计算资源，需要显式拥有以下操作的权限：
    - `Microsoft.Network/virtualNetworks/join/action` 在 VNet 资源上。
    - `Microsoft.Network/virtualNetworks/subnet/join/action` 子网资源。
    
    有关 Azure RBAC with 网络的详细信息，请参阅 [联网内置角色](../role-based-access-control/built-in-roles.md#networking)。

- 在整个堆栈中，新的角色分配可以在缓存的权限上生效，这有时最多需要1小时。

## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
- [教程：训练模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)