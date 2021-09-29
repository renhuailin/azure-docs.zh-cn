---
title: 排查常见部署错误
description: 说明如何解决使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be79c143cf38602dbe8f406d8aa3e2988de2c9d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785223"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>排查使用 Azure Resource Manager 时的常见 Azure 部署错误

本文介绍了一些常见的 Azure 部署错误，并提供了有关如何解决这些错误的信息。 如果找不到部署错误的错误代码，请参阅[查找错误代码](#find-error-code)。

如果需要某个错误代码的信息，而本文没有提供该信息，请告知我们。 在此页的底部，你可以留下反馈。 我们将跟踪 GitHub 问题的反馈。

## <a name="error-codes"></a>错误代码

| 错误代码 | 缓解措施 | 详细信息 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 遵循存储帐户的命名限制。 | [解析存储帐户名称](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 查看可用的存储帐户属性。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 群集或区域没有可用的资源或无法支持所请求的 VM 大小。 稍后重试请求，或者请求不同的 VM 大小。 | [Linux 预配和分配问题](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)、[Windows 预配和分配问题](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows)和[排查分配失败问题](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | 等待并发操作完成。 | |
| AuthorizationFailed | 帐户或服务主体没有足够的访问权限，无法完成部署。 请检查帐户所属的角色，及其与部署范围相对应的访问权限。<br><br>所需的资源提供程序未注册时，可能会收到此错误。 | [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[解决注册问题](error-register-resource-provider.md) |
| BadRequest | 发送的部署值与资源管理器预期的值不匹配。 请检查内部状态消息，获取故障排除帮助。 | [模板引用](/azure/templates/)和[支持的位置](resource-location.md) |
| Conflict | 在资源的当前状态下不允许所请求的操作。 例如，仅当创建 VM 或该 VM 已取消分配时，才允许磁盘重设大小。 | |
| DeploymentActiveAndUneditable | 等待目标为此资源组的并发部署完成。 | |
| DeploymentFailedCleanUp | 在完整模式下部署时，模板中不存在的任何资源都将被删除。 当没有足够的权限删除模板中所有不存在的资源时，会出现此错误。 若要避免此错误，请将部署模式更改为“增量”。 | [Azure 资源管理器部署模式](deployment-modes.md) |
| DeploymentNameInvalidCharacters | 部署名称只能包含字母、数字、"-"、"." 或 "_"。 | |
| DeploymentNameLengthLimitExceeded | 部署名称限制为 64 个字符。  | |
| DeploymentFailed | DeploymentFailed 错误为常规错误，未提供解决错误所需的详细信息。 请查看错误代码的错误详情，其中提供了详细信息。 | [查找错误代码](#find-error-code) |
| DeploymentQuotaExceeded | 如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 | [解决部署计数超出 800 的错误](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | 简化模板以减小大小。 | [解决模板大小错误](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS 记录名称必须唯一。 输入不同的名称。 | |
| ImageNotFound | 检查 VM 映像设置。 |  |
| InUseSubnetCannotBeDeleted | 如果尝试更新资源，但已通过删除并创建资源处理了请求，则可能会出现此错误。 请确保指定所有未更改的值。 | [更新资源](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 获取相应租户的访问令牌。 只能从帐户所属的租户获取该令牌。 | |
| InvalidContentLink | 很可能尝试过链接到不可用的嵌套模板。 请仔细检查提供给嵌套模板的 URI。 如果模板在存储帐户中存在，请确保 URI 可访问。 可能需要传递 SAS 令牌。 目前，无法链接到位于 [Azure 存储防火墙](../../storage/common/storage-network-security.md)后面的存储帐户中的模板。 考虑将模板移到其他存储库，如 GitHub。 | [链接的模板](linked-templates.md) |
| InvalidDeploymentLocation | 在订阅级别部署时，你为以前使用的部署名称提供了不同的位置。 | [订阅级别部署](deploy-to-subscription.md) |
| InvalidParameter | 为资源提供的某个值与预期值不匹配。 许多不同的条件可能会导致此错误。 例如，密码可能不符合要求，或者 blob 名称可能不正确。 错误消息应表明哪个值需要更正。 | |
| InvalidRequestContent | 部署值包含无法识别的值，或者缺少必需的值。 确认资源类型的值。 | [模板参考](/azure/templates/) |
| InvalidRequestFormat | 在运行部署时启用调试日志记录功能，并验证请求的内容。 | [调试日志记录](#enable-debug-logging) |
| InvalidResourceNamespace | 检查在  type 属性中指定的资源命名空间。 | [模板参考](/azure/templates/) |
| InvalidResourceReference | 资源尚不存在，或错误地引用了它。 请检查是否需要添加依赖项， 并验证所使用的  reference 函数是否包括方案所需的参数。 | [解决依赖项问题](error-not-found.md) |
| InvalidResourceType | 检查在  type 属性中指定的资源类型。 | [模板参考](/azure/templates/) |
| InvalidSubscriptionRegistrationState | 向资源提供程序注册订阅。 | [解决注册问题](error-register-resource-provider.md) |
| InvalidTemplate | 检查模板语法是否存在错误。 | [解决模板无效的问题](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 删除不必要的依赖项。 | [解决循环依赖项](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | 简化模板以减小大小。 | [解决模板大小错误](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | 检查帐户所属的租户是否与要部署到的资源组所属的租户相同。 | |
| LinkedInvalidPropertyId | 无法正确解析资源的资源 ID。 请检查是否提供了资源 ID 的所有必需值，包括订阅 ID、资源组名称、资源类型、父资源名称（如果需要）、资源名称。 | |
| LocationRequired | 提供资源的位置。 | [设置位置](resource-location.md) |
| MismatchingResourceSegments | 请确保嵌套资源的名称和类型中包含正确数量的段。 | [解决资源段](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | 检查资源提供程序注册状态，以及支持的位置。 | [解决注册问题](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | 向资源提供程序注册订阅。 | [解决注册问题](error-register-resource-provider.md) |
| NoRegisteredProviderFound | 检查资源提供程序注册状态。 | [解决注册问题](error-register-resource-provider.md) |
| NotFound | 你可能在尝试部署与父资源并行的依赖资源。 请检查是否需要添加依赖项。 | [解决依赖项问题](error-not-found.md) |
| OperationNotAllowed | 部署所尝试的操作超出订阅、资源组或区域的配额。 请尽可能修改部署，使之保持在配额范围内。 否则，请考虑请求更改配额。 | [解决配额问题](error-resource-quota.md) |
| ParentResourceNotFound | 确保在创建子资源之前存在父资源。 | [解决父资源问题](error-parent-resource.md) |
| PasswordTooLong | 你可能选择了包含太多字符的密码，或在将密码值作为参数传递之前将其转换成了一个安全字符串。 如果模板包括一个 **安全字符串** 参数，则不需要将值转换为安全字符串。 请以文本形式提供密码值。 |  |
| PrivateIPAddressInReservedRange | 指定的 IP 地址包括 Azure 所需的地址范围。 请更改 IP 地址，避免使用保留的范围。 | [IP 地址](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | 指定的 IP 地址位于子网范围之外。 请更改 IP 地址，使之位于子网范围之内。 | [IP 地址](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | 已部署资源上的某些属性不能更改。 更新资源时，请仅更改允许的属性。 | [更新资源](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | 订阅中的某个资源策略阻止你在部署期间尝试执行的操作。 请找出阻止该操作的策略。 如果可能，请更改部署，使之符合策略的限制。 | [解决策略问题](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | 提供不包含保留名称的资源名称。 | [保留的资源名称](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 等待删除操作完成。 | |
| ResourceGroupNotFound | 检查部署的目标资源组的名称。 目标资源组必须已存在于订阅中。 请检查订阅上下文。 | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 部署引用了一个无法解析的资源。 请验证所使用的  reference 函数是否包括方案所需的参数。 | [解决引用问题](error-not-found.md) |
| ResourceQuotaExceeded | 部署尝试创建的资源超过了订阅、资源组或区域的配额。 请尽可能修改基础结构，使之保持在配额范围内。 否则，请考虑请求更改配额。 | [解决配额问题](error-resource-quota.md) |
| SkuNotAvailable | 选择可在所选位置中使用的 SKU（例如 VM 大小）。 | [解决 SKU 问题](error-sku-not-available.md) |
| StorageAccountAlreadyExists | 为存储帐户提供唯一名称。 | [解析存储帐户名称](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | 为存储帐户提供唯一名称。 | [解析存储帐户名称](error-storage-account-name.md) |
| StorageAccountNotFound | 检查尝试使用的存储帐户的订阅、资源组和名称。 | |
| SubnetsNotInSameVnet | 一个虚拟机只能有一个虚拟网络。 部署多个 NIC 时，请确保它们属于同一虚拟网络。 | [多个 NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | 无法访问用于部署的指定订阅。 有可能是订阅 ID 错误，部署模板的用户没有足够的权限部署到订阅，或者订阅 ID 格式错误。 使用嵌套部署[跨范围部署](./deploy-to-resource-group.md)时，应为订阅提供 GUID。 | |
| SubscriptionNotRegistered | 部署资源时，必须为订阅注册资源提供程序。 使用 Azure 资源管理器模板进行部署时，会在订阅中自动注册资源提供程序。 有时，自动注册未及时完成。 若要避免此间歇性错误，请在部署前注册资源提供程序。 | [解决注册问题](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | 删除不必要的依赖项。 | [解决循环依赖项](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | 减少单个部署的资源组数。 | [跨范围部署](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>查找错误代码

可能接收到的错误有两种类型：

* 验证错误
* 部署错误

验证错误源于部署之前可确定的方案。 原因包括模板中的语法错误，或尝试部署超出订阅配额的资源。 部署错误源于部署过程中发生的条件。 原因包括尝试访问并行部署的资源。

两种类型的错误都会返回用于对部署进行故障排除的错误代码。 两种类型的错误都会显示在[活动日志](../../azure-monitor/essentials/activity-log.md)中。 但是，验证错误不会显示在部署历史记录中，因为部署从未启动。

### <a name="validation-errors"></a>验证错误

通过门户部署时，提交值后会看到验证错误。

![显示门户验证错误](./media/common-deployment-errors/validation-error.png)

选择消息获取更多详细信息。 下图显示了一条 **InvalidTemplateDeployment** 错误，以及一条指出策略阻止了部署的消息。

![显示验证详细信息](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>部署错误

如果操作通过验证但在部署期间失败，将收到部署错误。

若要通过 PowerShell 查看部署错误代码和消息，请使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

若要通过 Azure CLI 查看部署错误代码和消息，请使用：

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

在门户中，选择通知。

![通知错误](./media/common-deployment-errors/notification.png)

查看有关部署的更多详细信息。 选择查找有关错误的详细信息的选项。

![部署失败](./media/common-deployment-errors/deployment-failed.png)

看到错误消息和错误代码。 请注意有两个错误代码。 第一个错误代码 (DeploymentFailed) 表示常规错误，不提供解决错误所需的详细信息。 第二个错误代码 (**StorageAccountNotFound**) 提供所需的详细信息。

![错误详细信息](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>启用调试日志记录

有时需要有关请求和响应的详细信息才能了解出现的问题。 部署过程中，可以请求在部署期间记录更多信息。

### <a name="powershell"></a>PowerShell

在 PowerShell 中，将 **DeploymentDebugLogLevel** 参数设置为 All、ResponseContent 或 RequestContent。

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

使用以下 cmdlet 检查请求内容：

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

或者，使用以下信息检查响应内容：

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

此信息可帮助确定模板中某个值的设置是否错误。

### <a name="azure-cli"></a>Azure CLI

目前，Azure CLI 不支持启用调试日志记录，但可以检索调试日志记录。

使用以下命令查看部署操作：

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

使用以下命令检查请求内容：

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

使用以下命令检查响应内容：

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>嵌套模板

若要记录嵌套模板的调试信息，请使用 **debugSetting** 元素。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>创建故障排除模板

在某些情况下，排除模板故障的最简单方法是测试部分模板。 可以创建一个简化的模板，通过该模板将测试重点放在最可能导致错误的部分。 例如，假设在引用资源时收到错误消息。 创建一个模板，该模板返回可能导致问题的部分，而不是处理整个模板。 这可以帮助确定传入的是否是正确的参数，是否正确使用模板函数，以及是否获得所需的资源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

或者，假设收到部署错误，而你认为它与依赖关系设置错误有关。 通过将模板分解为多个简化模板进行测试。 首先，创建仅部署单个资源（如 SQL Server）的模板。 确保已正确定义该资源时，再添加依赖于它的资源（如 SQL 数据库）。 正确定义这两个资源后，添加其他从属资源（如审核策略）。 在每个测试部署之间，删除资源组，以确保充分测试依赖关系。

## <a name="next-steps"></a>后续步骤

* 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](template-tutorial-troubleshoot.md)
* 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](deployment-history.md)。
