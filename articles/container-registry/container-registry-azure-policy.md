---
title: 使用 Azure Policy 实现合规性
description: 分配 Azure Policy 中的内置策略定义来审核 Azure 容器注册表的合规性
ms.topic: article
ms.date: 08/10/2021
ms.openlocfilehash: d8f986a3a857f622248daa75d0402f1abfbf8a7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722486"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>使用 Azure Policy 审核 Azure 容器注册表的合规性

[Azure Policy](../governance/policy/overview.md) 是 Azure 中的一项服务，可用于创建、分配和管理策略定义。 这些策略定义将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。

本文介绍适用于 Azure 容器注册表的内置策略定义。 可以使用这些定义来审核新的和现有的注册表的合规性。

使用 Azure Policy 免费。

## <a name="built-in-policy-definitions"></a>内置策略定义

以下内置策略定义特定于 Azure 容器注册表：

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="create-policy-assignments"></a>创建策略分配

* 使用 [Azure 门户](../governance/policy/assign-policy-portal.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、[资源管理器模板](../governance/policy/assign-policy-template.md) 或 Azure Policy SDK 创建策略分配。
* 将策略分配的作用域限定为资源组、订阅或 [Azure 管理组](../governance/management-groups/overview.md)。 容器注册表策略分配适用于该作用域内现有的和新的容器注册表。
* 可以随时启用或禁用[策略实施](../governance/policy/concepts/assignment-structure.md#enforcement-mode)功能。

> [!NOTE]
> 在创建或更新某个策略分配后，需要花费一些时间，该分配才会评估定义范围中的资源。 请查看有关[策略评估触发器](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)的信息。

## <a name="review-policy-compliance"></a>查看策略合规性

可以使用 Azure 门户、Azure 命令行工具或 Azure Policy SDK 来访问策略分配生成的合规性信息。 有关详细信息，请参阅[获取 Azure 资源的合规性数据](../governance/policy/how-to/get-compliance-data.md)。

有多种可能的原因会导致资源不合规。 若要确定原因或查找导致问题的变更，请参阅[确定不合规情况](../governance/policy/how-to/determine-non-compliance.md)。

### <a name="policy-compliance-in-the-portal"></a>门户中的策略合规性：

1. 选择“所有服务”  ，然后搜索“策略”。
1. 选择“合规性”。
1. 将筛选器用于限制合规性状态，或用于搜索策略。

    ![门户中的策略合规性](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. 选择一个策略来查看聚合合规性详细信息和事件。 然后，根据需要选择一个适用于资源合规性的特定注册表。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI 中的策略合规性

也可使用 Azure CLI 来获取合规性数据。 例如，在 CLI 中使用 [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) 命令获取已应用的 Azure 容器注册表策略的策略 ID：

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

示例输出：

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

然后运行 [az policy state list](/cli/azure/policy/state#az_policy_state_list)，以便返回特定策略 ID 下所有资源的 JSON 格式合规性状态：

```azurecli
az policy state list \
  --resource <policyID>
```

也可运行 [az policy state list](/cli/azure/policy/state#az_policy_state_list)，以便返回特定注册表资源（例如 myregistry）的 JSON 格式合规性状态：

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure Policy [定义](../governance/policy/concepts/definition-structure.md)和[效果](../governance/policy/concepts/effects.md)。

* 创建[自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)。

* 详细了解 Azure 中的[治理功能](../governance/index.yml)。
