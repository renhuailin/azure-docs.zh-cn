---
title: 快速入门：通过 Python 进行新策略分配
description: 本快速入门介绍如何使用 Python 创建 Azure Policy 分配以识别不合规的资源。
ms.date: 10/01/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: fd9328fdc3f925756652853a81e8b9c77a0266e7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350791"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>快速入门：使用 Python 创建策略分配以识别不合规的资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 在本快速入门中，我们将创建策略分配，以识别未使用托管磁盘的虚拟机。 完成后，我们便可以识别不合规的虚拟机。 

Python 库用于从命令行或脚本管理 Azure 资源。 本指南介绍如何使用 Python 库来创建策略分配。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>添加 Policy 库

若要使 Python 能够与 Azure Policy 配合使用，必须添加库。 此库适用于可使用 Python 的任何情况，包括 [Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 请确保安装最新的 Python（至少为 3.8）。 如果尚未安装，请在 [Python.org](https://www.python.org/downloads/) 下载。

1. 请确保安装最新的 Azure CLI（至少为 2.5.1）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 在下面的示例中需要使用 Azure CLI，这样 Python 才能使用基于 CLI 的身份验证。 有关其他选项的信息，请参阅[使用适用于 Python 的 Azure 管理库进行身份验证](/azure/developer/python/azure-sdk-authenticate)。

1. 通过 Azure CLI 进行身份验证。

   ```azurecli
   az login
   ```

1. 在所选的 Python 环境中，安装 Azure Policy 所需的库：

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core

   # Add the Azure identity library for Python
   pip install azure.identity
   ```

   > [!NOTE]
   > 如果已为所有用户安装了 Python，则必须从提升的控制台运行这些命令。

1. 验证是否已安装这些库。 `azure-mgmt-policyinsights` 应为 0.5.0 或更高版本，`azure-mgmt-resource` 应为 9.0.0 版或更高版本，`azure-cli-core` 应为 2.5.0 或更高版本  。

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core azure.identity
   ```

## <a name="create-a-policy-assignment"></a>创建策略分配

本快速入门将创建一个策略分配，并分配“审核未使用托管磁盘的 VM”(`06a78e20-9358-41c9-923c-fb736d382a4d`) 定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

运行以下代码创建新的策略分配：

```python
# Import specific methods and models from other libraries
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment, Identity, UserAssignedIdentitiesValue, PolicyAssignmentUpdate
from azure.identity import AzureCliCredential

# Set subscription
subId = "{subId}"
assignmentLocation = "westus2"

# Get your credentials from Azure CLI (development only!) and get your subscription list
credential = AzureCliCredential()
policyClient = PolicyClient(credential, subId, base_url=none)

# Create details for the assignment
policyAssignmentIdentity = Identity(type="SystemAssigned")
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", description="Shows all virtual machines not using managed disks", identity=policyAssignmentIdentity, location=assignmentLocation)

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

上述命令使用以下信息：

分配详细信息:
- subId - 你的订阅。 需要使用它进行身份验证。 请将 `{subId}` 替换为你的订阅。
- **display_name** - 策略分配的显示名称。 本例使用了“审核未使用托管磁盘分配的虚拟机”  。
- policy_definition_id - 策略定义路径，用作创建分配的依据。 在本例中，它为策略定义“审核未使用托管磁盘的 VM”的 ID  。 在此示例中，策略定义是内置的，路径不包括管理组或订阅信息。
- **作用域** - 作用域确定要对哪些资源或资源分组实施策略分配。 它的范围可以从管理组到单个资源。 请确保将 `{scope}` 替换为以下某个模式：
  - 管理组：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 订阅：`/subscriptions/{subscriptionId}`
  - 资源组：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 资源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **说明** - 有关策略用途或将其分配到此范围的原因的更深入说明。

分配创建：

- 作用域 - 此作用域确定保存策略分配的位置。 分配详细信息中设置的作用域必须存在于此作用域内。
- 名称 - 分配的实际名称。 对于此示例，使用 _audit-vm-manageddisks_。
- 策略分配 - 在上一步中创建的 Python PolicyAssignment 对象。

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

使用以下信息来识别不符合所创建的策略分配的资源。 运行以下代码：

```python
# Import specific methods and models from other libraries
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions
from azure.identity import AzureCliCredential

# Set subscription
subId = "{subId}"

# Get your credentials from Azure CLI (development only!) and get your subscription list
credential = AzureCliCredential()
policyClient = PolicyInsightsClient(credential, subId, base_url=none)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id=subId, query_options=queryOptions)

# Show results
print(results)
```

将 `{subId}` 替换为要查看此策略分配的符合性结果的订阅。 有关其他作用域和汇总数据的方法的列表，请参阅[策略状态方法](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)。

结果应如以下示例所示：

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

结果与 Azure 门户视图中策略分配的“资源符合性”  选项卡中显示的内容相匹配。

## <a name="clean-up-resources"></a>清理资源

要删除创建的分配，请使用以下命令：

```python
# Import specific methods and models from other libraries
from azure.mgmt.resource.policy import PolicyClient
from azure.identity import AzureCliCredential

# Set subscription
subId = "{subId}"

# Get your credentials from Azure CLI (development only!) and get your subscription list
credential = AzureCliCredential()
policyClient = PolicyClient(credential, subId, base_url=none)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

请将 `{subId}` 替换为你的订阅，将 `{scope}` 替换为创建策略分配时使用的相同作用域。

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

要了解有关分配策略定义以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)
