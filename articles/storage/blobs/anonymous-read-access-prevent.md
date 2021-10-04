---
title: 阻止对容器和 Blob 的匿名公共读取访问
titleSuffix: Azure Storage
description: 了解如何分析针对存储帐户的匿名请求，以及如何阻止对整个存储帐户或单个容器的匿名访问。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 884f70367c7e7edaf546b65b4e0e2825dacf0a07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577928"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>阻止对容器和 Blob 的匿名公共读取访问

对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但也可能会带来安全风险。 请务必谨慎管理匿名访问，并了解如何评估对数据的匿名访问。 操作复杂性、人为错误或对可公共访问的数据的恶意攻击可能会导致代价高昂的数据泄露。 Microsoft 建议仅在必要时为应用程序方案启用匿名访问。

默认情况下，始终会禁止对 blob 数据的公共访问。 但是，存储帐户的默认配置允许具有适当权限的用户配置对存储帐户中的容器和 blob 的公共访问。 为增强安全性，无论单个容器的公共访问设置如何，都可以禁止对存储帐户的所有公共访问。 禁止对存储帐户进行公共访问可以阻止用户启用帐户中的容器的公共访问权限。 若非有必要，Microsoft 建议禁止对存储帐户进行公共访问。 禁止公共访问有助于防止意外的匿名访问产生的数据泄露。

当你禁止对存储帐户的公共 blob 访问时，Azure 存储会拒绝对该帐户的所有匿名请求。 禁止对某个帐户的公共访问后，以后无法为该帐户中的容器配置公共访问。 已配置了公共访问的所有容器将不再接受匿名请求。 有关详细信息，请参阅[配置对容器和 Blob 的匿名公共读取访问](anonymous-read-access-configure.md)。

本文介绍了如何使用 DRAG（Detection-Remediation-Audit-Governance，检测-修正-审核-治理）框架持续管理对存储帐户的公共访问。

## <a name="detect-anonymous-requests-from-client-applications"></a>检测来自客户端应用程序的匿名请求

如果你禁止对存储帐户的公共读取访问，系统可能会拒绝对当前配置了公共访问的容器和 blob 的请求。 禁止对存储帐户的公共访问将替代该存储帐户中各个容器的公共访问设置。 禁止对存储帐户的公共访问后，将来对该帐户的任何匿名请求都会失败。

为了了解禁止公共访问可能会对客户端应用程序造成的影响，Microsoft 建议你为该帐户启用日志记录和指标，并分析一段时间间隔内的匿名请求的模式。 使用指标来确定对存储帐户的匿名请求数，并使用日志来确定匿名访问了哪些容器。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>使用指标资源管理器监视匿名请求

若要跟踪对存储帐户的匿名请求，请在 Azure 门户中使用 Azure 指标资源管理器。 若要详细了解 Azure 指标资源管理器，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/essentials/metrics-getting-started.md)。

按照以下步骤创建跟踪匿名请求的指标：

1. 导航到 Azure 门户中的存储帐户。 在“监视”部分下，选择“指标” 。
1. 选择“添加指标”。 在“指标”对话框中，指定以下值：
    1. 将“作用域”字段设置为存储帐户的名称。
    1. 将“指标命名空间”设置为“Blob”。 此指标仅报告对 Blob 存储的请求。
    1. 将“指标”字段设置为“事务”。
    1. 将“聚合”字段设置为“求和”。

    新指标会显示给定时间间隔内针对 Blob 存储的事务数之和。 生成的指标如下图所示：

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="屏幕截图显示了如何将指标配置为对 blob 事务数求和":::

1. 接下来，选择“添加筛选器”按钮，为匿名请求创建基于指标的筛选器。
1. 在“筛选器”对话框中，指定以下值：
    1. 将属性值设置为“身份验证”。
    1. 将“运算符”字段设置为等号 (=)。
    1. 通过从下拉列表中选择或输入将“值”字段设置为“匿名”。
