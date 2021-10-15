---
title: 阻止通过共享密钥进行授权
titleSuffix: Azure Storage
description: 若要要求客户端使用 Azure AD 对请求进行授权，你可禁止使用共享密钥 对存储帐户进行授权的请求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 566c538e0864f11dad0a642dd18f711c7cf00886
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532844"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account"></a>阻止对 Azure 存储帐户进行共享密钥授权

对 Azure 存储帐户的每个安全请求都必须经过授权。 默认情况下，可以使用 Azure Active Directory (Azure AD) 凭据对请求进行授权，或使用帐户访问密钥进行共享密钥授权。 在这两种类型的授权中，与共享密钥相比，Azure AD 提供更高级别的安全性和易用性，是 Microsoft 推荐的授权方法。 若要要求客户端使用 Azure AD 对请求进行授权，你可以禁止使用共享密钥对向存储帐户发出的请求进行授权。

当禁止对某个存储帐户进行共享密钥授权时，Azure 存储将拒绝向该帐户发出的所有使用帐户访问密钥进行授权的后续请求。 只有通过 Azure AD 进行授权的安全请求才会成功。 有关使用 Azure AD 的详细信息，请参阅[授权访问 Azure 存储中的数据](authorize-data-access.md)。

本文介绍如何检测通过共享密钥授权发送的请求，以及如何修正存储帐户的共享密钥授权。

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>检测客户端应用程序使用的授权类型

如果你不允许对存储帐户进行共享密钥授权，则从使用帐户访问密钥进行共享密钥授权的客户端发出的请求将失败。 若要了解在进行此更改之前禁用共享密钥授权可能会对客户端应用程序产生何种影响，请为存储帐户启用日志记录和指标。 然后，你可以分析一段时间内帐户的请求模式，以确定请求的授权方式。

使用指标来确定存储帐户收到的通过共享密钥或共享访问签名 (SAS) 授权的请求数。 使用日志来确定发送这些请求的客户端。

可以使用共享密钥或 Azure AD 向 SAS 授权。 有关解释使用共享访问签名发出的请求的详细信息，请参阅[了解禁止共享密钥对 SAS 令牌产生的影响](#understand-how-disallowing-shared-key-affects-sas-tokens)。

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>监视通过共享密钥授权的请求数

若要跟踪对存储帐户请求的授权方式，请在 Azure 门户中使用 Azure 指标资源管理器。 若要详细了解 Azure 指标资源管理器，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/essentials/metrics-getting-started.md)。

按照以下步骤创建一个指标，该指标用于跟踪使用共享密钥或 SAS 发出的请求：

1. 导航到 Azure 门户中的存储帐户。 在“监视”部分下，选择“指标” 。
1. 选择“添加指标”。 在“指标”对话框中，指定以下值：
    1. 将“范围”字段设置为存储帐户的名称。
    1. 将“指标命名空间”设置为“帐户”。 此指标将报告对存储帐户的所有请求。
    1. 将“指标”字段设置为“事务”。
    1. 将“聚合”字段设置为“求和”。

    新指标会显示给定时间间隔内针对存储帐户的事务数之和。 生成的指标如下图所示：

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="显示如何将指标配置为对使用共享密钥或 SAS 进行的事务数求和的屏幕截图":::

1. 接下来，选择“添加筛选器”按钮，为授权类型指标创建筛选器。
1. 在“筛选器”对话框中，指定以下值：
    1. 将属性值设置为“身份验证”。
    1. 将“运算符”字段设置为等号 (=)。
    1. 在“值”字段中，选择“帐户密钥”和“SAS” 。
1. 在右上角，选择要查看指标的时间范围。 还可以通过指定从 1 分钟到 1 个月的时间间隔，来指示请求聚合粒度。 例如，将“时间范围”设置为30天，并将“时间粒度”设置为 1 天，以查看过去 30 天内按天聚合的请求 。

配置指标后，对存储帐户的请求将开始显示在图表上。 下图显示了使用共享密钥授权或使用 SAS 令牌发出的请求。 在过去的 30 天内，请求按天聚合。

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="显示通过共享密钥授权的聚合请求的屏幕截图":::

你还可以配置警报规则，让系统在对针对存储帐户发出的匿名请求达到一定数量时通知你。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/alerts/alerts-metric.md)。

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>分析日志以识别使用共享密钥或 SAS 为请求授权的客户端

