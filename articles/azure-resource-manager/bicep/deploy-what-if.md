---
title: Bicep 部署 What-if
description: 在部署 Bicep 文件之前，确定资源将发生哪些更改。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: tomfitz
ms.openlocfilehash: 157db0aa85fe086e656cfc98c7b94527416350d0
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027742"
---
# <a name="bicep-deployment-what-if-operation"></a>Bicep 部署 what-if 操作

在部署 Bicep 文件之前，可以预览将发生的更改。 Azure 资源管理器提供 What-if 操作，让你在部署 Bicep 文件时了解资源发生的更改。 what-if 操作不会对现有资源进行任何更改， 而是预测在部署指定的 Bicep 文件时发生的更改。

可将 what-if 操作与 Azure PowerShell、Azure CLI 或 REST API 操作配合使用。 资源组、订阅、管理组合租户级部署支持 What-if。

## <a name="install-azure-powershell-module"></a>安装 Azure PowerShell 模块

若要在 PowerShell 中使用 what-if，则必须安装 Az 模块 4.2 或更高版本。

若要安装该模块，请使用：

```powershell
Install-Module -Name Az -Force
```

若要详细了解如何安装模块，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="install-azure-cli-module"></a>安装 Azure CLI 模块

若要在 Azure CLI 中使用 what-if，则必须安装 Azure CLI 2.14.0 或更高版本。 如果需要，请[安装 Azure CLI 的最新版本](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>查看结果

在 PowerShell 或 Azure CLI 中使用 what-if 时，输出包含进行了颜色编码的结果，方便你查看不同类型的更改。

![Bicep 部署 What-if 操作 fullresourcepayload 和更改类型](./media/deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本输出如下：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> What-if 操作无法解析[引用函数](./bicep-functions-resource.md#reference)。 每次将属性设置为包含引用函数的模板表达式时，What-if 都会报告属性将改变。 发生此行为的原因是，What-if 将属性的当前值（如布尔值 `true` 或 `false`）与未解析的模板表达式进行比较。 很明显，这些值不匹配。 部署 Bicep 文件时，只有在模板表达式解析为其他值时，属性才会更改。

## <a name="what-if-commands"></a>what-if 命令

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署 Bicep 文件前预览更改，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 或 [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)。 将 `-Whatif` 开关参数添加到部署命令。

* 对于资源组部署，请使用 `New-AzResourceGroupDeployment -Whatif`
* 对于订阅级别的部署，请使用 `New-AzSubscriptionDeployment -Whatif` 和 `New-AzDeployment -Whatif`

可使用 `-Confirm` 开关参数来预览所做的更改，让系统显示是否继续部署的提示。

* 对于资源组部署，请使用 `New-AzResourceGroupDeployment -Confirm`
* 对于订阅级别的部署，请使用 `New-AzSubscriptionDeployment -Confirm` 和 `New-AzDeployment -Confirm`

上述命令返回适用于手动检查的文本摘要。 若要获取可通过编程方式检查更改的对象，请使用 [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) 或 [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)。

* 对于资源组部署，请使用 `$results = Get-AzResourceGroupDeploymentWhatIfResult`
* 对于订阅级别的部署，请使用 `$results = Get-AzSubscriptionDeploymentWhatIfResult` 或 `$results = Get-AzDeploymentWhatIfResult`

### <a name="azure-cli"></a>Azure CLI

若要在部署 Bicep 文件前预览更改，请使用：

* [az deployment group what-if](/cli/azure/deployment/group#az_deployment_group_what_if)（适用于资源组部署）
* [az deployment sub what-if](/cli/azure/deployment/sub#az_deployment_sub_what_if)（适用于订阅级别部署）
* [az deployment mg what-if](/cli/azure/deployment/mg#az_deployment_mg_what_if)（适用于管理组部署）
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az_deployment_tenant_what_if)（适用于租户部署）

可以使用 `--confirm-with-what-if` 开关（或其缩写形式 `-c`）预览更改，并让系统显示是否继续部署的提示。 将此开关添加到：

* [az 部署组创建](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)。
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

例如，对于资源组部署，请使用 `az deployment group create --confirm-with-what-if` 或 `-c`。

上述命令返回适用于手动检查的文本摘要。 若要获取可通过编程方式检查更改的 JSON 对象，请使用 `--no-pretty-print` 开关。 例如，对于资源组部署，请使用 `az deployment group what-if --no-pretty-print`。

若要返回没有颜色的结果，请打开 [Azure CLI 配置](/cli/azure/azure-cli-configuration)文件。 将“no_color”设置为“yes”。

### <a name="azure-rest-api"></a>Azure REST API

对于 REST API，请使用：

* 对于资源组部署，请使用[部署 - What If](/rest/api/resources/deployments/whatif)
* [Deployments - What If At Subscription Scope](/rest/api/resources/deployments/whatifatsubscriptionscope)（适用于订阅部署）
* [Deployments - What If At Management Group Scope](/rest/api/resources/deployments/whatifatmanagementgroupscope)（适用于管理组部署）
* [Deployments - What If At Tenant Scope](/rest/api/resources/deployments/whatifattenantscope)（适用于租户部署）。

## <a name="change-types"></a>更改类型

what-if 操作列出六种不同的更改类型：

* **创建**：资源当前不存在，但已在 Bicep 文件中定义。 将创建该资源。
* **删除**：仅当为 JSON 模板部署使用 [完整模式](../templates/deployment-modes.md)时，此更改类型才适用。 资源存在，但未在 Bicep 文件中定义。 使用完整模式时，将删除该资源。 此更改类型仅包括[支持完整模式删除](../templates/deployment-complete-mode-deletion.md)的资源。
* **忽略**：资源存在，但未在 Bicep 文件中定义。 不会部署或修改资源。
* **无更改**：资源存在，且在 Bicep 文件中定义。 将重新部署资源，但资源的属性不会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。
* **修改**：资源存在，且在 Bicep 文件中定义。 将重新部署资源，且资源的属性会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。
* **部署**：资源存在，并在 Bicep 文件中定义。 将重新部署资源。 资源的属性可能会更改，也可能不会更改。 当没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当 [ResultFormat](#result-format) 设置为 `ResourceIdOnly` 时，才会看到此状况。

## <a name="result-format"></a>结果格式

控制返回的有关预测更改的详细信息级别。 可以使用两个选项：

* **FullResourcePayloads** - 返回将会更改的资源的列表，以及将会更改的属性的相关详细信息。
* **ResourceIdOnly** - 返回将会更改的资源列表

默认值为 FullResourcePayloads。

对于 PowerShell 部署命令，请使用 `-WhatIfResultFormat` 参数。 在编程对象命令中，使用 `ResultFormat` 参数。

对于 Azure CLI，请使用 `--result-format` 参数。

以下结果显示了两种不同的输出格式：

* 完整资源有效负载

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

* 仅限资源 ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>运行 what-if 操作

### <a name="set-up-environment"></a>设置环境

为了了解 what-if 的工作原理，让我们运行一些测试。 首先，部署一个[用于创建虚拟网络的 Bicep 文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.bicep)。 将使用此虚拟网络来测试 what-if 如何报告更改。 下载 Bicep 文件的副本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile "what-if-before.bicep"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-file "what-if-before.bicep"
```

---

### <a name="test-modification"></a>测试修改

部署完成后，即可测试 what-if 操作。 这一次，将部署一个[用于更改虚拟网络的 Bicep 文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.bicep)。 该模板中缺少一个原始标记，已删除了一个子网，并且已更改了地址前缀。 下载 Bicep 文件的副本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateFile "what-if-after.bicep"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-file "what-if-after.bicep"
```

---

what-if 输出类似于：

![Bicep 部署 What-if 操作输出](./media/deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本输出如下：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner:                    "Team A"
    + properties.enableVmProtection: false
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

请注意，输出顶部的颜色用于指示更改类型。

输出的底部显示了“已删除所有者”标记。 地址前缀已从 10.0.0.0/16 更改为 10.0.0.0/15。 已删除名为 subnet001 的子网。 请记住，这些更改并未部署。 如果部署 Bicep 文件，则可预览会发生的更改。

列出为已删除的某些属性实际上不会更改。 当属性不在 Bicep 文件中时，它们可能被错误地报告为已删除，但在部署过程中会自动设置为默认值。 此结果在 what-if 响应中被视为“干扰信息”。 最终部署的资源将具有为属性设置的值。 当 what-if 操作成熟时，将从结果中筛选出这些属性。

## <a name="programmatically-evaluate-what-if-results"></a>以编程方式评估 what-if 结果

现在，让我们将命令设置为变量，以编程方式评估 what-if 结果。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  --template-file "what-if-after.bicep"
```

可以看到每项更改的摘要。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-file "what-if-after.bicep" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>确认删除

若要在部署 Bicep 文件之前预览所做的更改，请在部署命令中使用 confirm 开关参数。 如果更改符合预期，请确认你想要完成此部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Confirm `
  -TemplateFile "what-if-after.bicep"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --confirm-with-what-if \
  --template-file "what-if-after.bicep"
```

---

![Bicep 部署 What-if 操作输出部署模式完成](./media/deploy-what-if/resource-manager-deployment-whatif-output-confirm.png)

文本输出如下：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner:                    "Team A&quot;
    + properties.enableVmProtection: false
    ~ properties.addressSpace.addressPrefixes: [
      - 0: &quot;10.0.0.0/16&quot;
      + 0: &quot;10.0.0.0/15&quot;
      ]
    ~ properties.subnets: [
      - 0:

          name:                     &quot;subnet001&quot;
          properties.addressPrefix: &quot;10.0.0.0/24&quot;

      ]

Resource changes: 1 to modify.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

你会看到预期的更改，并且可以确认你想要运行此部署。

## <a name="sdks"></a>SDK

可以通过 Azure SDK 使用 What-if 操作。

* 对于 Python，请使用 [What-if](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)。

* 对于 Java，请使用 [DeploymentWhatIf 类](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)。

* 对于 .NET，请使用 [DeploymentWhatIf 类](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)。

## <a name="next-steps"></a>后续步骤

* 要在管道中使用 What-if 操作，请参阅[在管道中使用 What-If 测试 ARM 模板](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)。
* 如果发现 What-if 操作出现了错误的结果，请在 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 上报告问题。
* 有关介绍如何使用 what if 的 Microsoft Learn 模块，请参阅[使用 What-if 和 ARM 模板测试工具包来预览更改和验证 Azure 资源](/learn/modules/arm-template-test/)。
