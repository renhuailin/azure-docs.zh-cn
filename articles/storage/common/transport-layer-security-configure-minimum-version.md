---
title: 针对传入请求强制实施必需的最低版本的传输层安全性 (TLS)
titleSuffix: Azure Storage
description: 将存储帐户配置为要求向 Azure 存储发出请求的客户端至少使用最低版本的传输层安全性 (TLS)。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/07/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c69e8a5030717dd76a887968f40034595b9cd939
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739024"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>针对发送到存储帐户的请求强制实施必需的最低版本的传输层安全性 (TLS)

客户端应用程序与 Azure 存储帐户之间的通信使用传输层安全性 (TLS) 进行加密。 TLS 是一种标准的加密协议，可确保通过 Internet 进行通信的客户端与服务之间的隐私和数据完整性。 有关 TLS 的详细信息，请参阅[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)。

Azure 存储当前支持 TLS 协议的三个版本：1.0、1.1 和 1.2。 Azure 存储在公共 HTTPS 终结点上使用 TLS 1.2，但仍支持 TLS 1.0 和 TLS 1.1 以实现后向兼容性。

Azure 存储帐户允许客户端使用 TLS 的最早版本、TLS 1.0 及更高版本发送和接收数据。 若要强制实施更严格的安全措施，可以将存储帐户配置为要求客户端使用较新版本的 TLS 发送和接收数据。 如果存储帐户对 TLS 存在最低版本要求，则使用较旧版本发出的任何请求都会失败。

本文介绍了如何使用 DRAG（Detection-Remediation-Audit-Governance，检测-修正-审核-治理）框架为你的存储帐户持续管理安全的 TLS。

若要了解如何在从客户端应用程序发送请求时指定 TLS 的特定版本，请参阅[为客户端应用程序配置传输层安全性 (TLS)](transport-layer-security-configure-client-version.md)。

## <a name="detect-the-tls-version-used-by-client-applications"></a>检测客户端应用程序使用的 TLS 版本

在对存储帐户强制实施最低 TLS 版本时，可能会拒绝使用较旧版本的 TLS 发送数据的客户端发出的请求。 为了了解配置最低 TLS 版本可能会对客户端应用程序产生的影响，Microsoft 建议你对 Azure 存储帐户启用日志记录，并在间隔一段时间后分析日志，以检测 TLS 客户端应用程序使用的是哪个版本。