1. 在右上角，选择要查看指标的时间间隔。 还可以通过指定从 1 分钟到 1 个月的时间间隔，来指示请求聚合粒度。

配置指标后，匿名请求将开始显示在图形上。 下图显示了在过去 30 分钟内聚合的匿名请求。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="屏幕截图显示了对 Blob 存储的聚合匿名请求":::

你还可以配置警报规则，让系统在针对你的存储帐户发出的匿名请求达到一定数量时通知你。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/alerts/alerts-metric.md)。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>分析日志以识别接收匿名请求的容器

Azure 存储日志会捕获有关针对存储帐户发出的请求的详细信息，包括请求的授权方式。 可以分析这些日志，以确定接收匿名请求的容器。

若要记录针对 Azure 存储帐户发出的请求以评估匿名请求，可以使用 Azure Monitor 中的 Azure 存储日志记录（预览版）。 有关详细信息，请参阅[监视 Azure 存储](./monitor-blob-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要详细了解日志查询，请参阅[教程：Log Analytics 查询入门](../../azure-monitor/logs/log-analytics-tutorial.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志记录预览版仅在 Azure 公有云中受支持。 政府云不支持 Azure Monitor 的 Azure 存储日志记录。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 门户中创建诊断设置

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须先创建一项诊断设置，用于指示要记录其数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. 在包含你的 Azure 存储帐户的订阅中创建一个新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 Log Analytics 工作区中提供。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在“监视”部分选择“诊断设置(预览版)”。
1. 选择“Blob”以记录针对 Blob 存储发出的请求。
1. 选择“添加诊断设置”。
1. 为该诊断设置提供一个名称。
1. 在“类别详细信息”下的“日志”部分，选择要记录的请求类型。 所有匿名请求都是读取请求，因此请选择“StorageRead”以捕获匿名请求。
1. 在“目标详细信息”下选择“发送到 Log Analytics” 。 选择你的订阅以及前面创建的 Log Analytics 工作区，如下图所示。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="展示如何创建用于记录请求的诊断设置的屏幕截图":::

创建诊断设置后，今后会根据该设置记录对存储帐户发出的请求。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

有关 Azure Monitor 的 Azure 存储日志中可用字段的参考，请参阅[资源日志（预览版）](./monitor-blob-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-anonymous-requests"></a>查询匿名请求的日志

Azure Monitor 中的 Azure 存储日志包括用于对存储帐户发出请求的授权类型。 在日志查询中，根据 AuthenticationType 属性筛选以查看匿名请求。

若要检索过去 7 天针对 Blob 存储发出的匿名请求的日志，请打开你的 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中并运行该查询：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

还可以基于此查询配置警报规则，以接收有关匿名请求的通知。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/alerts/alerts-log.md)。

## <a name="remediate-anonymous-public-access"></a>修正匿名公共访问

评估对存储帐户中的容器和 blob 的匿名请求后，可以采取措施来限制或阻止公共访问。 如果你的存储帐户中的某些容器可能需要供公共访问，你可以为存储帐户中的每个容器配置公共访问设置。 此选项提供对公共访问的最精细控制。 有关详细信息，请参阅[为容器设置公共访问级别](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

为增强安全性，可以禁止对整个存储帐户的公共访问。 存储帐户的公共访问设置会替代该帐户中容器的单个设置。 如果你禁止对存储帐户的公共访问，则任何配置为允许公共访问的容器将不再可供以匿名方式访问。 有关详细信息，请参阅[允许或禁止对存储帐户的公共读取访问](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)。

如果你的方案要求某些容器可供公共访问，则建议将这些容器及其 blob 移动到保留用于公共访问的存储帐户中。 然后，你可以禁止对任何其他存储帐户的公共访问。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>验证是否不允许对 blob 进行公共访问

若要验证是否禁止对特定 blob 进行公共访问，你可以尝试通过其 URL 下载 blob。 如果下载成功，则 blob 仍可供公共使用。 如果 blob 因为对存储帐户的公共访问被禁止而不可公共访问，则你将看到一条错误消息，指示此存储帐户不允许公共访问。

下面的示例演示了如何使用 PowerShell 来尝试通过 URL 下载 blob。 请记得将括号中的占位符值替换为你自己的值：

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>验证是否不允许修改容器的公共访问设置

若要验证在禁止对存储帐户的公共访问后是否无法修改容器的公共访问设置，可以尝试修改该设置。 如果禁止对存储帐户的公共访问，则更改容器的公共访问设置将失败。

下面的示例演示了如何使用 PowerShell 来尝试更改容器的公共访问设置。 请记得将括号中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>验证是否不允许创建启用了公共访问的容器

如果禁止对存储帐户的公共访问，则你将无法创建启用了公共访问的新容器。 若要进行验证，你可以尝试创建启用了公共访问的容器。

下面的示例演示了如何使用 PowerShell 来尝试创建启用了公共访问的容器。 请记得将括号中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>检查多个帐户的公共访问设置

若要检查具有最佳性能的一组存储帐户的公共访问设置，可以使用 Azure 门户中的 Azure Resource Graph 资源管理器。 若要详细了解如何使用 Resource Graph 资源管理器，请参阅[快速入门：使用 Azure Resource Graph 资源管理器运行你的第一个 Resource Graph 查询](../../governance/resource-graph/first-query-portal.md)。

默认情况下，不会为存储帐户设置 AllowBlobPublicAccess 属性，在你显式设置此属性之前，它不会返回值。 当此属性值为 null 或为 true 时，存储帐户允许公共访问。

在 Resource Graph 资源管理器中运行以下查询会返回存储帐户的列表，并显示每个帐户的公共访问设置：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

下图显示了整个订阅中的查询结果。 请注意，对于已显式设置了 AllowBlobPublicAccess 属性的存储帐户，它在结果中显示为 true 或 false。 如果尚未为存储帐户设置 AllowBlobPublicAccess 属性，则它在查询结果中显示为空白（或 null）。

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="屏幕截图显示了在各个存储帐户中针对公共访问设置的查询结果":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure Policy 审核合规性

如果你有大量存储帐户，则可能需要执行审核，以确保将这些帐户配置为阻止公共访问。 若要审核一组存储帐户的合规性，请使用 Azure Policy。 Azure Policy 是一项服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure Policy 可帮助你确保这些资源始终符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>创建具有 Audit 效果的策略

Azure Policy 支持的效果决定了针对资源评估某个策略规则时会发生什么情况。 当资源不合规时，Audit 效果会创建一个警告，但不会停止请求。 有关效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要通过 Azure 门户为存储帐户的公共访问设置创建具有“审核”效果的策略，请执行以下步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 在“创作”部分下，选择“定义”。
1. 选择“添加策略定义”以创建新的策略定义。
1. 对于“定义位置”字段，选择“更多”按钮以指定审核策略资源所在的位置。
1. 指定策略的名称。 还可以指定说明和类别。
1. 在“策略规则”下，将以下策略定义添加到“policyRule”部分。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 保存策略。

### <a name="assign-the-policy"></a>分配策略

接下来，将策略分配给资源。 策略的作用域对应于该资源及其下的所有资源。 有关策略分配的详细信息，请参阅 [Azure Policy 分配结构](../../governance/policy/concepts/assignment-structure.md)。

若要在 Azure 门户中分配策略，请执行下列步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 在“创作”部分下，选择“分配”。
1. 选择“分配策略”以创建新的策略分配。
1. 对于“作用域”字段，请选择策略分配的作用域。
1. 对于“策略定义”字段，请选择“更多”按钮，然后从列表中选择你在上一部分定义的策略。
1. 提供策略分配的名称。 说明是可选的。
1. 让“策略强制实施”设置为“启用”状态。 此设置对审核策略没有影响。
1. 选择“查看 + 创建”以创建分配。

### <a name="view-compliance-report"></a>查看合规性报告

分配策略后，你可以查看合规性报告。 审核策略的合规性报告会指出哪些存储帐户不符合策略。 有关详细信息，请参阅[获取策略合规性数据](../../governance/policy/how-to/get-compliance-data.md)。

创建策略分配后，合规性报告可能需要几分钟时间才会变得可用。

若要在 Azure 门户中查看合规性报告，请执行以下步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 选择“合规性”。
1. 筛选你在上一步创建的策略分配名称的结果。 该报告显示有多少资源不符合策略。
1. 你可以在报告中向下钻取以获取更多详细信息，包括不合规的存储帐户的列表。

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="屏幕截图显示了针对 blob 公共访问的审核策略的合规性报告":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>使用 Azure Policy 强制实施授权访问

Azure Policy 可以确保 Azure 资源符合要求和标准，从而为云治理提供支持。 若要确保组织中的存储帐户仅允许经授权的请求，你可以创建一个策略，用以阻止创建其公共访问设置允许匿名请求的新存储帐户。 如果该帐户的公共访问设置不符合策略，则此策略还会阻止对现有帐户进行的所有配置更改。

强制实施策略会使用“拒绝”效果来阻止将创建或修改一个允许公共访问的存储帐户的请求。 有关效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要为允许匿名请求的公共访问设置创建具有“拒绝”效果的策略，请执行[使用 Azure Policy 审核合规性](#use-azure-policy-to-audit-for-compliance)中所述的步骤，但在策略定义的“policyRule”节中提供以下 JSON：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

创建具有“拒绝”效果的策略并将其分配给作用域后，用户无法创建允许公共访问的存储帐户。 用户也不能对当前允许公共访问的现有存储帐户进行任何配置更改。 如果尝试这样做，将会导致错误。 必须将存储帐户的公共访问设置设为 **false**，然后才能继续创建或配置帐户。

下图显示了在以下情况下发生的错误：当具有“拒绝”效果的策略要求禁止公共访问时，你尝试创建允许公共访问（针对新帐户的默认设置）的存储帐户。

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="屏幕截图显示了在违反策略的情况下创建存储帐户时出现的错误":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>允许或禁止公共访问的权限

若要为存储帐户设置 AllowBlobPublicAccess 属性，用户必须有权创建和管理存储帐户。 提供这些权限的 Azure 基于角色的访问控制 (Azure RBAC) 角色包含 Microsoft.Storage/storageAccounts/write 或 Microsoft.Storage/storageAccounts/\* 操作 。 具有此操作的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

这些角色不提供通过 Azure Active Directory (Azure AD) 对存储帐户中数据的访问权限。 但是，它们包含 Microsoft.Storage/storageAccounts/listkeys/action，可以授予对帐户访问密钥的访问权限。 借助此权限，用户可以使用帐户访问密钥访问存储帐户中的所有数据。

角色分配的范围必须设定为存储帐户级别或更高级别，以允许用户启用或禁用存储帐户的公共访问。 有关角色作用域的详细信息，请参阅[了解 Azure RBAC 的作用域](../../role-based-access-control/scope-overview.md)。

请注意，仅向需要能够创建存储帐户或更新其属性的用户分配这些角色。 使用最小特权原则确保用户拥有完成任务所需的最少权限。 有关使用 Azure RBAC 管理访问权限的详细信息，请参阅 [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 所有者角色包括所有操作，因此具有这些管理角色之一的用户也可以创建和管理存储帐户。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

## <a name="next-steps"></a>后续步骤

- [配置对容器和 blob 的匿名公共读取访问](anonymous-read-access-configure.md)
- [通过 .NET 匿名访问公共容器和 Blob](anonymous-read-access-client.md)
