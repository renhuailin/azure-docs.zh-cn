---
title: 将资源部署到租户
description: 介绍如何在 Azure 资源管理器模板中的租户范围内部署资源。
ms.topic: conceptual
ms.date: 09/14/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0e998c4c26afc456a83cdb0c06a5ccb3906364df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606563"
---
# <a name="tenant-deployments-with-arm-templates"></a>使用 ARM 模板进行租户部署

随着组织的成熟，你可能需要在 Azure AD 租户中定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 通过租户级模板，可以声明的方式在全局级别应用策略和分配角色。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到租户级别。 本部分列出了支持的资源类型。

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

对于部署到管理组、订阅或资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

对于创建管理组，请使用：

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

若要创建订阅，请使用：

* [aliases](/azure/templates/microsoft.subscription/aliases)

对于管理成本，请使用：

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

若要配置门户，请使用：

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

内置策略定义是租户级别的资源，但你无法在租户中部署自定义策略定义。 有关为资源分配内置策略定义的示例，请参阅 [tenantResourceId 示例](./template-functions-resource.md#tenantresourceid-example)。

## <a name="schema"></a>架构

用于租户部署的架构与资源组部署的架构不同。

对于模板，请使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  ...
}
```

对于所有部署范围，参数文件的架构都相同。 对于参数文件，请使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  ...
}
```

## <a name="required-access"></a>所需访问权限

部署模板的主体必须具有在租户范围中创建资源的权限。 该主体必须有权执行部署操作 (`Microsoft.Resources/deployments/*`) 和创建模板中定义的资源。 例如，若要创建管理组，主体必须在租户范围内具有参与者权限。 若要创建角色分配，主体则必须具有所有者权限。

Azure Active Directory 的全局管理员不自动拥有分配角色的权限。 若要在租户范围内实现模板部署，全局管理员必须执行以下步骤：

1. 提升帐户访问权限，使其自身可分配角色。 有关详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

1. 向需要部署模板的主体分配所有者或参与者角色。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主体现已具有部署模板所需的权限。

## <a name="deployment-commands"></a>部署命令

用于租户部署的命令与资源组部署使用的命令不同。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 Azure PowerShell，请使用 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure 门户部署资源](deploy-portal.md)
* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [使用 ARM 模板和 Azure 资源管理器 REST API 部署资源](deploy-rest.md)
* [使用部署按钮从 GitHub 存储库部署模板](deploy-to-azure-button.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>部署位置和名称

对于租户级别的部署，必须提供部署位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。 [订阅](deploy-to-subscription.md)和[管理组](deploy-to-management-group.md)部署也需要位置。 对于[资源组](deploy-to-resource-group.md)部署，资源组的位置用于存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 _azuredeploy.json_ 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 例如，如果在 centralus 中创建名为“deployment1”的租户部署，则以后不能创建另一个名为“deployment1”但位置为“westus”的部署   。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到租户时，可以将资源部署到：

* 租户
* 租户中的管理组
* subscriptions
* 资源组

可以将[扩展资源](scope-extension-resources.md)的范围设置为与部署目标不同的范围。

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-tenant"></a>将范围设定为租户

在模板的资源部分中定义的资源将应用于租户。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>将范围设定为管理组

若要以租户内的管理组为目标，请添加嵌套部署并指定 `scope` 属性。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>订阅的范围

还可以将租户中的订阅作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以租户中的订阅为目标，请使用嵌套部署和 `subscriptionId` 属性。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>将范围限定于资源组

还可以将租户中的资源组作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以租户中的资源组为目标，请使用嵌套部署。 设置 `subscriptionId` 和 `resourceGroup` 属性。 不要为嵌套部署设置位置，因为它部署在资源组的位置。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>创建管理组

以下模板用于创建管理组。

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

如果帐户没有部署到租户的权限，仍然可以通过部署到另一个范围来创建管理组。 有关详细信息，请参阅[管理组](deploy-to-management-group.md#management-group)。

## <a name="assign-role"></a>分配角色

以下模板用于在租户范围内分配角色。

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板分配 Azure 角色](../../role-based-access-control/role-assignments-template.md)。
* 还可在[订阅级别](deploy-to-subscription.md)或[管理组级别](deploy-to-management-group.md)部署模板。