为了记录发到 Azure 存储帐户的请求并确定客户端使用的 TLS 版本，可以使用 Azure Monitor（预览版）中的 Azure 存储日志记录。 有关详细信息，请参阅[监视 Azure 存储](../blobs/monitor-blob-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要详细了解日志查询，请参阅[教程：Log Analytics 查询入门](../../azure-monitor/logs/log-analytics-tutorial.md)。

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须先创建一项诊断设置，用于指示要记录其数据的请求类型和存储服务。 Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 此预览版支持记录 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列和表格。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. 在包含你的 Azure 存储帐户的订阅中创建一个新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 Log Analytics 工作区中提供。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在“监视”部分选择“诊断设置(预览版)”。
1. 选择要记录其请求的 Azure 存储服务。 例如，选择“Blob”会记录对 Blob 存储发出的请求。
1. 选择“添加诊断设置”。
1. 为该诊断设置提供一个名称。
1. 在“类别详细信息”下的“日志”部分，选择要记录的请求类型。 可以记录读取、写入和删除请求。 例如，选择“StorageRead”和“StorageWrite”会记录对所选服务发送的读取和写入请求。
1. 在“目标详细信息”下选择“发送到 Log Analytics”。  选择你的订阅以及前面创建的 Log Analytics 工作区，如下图所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="展示如何创建用于记录请求的诊断设置的屏幕截图":::

创建诊断设置后，今后会根据该设置记录对存储帐户发出的请求。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

有关 Azure Monitor 的 Azure 存储日志中可用字段的参考，请参阅[资源日志（预览版）](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)。

### <a name="query-logged-requests-by-tls-version"></a>按 TLS 版本查询记录的请求

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发送请求的 TLS 版本。 使用 TlsVersion 属性可检查已记录请求的 TLS 版本。

要确定过去七天内使用不同版本的 TLS 对 Blob 存储发出了多少请求，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中并运行该查询。 请记得将括号中的占位符值替换为你自己的值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

结果将显示使用各个 TLS 版本发出的请求数计数：

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="显示返回 TLS 版本的 log analytics 查询结果的屏幕截图":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>按调用方 IP 地址和用户代理标头查询记录的请求

Azure Monitor 中的 Azure 存储日志还包括调用方 IP 地址和用户代理标头，以帮助你评估哪些客户端应用程序访问了存储帐户。 通过分析这些值，可以确定是必须更新客户端应用程序以使用较新的 TLS 版本，还是可以接受客户端未使用最低 TLS 版本发送请求时导致的失败。

要确定过去七天内哪些客户端使用低于 TLS 1.2 的 TLS 版本发出了请求，请将以下查询粘贴到新的日志查询中并运行该查询。 请记得将括号中的占位符值替换为你自己的值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>使用最低版本的 TLS 修正安全风险

如果你确信来自使用较旧版 TLS 的客户端的流量很小，或者允许使用较旧版 TLS 发出的请求失败，则可开始在存储帐户上实施最低版本的 TLS。 要求客户端使用最低版本的 TLS 对存储帐户发出请求是将数据的安全风险降至最低的策略的一部分。

> [!IMPORTANT]
> 如果使用连接到 Azure 存储的服务，请确保在你设置存储帐户所需的最低版本前，该服务使用相应的 TLS 版本向 Azure 存储发送请求。

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>为存储帐户配置最低 TLS 版本

若要为存储帐户配置最低 TLS 版本，请为该帐户设置 **MinimumTlsVersion** 版本。 此属性适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 有关 Azure 资源管理器部署模型的详细信息，请参阅[存储帐户概述](storage-account-overview.md)。

MinimumTlsVersion 属性的默认值因其设置方式而异。 通过 Azure 门户创建存储帐户时，默认情况下，最低 TLS 版本设置为 1.2。 使用 PowerShell、Azure CLI 或 Azure 资源管理器模板创建存储帐户时，默认情况下不会设置 MinimumTlsVersion 属性。在你显式设置此属性之前，它不会返回值。

如果未设置 MinimumTlsVersion 属性，则其值可能会显示为 null 或空字符串，具体取决于上下文 。 如果未设置属性值，则存储帐户会允许以 TLS 1.0 或更高版本发送的请求。

# <a name="portal"></a>[Portal](#tab/portal)

通过 Azure 门户创建存储帐户时，默认情况下，最低 TLS 版本设置为 1.2。

若要通过 Azure 门户为现有存储帐户配置最低 TLS 版本，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下，选择“配置”。
1. 在“最低 TLS 版本”下，使用下拉列表选择访问此存储帐户中的数据必需的最低 TLS 版本。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="屏幕截图显示了如何在 Azure 门户中配置最低版本的 TLS。" lightbox="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 为存储帐户配置最低的 TLS 版本，请安装 [Azure PowerShell 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 或更高版本。 接下来，为新的或现有的存储帐户配置 **MinimumTLSVersion** 属性。 **MinimumTlsVersion** 的有效值为 `TLS1_0`、`TLS1_1` 和 `TLS1_2`。

以下示例创建一个存储帐户并将 **MinimumTLSVersion** 设置为 TLS 1.1，然后更新该帐户并将 **MinimumTLSVersion** 设置为 TLS 1.2。 该示例还检索每个事例中的属性值。 请记得将括号中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 为存储帐户配置最低的 TLS 版本，请安装 Azure CLI 2.9.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 接下来，为新的或现有的存储帐户配置 **minimumTlsVersion** 属性。 **minimumTlsVersion** 的有效值为 `TLS1_0`、`TLS1_1` 和 `TLS1_2`。

以下示例创建一个存储帐户并将 **minimumTLSVersion** 设置为 TLS 1.1。 然后，它更新帐户并将 **minimumTLSVersion** 属性设置为 TLS 1.2。 该示例还检索每个事例中的属性值。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[模板](#tab/template)

若要通过模板为存储帐户配置最低 TLS 版本，请创建一个将 **MinimumTLSVersion** 属性设置为 `TLS1_0`、`TLS1_1` 或 `TLS1_2` 的模板。 以下步骤说明了如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择“创建资源”。
1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
1. 依次选择“模板部署(使用自定义模板进行部署)(预览版)”、“创建”、“在编辑器中生成自己的模板”。
1. 在模板编辑器中，粘贴以下 JSON 以创建新帐户，并将最低 TLS 版本设置为 TLS 1.2。 请记得将尖括号中的占位符替换为你自己的值。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 保存模板。
1. 指定资源组参数，然后选择“查看 + 创建”按钮以部署模板，并创建配置了 **MinimumTLSVersion** 属性的存储帐户。

---

> [!NOTE]
> 更新存储帐户的最低 TLS 版本后，最多可能需要 30 秒才能完全传播更改。

配置最低 TLS 版本需要 Azure 存储资源提供程序的 2019-04-01 版或更高版本。 有关详细信息，请参阅 [Azure 存储资源提供程序 REST API](/rest/api/storagerp/)。

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>检查多个帐户的必需最低 TLS 版本

若要在具有最佳性能的一组存储帐户中检查必需的最低 TLS 版本，可以使用 Azure 门户中的 Azure Resource Graph 资源管理器。 若要详细了解如何使用 Resource Graph 资源管理器，请参阅[快速入门：使用 Azure Resource Graph 资源管理器运行你的第一个 Resource Graph 查询](../../governance/resource-graph/first-query-portal.md)。

在 Resource Graph 资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的最低 TLS 版本：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>测试客户端的最低 TLS 版本

若要测试存储帐户必需的最低 TLS 版本是否禁止使用较旧版本发出的调用，可以将客户端配置为使用较旧版本的 TLS。 若要详细了解如何将客户端配置为使用特定的 TLS 版本，请参阅[为客户端应用程序配置传输层安全性 (TLS)](transport-layer-security-configure-client-version.md)。

当客户端使用不满足为帐户配置的最低 TLS 版本的 TLS 版本访问存储帐户时，Azure 存储会返回错误代码 400 错误（错误请求），并返回一条消息，指示不允许所使用的 TLS 版本对此存储帐户发出请求。

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure Policy 审核合规性

如果你有大量存储帐户，则可能需要执行审核，以确保为所有帐户配置组织所需的最低 TLS 版本。 若要审核一组存储帐户的合规性，请使用 Azure Policy。 Azure Policy 是一项服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure Policy 可帮助你确保这些资源始终符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>创建具有 Audit 效果的策略

Azure Policy 支持的效果决定了针对资源评估某个策略规则时会发生什么情况。 当资源不合规时，Audit 效果会创建一个警告，但不会停止请求。 有关效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要通过 Azure 门户针对最低 TLS 版本创建具有 Audit 效果的策略，请执行以下步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 在“创作”部分下，选择“定义”。
1. 选择“添加策略定义”以创建新的策略定义。
1. 对于“定义位置”字段，选择“更多”按钮以指定审核策略资源所在的位置。
1. 指定策略的名称。 还可以指定说明和类别。
1. 在“策略规则”下，将以下策略定义添加到“policyRule”部分。

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
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

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="屏幕截图显示了针对最低 TLS 版本的审核策略的合规性报告":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>使用 Azure Policy 强制实施最低 TLS 版本

Azure Policy 可以确保 Azure 资源符合要求和标准，从而为云治理提供支持。 若要为你的组织中的存储帐户强制实施最低 TLS 版本要求，你可以创建一个策略，用于阻止创建具有以下特点的新存储帐户：该帐户将最低 TLS 要求设置为比该策略规定的版本更旧的 TLS。 如果该帐户的最低 TLS 版本设置不符合策略，则此策略还会阻止对现有帐户进行所有配置更改。

强制实施策略会使用 Deny 效果来阻止具有如下特点的请求：将创建或修改存储帐户，导致最低 TLS 版本不再符合组织标准。 有关效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要针对低于 TLS 1.2 的最低 TLS 版本创建具有 Deny 效果的策略，请执行[使用 Azure Policy 审核合规性](#use-azure-policy-to-audit-for-compliance)中所述的步骤，但在策略定义的“policyRule”部分提供以下 JSON：

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

创建具有 Deny 效果的策略并将其分配给作用域后，用户无法创建最低 TLS 版本低于 1.2 的存储帐户。 用户也不能对当前需要的最低 TLS 版本低于 1.2 的现有存储帐户进行任何配置更改。 如果尝试这样做，将会导致错误。 必须将存储帐户的必需最低 TLS 版本设置为 1.2，然后才能继续创建或配置帐户。

下图显示了在以下情况下发生的错误：当具有 Deny 效果的策略要求将最低 TLS 版本设置为 TLS 1.2 时，你尝试创建最低 TLS 版本设置为 TLS 1.0（针对新帐户的默认值）的存储帐户。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="屏幕截图显示了在违反策略的情况下创建存储帐户时出现的错误":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>需要最低版本的 TLS 所需的权限

若要为存储帐户设置 MinimumTlsVersion 属性，用户必须具有创建和管理存储帐户的权限。 提供这些权限的 Azure 基于角色的访问控制 (Azure RBAC) 角色包含 Microsoft.Storage/storageAccounts/write 或 Microsoft.Storage/storageAccounts/\* 操作 。 具有此操作的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

这些角色不提供通过 Azure Active Directory (Azure AD) 对存储帐户中数据的访问权限。 但是，它们包含 Microsoft.Storage/storageAccounts/listkeys/action，可以授予对帐户访问密钥的访问权限。 借助此权限，用户可以使用帐户访问密钥访问存储帐户中的所有数据。

角色分配的范围必须设定为存储帐户级别或更高级别，以允许用户为存储帐户要求最低版本的 TLS。 有关角色范围的详细信息，请参阅[了解 Azure RBAC 的范围](../../role-based-access-control/scope-overview.md)。

请注意，仅向需要能够创建存储帐户或更新其属性的用户分配这些角色。 使用最小特权原则确保用户拥有完成任务所需的最少权限。 有关使用 Azure RBAC 管理访问权限的详细信息，请参阅 [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 所有者角色包括所有操作，因此具有这些管理角色之一的用户也可以创建和管理存储帐户。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

## <a name="network-considerations"></a>网络注意事项

当客户端向存储帐户发送请求时，客户端在处理任何请求之前，首先会与存储帐户的公共终结点建立连接。 建立连接后，将检查最低 TLS 版本设置。 如果请求使用的 TLS 版本比设置指定的版本低，则连接仍会成功，但是请求最终会失败。 有关 Azure 存储的公共终结点的详细信息，请参阅[资源 URI 语法](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)。

## <a name="next-steps"></a>后续步骤

- [为客户端应用程序配置传输层安全性 (TLS)](transport-layer-security-configure-client-version.md)
- [适用于 Blob 存储的安全建议](../blobs/security-recommendations.md)