Azure 存储日志会捕获有关针对存储帐户发出的请求的详细信息，包括请求的授权方式。 可以分析日志以确定哪些客户端正在使用共享密钥或 SAS 令牌为请求授权。

若要记录针对 Azure 存储帐户发出的请求以评估这些请求的授权方式，可以使用 Azure Monitor 中的 Azure 存储日志记录（预览版）。 有关详细信息，请参阅[监视 Azure 存储](../blobs/monitor-blob-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要详细了解日志查询，请参阅[教程：Log Analytics 查询入门](../../azure-monitor/logs/log-analytics-tutorial.md)。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 门户中创建诊断设置

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须先创建一项诊断设置，用于指示要记录其数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. 在包含你的 Azure 存储帐户的订阅中创建新的 Log Analytics 工作区，或使用现有的 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 Log Analytics 工作区中提供。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在“监视”部分选择“诊断设置(预览版)”。
1. 选择要记录其请求的 Azure 存储服务。 例如，选择“Blob”会记录对 Blob 存储发出的请求。
1. 选择“添加诊断设置”。
1. 为该诊断设置提供一个名称。
1. 在“类别详细信息”下的“日志”部分，选择“StorageRead”、“StorageWrite”和“StorageDelete”，以记录对所选服务发出的所有数据请求。    
1. 在“目标详细信息”下选择“发送到 Log Analytics”。  选择你的订阅以及前面创建的 Log Analytics 工作区，如下图所示。

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="展示如何创建用于记录请求的诊断设置的屏幕截图":::

可为存储帐户中每种类型的 Azure 存储资源创建一项诊断设置。

创建诊断设置后，今后会根据该设置记录对存储帐户发出的请求。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

有关 Azure Monitor 的 Azure 存储日志中可用字段的参考，请参阅[资源日志（预览版）](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>查询使用共享密钥或 SAS 发出的请求的日志

Azure Monitor 中的 Azure 存储日志包括用于对存储帐户发出请求的授权类型。 若要检索过去 7 天发出的、使用共享密钥或 SAS 授权的请求的日志，请打开你的 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中并运行该查询。 此查询将显示最频繁地发送使用共享密钥或 SAS 授权的请求的 10 个 IP 地址：

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

还可以基于此查询配置警报规则，以接收有关已使用共享密钥或 SAS 为请求授权的通知。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/alerts/alerts-log.md)。

## <a name="remediate-authorization-via-shared-key"></a>修正通过共享密钥进行的授权

在分析对存储帐户发出的请求的授权方式后，可以采取措施来阻止通过共享密钥进行访问。 但首先，你需要将使用共享密钥授权的所有应用程序更新为改用 Azure AD。 可以按照[检测客户端应用程序使用的授权类型](#detect-the-type-of-authorization-used-by-client-applications)所述的方法监视日志和指标，以便对转换进行跟踪。 若要详细了解如何使用 Azure AD 访问存储帐户中的数据，请参阅[授权访问 Azure 存储中的数据](authorize-data-access.md)。

确信可以安全地拒绝通过共享密钥授权的请求时，可以将存储帐户的“AllowSharedKeyAccess”属性设置为“false” 。

默认情况下，不会设置“AllowSharedKeyAccess”属性，并且在显式设置此属性之前，它不会返回值。 当属性值为“null”或“true”时，存储帐户允许通过共享密钥授权的请求 。

> [!WARNING]
> 如果任何客户端当前正在使用共享密钥访问存储帐户中的数据，则 Microsoft 建议将这些客户端迁移到 Azure AD，然后再禁止通过共享密钥对存储帐户进行访问。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中禁止对存储帐户的共享密钥授权，请按照以下步骤操作：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下找到“配置”设置。 
1. 将“允许存储帐户密钥访问”设置为“禁用”。 

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="显示如何禁止对帐户的共享密钥访问的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 来禁止对存储帐户进行共享密钥授权，请安装 [Az.Storage PowerShell 模块](https://www.powershellgallery.com/packages/Az.Storage) 3.4.0 或更高版本。 接下来，为新的或现有的存储帐户配置 **AllowSharedKeyAccess** 属性。

以下示例演示如何使用 PowerShell 来禁止通过共享密钥对现有存储帐户进行访问。 请记得将括号中的占位符值替换为你自己的值：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 来禁止对存储帐户进行共享密钥授权，请安装 Azure CLI 2.20.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 接下来，为新的或现有的存储帐户配置“allowSharedKeyAccess”属性。

以下示例演示如何使用 Azure CLI 来禁止通过共享密钥对现有存储帐户进行访问。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

禁用共享密钥授权后，使用共享密钥授权对存储帐户发出的请求将失败，错误代码为 403（禁止访问）。 Azure 存储会返回错误，指示不允许对存储帐户进行基于密钥的授权。

仅使用 Azure 资源管理器部署模型的存储帐户支持 AllowSharedKeyAccess 属性。 有关哪些存储帐户使用 Azure 资源管理器部署模型的信息，请参阅[存储帐户的类型](storage-account-overview.md#types-of-storage-accounts)。

### <a name="verify-that-shared-key-access-is-not-allowed"></a>验证是否不允许使用共享密钥访问

若要验证是否不再允许使用共享密钥授权，可以尝试使用帐户访问密钥调用数据操作。 以下示例尝试使用访问密钥创建容器。 在不允许对存储帐户进行共享密钥授权的情况下，此调用将失败。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> 匿名请求是未经授权的，如果已将存储帐户和容器配置为匿名公共读取访问，则该请求将继续。 有关详细信息，请参阅[配置对容器和 Blob 的匿名公共读取访问](../blobs/anonymous-read-access-configure.md)。

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>检查多个帐户的共享密钥访问设置

若要检查具有最佳性能的一组存储帐户的共享密钥访问设置，可以使用 Azure 门户中的 Azure Resource Graph 资源管理器。 若要详细了解如何使用 Resource Graph 资源管理器，请参阅[快速入门：使用 Azure Resource Graph 资源管理器运行你的第一个 Resource Graph 查询](../../governance/resource-graph/first-query-portal.md)。

在 Resource Graph 资源管理器中运行以下查询会返回存储帐户的列表，并显示每个帐户的共享密钥访问设置：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>允许或禁止共享密钥访问的权限

若要为存储帐户设置“AllowSharedKeyAccess”属性，用户必须具有创建和管理存储帐户的权限。 提供这些权限的 Azure 基于角色的访问控制 (Azure RBAC) 角色包含 Microsoft.Storage/storageAccounts/write 或 Microsoft.Storage/storageAccounts/\* 操作 。 具有此操作的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

这些角色不提供通过 Azure Active Directory (Azure AD) 对存储帐户中数据的访问权限。 但是，它们包含 Microsoft.Storage/storageAccounts/listkeys/action，可以授予对帐户访问密钥的访问权限。 借助此权限，用户可以使用帐户访问密钥访问存储帐户中的所有数据。

角色分配的范围必须设定为存储帐户级别或更高级别，以允许用户启用或禁用对存储帐户的共享密钥访问。 有关角色范围的详细信息，请参阅[了解 Azure RBAC 的范围](../../role-based-access-control/scope-overview.md)。

请注意，仅向需要能够创建存储帐户或更新其属性的用户分配这些角色。 使用最小特权原则确保用户拥有完成任务所需的最少权限。 有关使用 Azure RBAC 管理访问权限的详细信息，请参阅 [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 所有者角色包括所有操作，因此具有这些管理角色之一的用户也可以创建和管理存储帐户。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>了解禁用共享密钥如何影响 SAS 令牌

当存储帐户不允许使用共享密钥访问时，Azure 存储会根据 SAS 的类型和请求的目标服务来处理 SAS 令牌。 下表显示了每种类型的 SAS 的授权方式，以及当存储帐户的“AllowSharedKeyAccess”属性为“false”时，Azure 存储将如何处理该 SAS 。

| SAS 类型 | 授权类型 | AllowSharedKeyAccess 为 false 时的行为 |
|-|-|-|
| 用户委托 SAS（仅限 Blob 存储） | Azure AD | 允许请求。 Microsoft 建议尽可能使用用户委托 SAS 以提高安全性。 |
| 服务 SAS | 共享密钥 | 拒绝对所有 Azure 存储服务的请求。 |
| 帐户 SAS | 共享密钥 | 拒绝对所有 Azure 存储服务的请求。 |

Azure 指标和 Azure Monitor 中的日志记录不区分不同类型的共享访问签名。 Azure 指标资源管理器中的 SAS 筛选器和 Azure Monitor 中 Azure 存储日志记录内的 SAS 字段都会报告通过任何类型的 SAS 授权的请求 。 但是，不同类型的共享访问签名以不同的方式获得授权，并且当不允许使用共享密钥访问时，行为会有所不同：

- 服务 SAS 令牌或帐户 SAS 令牌使用共享密钥授权，当 AllowSharedKeyAccess 属性设置为 false时，不允许在对 Blob 存储的请求中使用 。
- 用户委派 SAS 使用 Azure AD 授权，当 AllowSharedKeyAccess 属性设置为 false时，不允许在对 Blob 存储的请求中使用 。

评估通向存储帐户的流量时，请记住，[检测客户端应用程序使用的授权类型](#detect-the-type-of-authorization-used-by-client-applications)中所述的指标和日志可能包括使用用户委托 SAS 发出的请求。

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)。

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>考虑与其他 Azure 工具和服务的兼容性

许多 Azure 服务使用共享密钥授权来与 Azure 存储进行通信。 如果不允许对存储帐户进行共享密钥授权，这些服务将无法访问该帐户中的数据，并且应用程序可能会受到负面影响。

某些 Azure 工具提供了使用 Azure AD 授权来访问 Azure 存储的选项。 下表列出了一些常用的 Azure 工具，并说明了它们是否可以使用 Azure AD 来授权对 Azure 存储的请求。

| Azure 工具 | 对 Azure 存储的 Azure AD 授权 |
|-|-|
| Azure 门户 | 支持。 有关使用 Azure AD 帐户从 Azure 门户进行授权的信息，请参阅[选择如何授予对 Azure 门户中 blob 数据的访问权限](../blobs/authorize-data-operations-portal.md)。 |
| AzCopy | 支持用于 Blob 存储。 有关授权 AzCopy 操作的信息，请参阅 AzCopy 文档中的[选择如何提供授权凭据](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials)。 |
| Azure 存储资源管理器 | 仅支持用于 Blob 存储和 Azure Data Lake Storage Gen2。 不支持对队列存储的 Azure AD 访问。 请确保选择正确的 Azure AD 租户。 有关详细信息，请参阅[存储资源管理器入门](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | 支持。 有关如何使用 Azure AD 为 blob 或队列操作授权 PowerShell 命令的信息，请参阅[使用 Azure AD 凭据运行 PowerShell 命令以访问 blob 数据](../blobs/authorize-data-operations-powershell.md)或[使用 Azure AD 凭据运行 PowerShell 命令以访问队列数据](../queues/authorize-data-operations-powershell.md)。 |
| Azure CLI | 支持。 有关如何使用 Azure AD 授权 Azure CLI 命令来访问 blob 和队列数据的信息，请参阅[使用 Azure AD 凭据运行 Azure CLI 命令以访问 blob 或队列数据](../blobs/authorize-data-operations-cli.md)。 |
| Azure IoT 中心 | 支持。 有关详细信息，请参阅 [IoT 中心对虚拟网络的支持](../../iot-hub/virtual-network-support.md)。 |
| Azure Cloud Shell | Azure Cloud Shell 是 Azure 门户中的集成式 shell。 Azure Cloud Shell 在存储帐户中的 Azure 文件共享内持久存放文件。 如果禁止对该存储帐户使用共享密钥授权，则这些文件将不可访问。 有关详细信息，请参阅[连接 Microsoft Azure 文件存储](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage)。 <br /><br /> 若要在 Azure Cloud Shell 中运行命令以管理禁止进行共享密钥访问的存储帐户，请先确保已通过 Azure RBAC 为你授予了对这些帐户的必要权限。 有关详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。 |

## <a name="transition-azure-files-and-table-storage-workloads"></a>转移 Azure 文件和表存储工作负载

Azure 存储仅支持针对 Blob 和队列存储请求的 Azure AD 授权。 如果你不允许对存储帐户使用共享密钥授权，则使用共享密钥授权的 Azure 文件存储或表存储的请求将失败。 由于 Azure 门户始终使用共享密钥授权来访问文件和表数据，因此，如果你不允许对存储帐户使用共享密钥进行授权，将无法访问 Azure 门户中的文件或表数据。

Microsoft 建议在禁止通过共享密钥访问帐户之前，将任何 Azure 文件存储或表存储数据迁移到单独的存储帐户，或者不将此设置应用于支持 Azure 文件存储或表存储工作负载的存储帐户。

禁止对存储帐户进行共享密钥访问不会影响与 Azure 文件存储的 SMB 连接。

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储中的数据](./authorize-data-access.md)
- [使用 Azure Active Directory 授予对 Blob 和队列的访问权限](authorize-data-access.md)
- [通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)