---
title: 体验版的类型，Microsoft 商业应用商店
description: 商业应用商店中的体验版类型
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.author: trkeya
author: trkeya
ms.date: 09/09/2021
ms.openlocfilehash: 7825dff873afaf223cab7b86c73083027caccc72
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363627"
---
# <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器体验版

如果你具有 Azure 市场或 AppSource 上的产品/服务，但想要构建仅包含 Azure 资源的体验版，请使用此类型。 Azure 资源管理器 (ARM) 模板是设计的一种 Azure 资源编码容器，用于以最佳的方式表示解决方案。 体验版采用提供的 ARM 模板，并将它所需的所有资源部署到资源组。 这是虚拟机或 Azure 应用产品/服务的唯一体验版选项。

如果你不熟悉 ARM 模板的定义，请阅读“[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)”和“[了解 ARM 模板的结构和语法](../azure-resource-manager/templates/syntax.md)”，以便更好地了解如何构建和测试你自己的模板。

有关“托管”或“逻辑应用”体验版的信息，请参阅“[什么是体验版？](what-is-test-drive.md)”

## <a name="technical-configuration"></a>技术配置

部署模板，其中包含组成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 在“合作伙伴中心”中设置以下属性：

- **区域**（必需）- 当前存在 26 个 Azure 支持的区域，可在这些区域提供你的体验版。 为了获得最佳性能，建议选择一个预期客户人数最多的区域。 需确保允许订阅在所选的各个区域中部署所需的所有资源。

- **实例** - 选择类型（热或冷）和可用实例的数量，将其乘以提供产品/服务的区域数量。

  - **热** - 部署此类型的实例并等待每个所选区域的访问。 客户无需等待部署，就能立即访问体验版的热实例。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议拥有至少一个热实例，因为多数客户不想等待完整部署，因此，如果没有热实例可用，则会导致客户使用量下降 。

  - **冷** - 此类型的实例表示每个区域可能部署的实例总数。 冷实例要求在客户请求体验版时部署整个体验版资源管理器模板，因此，冷实例的加载速度比热实例要慢得多 。 弊端在于你只需为体验版的持续时间付费，它并不像热实例一样始终在 Azure 订阅上运行 。

- **体验版 Azure 资源管理器模板** - 上传包含 Azure 资源管理器模板的 .zip。 若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 Azure 资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

    > [!note]
    > 若要成功发布，必须验证 ARM 模板的格式。 实现此目的的两种方法是使用 (1) [联机 API 工具](/rest/api/resources/deployments/validate)或 (2) [测试部署](../azure-resource-manager/templates/deploy-portal.md)。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的小时数。 此时间段结束后，体验版会自动终止。 仅使用整数（例如，2 小时有效，1.5 小时无效）。

## <a name="write-the-test-drive-template"></a>写入体验版模板

在设计了所需的资源包后，请编写并构建体验版 ARM 模板。 由于体验版使用完全自动化模式运行部署，因此，体验版模板具有以下限制：

### <a name="parameters"></a>参数

大多数模板有一套参数，这些参数定义了资源名称、资源大小（例如存储帐户类型或虚拟机大小）、用户名及密码、DNS 名称等。 使用 Azure 门户部署解决方案时，可以手动填充所有的这些参数、选择可用的 DNS 名称或存储帐户名称等。

![Azure 资源管理器中的参数列表](media/test-drive/resource-manager-parameters.png)

但体验版使用完全自动化模式，无需人工干预，因此，它仅支持有限的一组参数类别。 若体验版 ARM 模板中的某个参数不属于受支持类别，则必须使用变量或常量值替换此参数。

用户可以将任意有效的名称用于参数，体验版使用元数据类型值识别参数类别。 必须为每个模板参数指定元数据类型，否则模板将无法通过验证：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> 所有参数都是可选的，因此，如果不想使用任何参数，就不必选择。

### <a name="accepted-parameter-metadata-types"></a>接受的参数元数据类型

| 元数据类型   | 参数类型  | 描述     | 示例值    |
|---|---|---|---|
| baseuri     | string          | 部署包的基 URI| `https://<..>.blob.core.windows.net/<..>` |
| **username**    | string          | 随机的新用户名。| admin68876      |
| **password**    | 安全字符串    | 随机的新密码 | Lp!ACS\^2kh     |
| 会话 ID   | string          | 唯一的体验版会话 ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

体验版使用部署包的“**基 Uri**”初始化此参数，因此，可以使用此参数构造包内的任意文件的 Uri。

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

在模板内部，可以使用此参数构造体验版部署包中的任意文件的 Uri。 以下示例演示了如何构造链接模板的 Uri：

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

体验版使用随机的新用户名初始化此参数：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

示例值：`admin68876`

可以将随机的或恒定的用户名用于解决方案。

#### <a name="password"></a>password

体验版使用随机的新密码初始化此参数：

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

示例值：`Lp!ACS^2kh`

可以将随机的或恒定的密码用于解决方案。

#### <a name="session-id"></a>会话 ID

体验版使用表示体验版会话 ID 的唯一 GUID 来初始化此参数：

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

示例值：`b8c8693e-5673-449c-badd-257a405a6dee`

如有必要，可以使用此参数对体验版会话进行唯一地标识。

### <a name="unique-names"></a>唯一的名称

一些 Azure 资源（如存储帐户或 DNS 名称）需要具有全局唯一的名称。 这意味着，每当体验版部署 ARM 模板时，它都会为其所有的资源创建一个具有唯一名称的新资源组。 因此，需要将 [uniquestring](../azure-resource-manager/templates/template-functions.md) 函数与资源组 ID 上的变量名称连接在一起使用，以生成随机的唯一值：

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

请务必将参数/变量字符串 (`contosovm`) 与唯一的字符串输出 (`resourceGroup().id`) 连接在一起，因为这样可确保每个变量的唯一性和可靠性。

例如，大多数的资源名称不能以数字开头，而唯一字符串函数可以返回一个以数字开头的字符串。 因此，若使用原始的唯一字符串输出，部署将失败。

有关详细信息，可查看[本文](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)中的资源命名规则和限制。

### <a name="deployment-location"></a>部署位置

用户可以在不同的 Azure 区域中部署体验版。

当体验版创建实验室实例时，通常会在所选区域中创建一个资源组，然后在此组上下文中执行部署模板。 因此，模板应从资源组选择部署位置：

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

然后将此位置用于特定实验室实例的各个资源：

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

需确保允许订阅在所选的各个区域中部署想要部署的所有资源。 同时，还需确保在将要启用的所有区域中有虚拟机映像可使用，否则，部署模板将不适用于某些区域。

### <a name="outputs"></a>输出

通常情况下，在使用资源管理器模板时，无需生成任何输出即可进行部署。 因为你知道用于填充模板参数的所有值，并且始终可以手动检查任意资源的属性。

但就体验版资源管理器模板而言，将所有信息（网站 URI、虚拟机主机名、用户名及密码）返回到体验版至关重要，因为需要使用这些信息来访问实验室。 请确保所有输出名称是可读的，因为这些变量要呈现给客户。

对于模板输出，没有任何限制。 体验版会将所有输出值转换为字符串，因此，如果将对象发送到输出，用户会看到 JSON 字符串。

示例：

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>订阅限制

记得订阅和服务限制。 如若要部署多达 10 个 4 核虚拟机，需确保用于实验室的订阅允许使用 40 核。 有关最新的 Azure 订阅和服务限制，请参阅“[Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)”。 由于可同时使用多个体验版，因此，请验证订阅是否能够处理可采用的并发体验版总数乘以核数后的总核数。

### <a name="what-to-upload"></a>要上传的内容

体验版 ARM 模板以 zip 文件格式上传，其中可包含各种部署项目，但需有一个名称为 `main-template.json` 的文件。 这是体验版用于实例化实验室的 ARM 部署模板。 如果有除此文件之外的其他资源，可将其引用为此模板内的外部资源，或者可以将该资源包含在 zip 文件中。

在发布认证期间，体验版会将部署包解压缩，并将其中的内容放入内部体验版 blob 容器。 此容器结构反映部署包的结构：

| package.zip                       | 体验版 blob 容器         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

我们将此 blob 容器的 URI 称为基 URI。 实验室的每个修订版有其自己的 blob 容器，因此，实验室的每个修订版有其自己的基 URI。 体验版可以通过模板参数将未解压的部署包的基 URI 传递到模板。

### <a name="transform-template-examples-for-test-drive"></a>体验版模板转换示例

将资源体系结构转换为体验版资源管理器模板的过程可能会让人望而却步。 有关更多帮助，请参阅“[什么是体验版？](what-is-test-drive.md#transforming-examples)”一节，了解如何最好地转换当前部署模板的示例。

## <a name="test-drive-deployment-subscription-details"></a>体验版部署订阅详细信息

要填写的最后一个部分是确保能够通过连接 Azure 订阅和 Azure Active Directory (AD) 来自动部署体验版。

![体验版部署订阅详细信息](media/test-drive/deployment-subscription-details.png)

1. 获取“Azure 订阅 ID”。 此项授予对 Azure 服务和 Azure 门户的访问权限。 将在该订阅中报告资源用量和计收服务费用。 如果没有一个专门用于体验版的独立 Azure 订阅，请创建一个订阅。 你可以通过登录到 Azure 门户，并从左侧导航菜单中选择“**订阅**”来查找 Azure 订阅 ID（如 `1a83645ac-1234-5ab6-6789-1h234g764ghty1`）。

   ![Azure 订阅](media/test-drive/azure-subscriptions.png)

2. 获取“**Azure AD 租户 ID**”。 如果你已经有可用的租户 ID，可以在“**Azure Active Directory**” >  **“属性”**  > “**目录 ID**”中找到它：

   ![Azure Active Directory 属性](media/test-drive/azure-active-directory-properties.png)

   如果没有租户 ID，请在 Azure Active Directory 中创建一个新的 ID。 有关设置租户的帮助，请参阅“[快速入门：设置租户](../active-directory/develop/quickstart-create-new-tenant.md)”。

3. 将 Microsoft 体验版应用程序预配到租户。 我们将使用此应用程序对体验版资源执行操作。
    1. 如果尚未安装，请安装 [Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。
    1. 添加 Microsoft 测试体验版应用程序的服务主体。
        1. 运行 `Connect-AzAccount` 并提供凭据以登录 Azure 帐户，这需要 Azure Active Directory 全局管理员[内置角色](/azure/active-directory/roles/permissions-reference#global-administrator)。 
        1. 创建新服务主体：`New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment`。
        1. 确保已创建服务主体：`Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'`。
      ![显示用于验证服务主体的代码](media/test-drive/commands-to-verify-service-principal.png)

4. 对于 Azure AD 应用 ID，请粘贴此应用程序 ID：`d7e39695-0b24-441c-a140-047800a05ede`。
5. 对于 Azure AD 应用密钥，由于不需要机密，因此请插入虚拟机密，例如“no-secret”。
6. 由于我们要使用该应用程序部署到订阅，因此，需要从 Azure 门户或 PowerShell 将应用程序添加为订阅中的参与者：

   1. 通过 Azure 门户：

       1. 选择要用于体验版的“订阅”。
       1. 选择“访问控制 (IAM)”。<br>

          ![添加一个新的访问控制 (IAM) 参与者](media/test-drive/access-control-principal.png)

       1. 选择“角色分配”选项卡，然后选择“+ 添加角色分配”。

          ![在“选择访问控制 (IAM)”窗口中，显示如何选择“角色分配”选项卡，然后选择“+ 添加角色分配”。](media/test-drive/access-control-principal-add-assignments.jpg)

       1. 输入此 Azure AD 应用程序名称：`Microsoft TestDrive`。 选择要将“**参与者**”角色分配给的应用程序。

          ![如何分配参与者角色](media/test-drive/access-control-permissions.jpg)

       1. 选择“保存”。
   1. 如果使用 PowerShell：
      1. 运行此代码可获取 ServicePrincipal object-id：`(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id`。
      1. 使用 ObjectId 和订阅 ID 运行此代码：`New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>`。

> [!NOTE]
> 在删除旧的 appID 之前，请转到 Azure 门户，然后转到“资源组”，并搜索 `CloudTry_`。 检查“事件发起者”列。
>
> :::image type="content" source="media/test-drive/event-initiated-by-field.png" lightbox="media/test-drive/event-initiated-by-field.png" alt-text="显示“事件发起者”字段":::
>
> 除非至少有一个资源（操作名称）设置为 Microsoft TestDrive，否则不要删除旧的 appID。
>
> 若要删除 appID，请在左侧导航菜单中选择“Azure Active Directory” >  **“应用注册”** ，然后选择“所有应用程序”选项卡。选择你的应用程序并选择“删除”。

## <a name="republish"></a>重新发布

现在所有的体验版字段都已完成，**请重新发布** 产品/服务。 在体验版通过认证后，应该对产品/服务预览版中的客户体验进行广泛的测试：

1. 在 UI 中启动体验版。
1. 在 Azure 门户中打开 Azure 订阅。
1. 验证体验版是否正在正确部署。

   ![Azure 门户](media/test-drive/azure-portal.png)

请勿删除为客户预配的任何体验版实例；当客户完成这些资源组后，该服务自动清理这些资源组。

如果对预览版产品/服务感到满意，现在就可以将它 **上线**！ 要仔细检查所有的端到端体验，请查看最终的审查过程。 如果我们拒绝该产品/服务，我们将向用户产品/服务工程联系人发送电子邮件，说明需要修复的内容。

## <a name="next-steps"></a>后续步骤

- 如果按照说明在合作伙伴中心创建产品/服务，请使用后退箭头返回到该主题。
- 有关其他类型的体验版的详细信息，请参阅“[什么是体验版？](what-is-test-drive.md)”。
