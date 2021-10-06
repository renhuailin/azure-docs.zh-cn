---
title: 理解查询语言
description: 介绍 Resource Graph 表以及可用于 Azure Resource Graph 的 Kusto 数据类型、运算符和函数。
ms.date: 09/03/2021
ms.topic: conceptual
ms.openlocfilehash: a66bc9167ff3417b6f88442403232490b20733ff
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425689"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查询语言

Azure Resource Graph 查询语言支持多个运算符和函数。 每个运算符和函数的工作原理和操作方式基于 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/index)。 若要了解 Resource Graph 使用的查询语言，请从 [KQL 教程](/azure/data-explorer/kusto/query/tutorial)开始。

本文介绍 Resource Graph 支持的语言组件：

- [Resource Graph 表](#resource-graph-tables)
- [Resource Graph 自定义语言元素](#resource-graph-custom-language-elements)
- [支持的 KQL 语言元素](#supported-kql-language-elements)
- [查询的作用域](#query-scope)
- [转义字符](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 表

Resource Graph 为其存储的有关 Azure 资源管理器资源类型及其属性的数据提供多个表。 一些表可以与 `join` 或 `union` 运算符配合使用，以便从相关资源类型获取属性。 下面是 Resource Graph 中可用表的列表：

|Resource Graph 表 |与其他表之间可否执行 `join`？ |说明 |
|---|---|---|
|资源 |是 |如果未在查询中定义，则为默认表。 此处显示了大多数资源管理器资源类型和属性。 |
|ResourceContainers |是 |包含管理组 (`Microsoft.Management/managementGroups`)、订阅 (`Microsoft.Resources/subscriptions`) 和资源组 (`Microsoft.Resources/subscriptions/resourcegroups`) 资源类型和数据。 |
|AdvisorResources |是（预览版） |包括与 `Microsoft.Advisor` 相关的资源。 |
|AlertsManagementResources |是（预览版） |包括与 `Microsoft.AlertsManagement` 相关的资源。 |
|DesktopVirtualizationResources |是 |包括与 `Microsoft.DesktopVirtualization` 相关的资源。 |
|ExtendedLocationResources |否 |包括与 `Microsoft.ExtendedLocation` 相关的资源。 |
|GuestConfigurationResources |否 |包括与 `Microsoft.GuestConfiguration` 相关的资源。 |
|HealthResources|是（预览版） |包括与 `Microsoft.ResourceHealth/availabilitystatuses` 相关的资源。 |
|IoTSecurityResources |否 |包括与 `Microsoft.IoTSecurity` 相关的资源。 |
|KubernetesConfigurationResources |否 |包括与 `Microsoft.KubernetesConfiguration` 相关的资源。 |
|MaintenanceResources |部分可以，仅限联接到。 （预览版） |包括与 `Microsoft.Maintenance` 相关的资源。 |
|PatchAssessmentResources|否 |包括与 Azure 虚拟机补丁评估相关的资源。 |
|PatchInstallationResources|否 |包括与 Azure 虚拟机补丁安装相关的资源。 |
|PolicyResources |是 |包括与 `Microsoft.PolicyInsights` 相关的资源。 |
|RecoveryServicesResources |部分可以，仅限联接到。 （预览版） |包括与 `Microsoft.DataProtection` 和 `Microsoft.RecoveryServices` 相关的资源。 |
|SecurityResources |是（预览版） |包括与 `Microsoft.Security` 相关的资源。 |
|ServiceHealthResources |否（预览版） |包括与 `Microsoft.ResourceHealth/events` 相关的资源。 |
|WorkloadMonitorResources |否 |包括与 `Microsoft.WorkloadMonitor` 相关的资源。 |

有关包含资源类型的完整列表，请参阅[参考：支持的表和资源类型](../reference/supported-tables-resources.md)。

> [!NOTE]
>  Resources 是默认表。 查询 Resources 表时，无需提供表名称，除非使用 `join` 或 `union`。 但是，建议的做法是始终在查询中包含初始表。

使用门户中的 Resource Graph 资源管理器来发现每个表中有哪些可用的资源类型。 或者，使用查询（如 `<tableName> | distinct type`）来获取环境中存在的给定 Resource Graph 表所支持的资源类型的列表。

以下查询显示了简单的 `join` 用法。 查询结果将列混合在一起，并且联接表中的所有重复列名称（在此示例中为 ResourceContainers）将附加 1。 由于 ResourceContainers 表具有用于订阅的类型和用于资源组的类型，因此可以使用任一类型来联接到 Resources 表中的资源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

以下查询显示了 `join` 的更复杂用法。 首先，该查询使用 `project` 针对 Azure Key Vault 保管库资源类型从 Resources 获取字段。 下一步使用 `join` 将结果与 ResourceContainers合并，其中，type 是既在第一个表的 `project` 中又在已联接表的 `project` 中的属性上的订阅。 字段重命名避免了 `join` 将其添加为 name1，因为该字段已从 Resources 投影。 查询结果是单个密钥保管库，其中显示密钥保管库的类型、名称、位置和资源组以及密钥保管库所在订阅的名称。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> 限制具有 `project` 的 `join` 结果时，`join` 用于关联两个表的属性（在上述示例中为 subscriptionId）必须包含在 `project` 中。

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>扩展属性（预览）

作为预览功能，除了 Azure 资源管理器提供的属性以外，Resource Graph 中的某些资源类型还有其他类型相关的属性可供查询。 这组值（称为“扩展属性”）存在于 `properties.extended` 中受支持的资源类型中。 若要查看哪些资源类型具有“扩展属性”，请使用以下查询：

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

示例：通过 `instanceView.powerState.code` 获取虚拟机计数：

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resource Graph 自定义语言元素

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>共享查询语法（预览）

作为预览功能，[共享查询](../tutorials/create-share-query.md)可在 Resource Graph 查询中直接访问。 在这种情况下，可以创建标准查询作为共享查询并重复使用它们。 若要在 Resource Graph 查询中调用共享查询，请使用 `{{shared-query-uri}}` 语法。 共享查询的 URI 是该查询的“设置”页上的共享查询的资源 ID。 在此示例中，我们的共享查询 URI 是 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS`。
此 URI 指向我们想要在另一个查询中引用的共享查询的订阅、资源组和全名。 此查询与[教程：创建和共享查询](../tutorials/create-share-query.md)中创建的查询相同。

> [!NOTE]
> 无法保存将共享查询作为共享查询引用的查询。

示例 1：仅使用共享查询

此 Resource Graph 查询的结果与存储在共享查询中的查询相同。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

示例 2：将共享查询并入更大的查询中

此查询首先使用共享查询，然后使用 `limit` 进一步限制结果。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>支持的 KQL 语言元素

Resource Graph 支持部分 KQL [数据类型](/azure/data-explorer/kusto/query/scalar-data-types/)、[标量函数](/azure/data-explorer/kusto/query/scalarfunctions)、[标量运算符](/azure/data-explorer/kusto/query/binoperators)和[聚合函数](/azure/data-explorer/kusto/query/any-aggfunction)。 Resource Graph 支持特定[表格运算符](/azure/data-explorer/kusto/query/queries)，其中一些运算符具有不同的行为。

### <a name="supported-tabulartop-level-operators"></a>支持的表格/顶级运算符

下面是 Resource Graph 支持的 KQL 表格运算符的列表，以及特定示例：

|KQL |Resource Graph 示例查询 |说明 |
|---|---|---|
|[计数](/azure/data-explorer/kusto/query/countoperator) |[对密钥保管库进行计数](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/data-explorer/kusto/query/distinctoperator) |[显示包含存储的资源](../samples/starter.md#show-storage) | |
|[extend](/azure/data-explorer/kusto/query/extendoperator) |[按 OS 类型对虚拟机进行计数](../samples/starter.md#count-os) | |
|[join](/azure/data-explorer/kusto/query/joinoperator) |[具有订阅名称的密钥保管库](../samples/advanced.md#join) |支持的联接类型：[innerunique](/azure/data-explorer/kusto/query/joinoperator#default-join-flavor)、[inner](/azure/data-explorer/kusto/query/joinoperator#inner-join)、[leftouter](/azure/data-explorer/kusto/query/joinoperator#left-outer-join)。 单个查询中最多只能有 3 个 `join`（其中 1 个可以是跨表 `join`）。 如果所有跨表 `join` 都在 Resource 与 ResourceContainers 之间使用，则会允许 3 个跨表 `join`。 不允许使用自定义联接策略，如广播联接。 有关哪些表可以使用 `join`，请参阅 [Resource Graph 表](#resource-graph-tables)。 |
|[limit](/azure/data-explorer/kusto/query/limitoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |Synonym of `take`. 无法与 [Skip](./work-with-data.md#skipping-records)配合使用。 |
|[mvexpand](/azure/data-explorer/kusto/query/mvexpandoperator) | | 旧运算符，请改用 `mv-expand`。 RowLimit 最大值为 400。 默认值为 128。 |
|[mv-expand](/azure/data-explorer/kusto/query/mvexpandoperator) |[列出具有特定写入位置的 Cosmos DB](../samples/advanced.md#mvexpand-cosmosdb) |RowLimit 最大值为 400。 默认值为 128。 单个查询中的 `mv-expand` 数量限制为 2 个。|
|[order](/azure/data-explorer/kusto/query/orderoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |`sort` 的同义词 |
|[parse](/azure/data-explorer/kusto/query/parseoperator) |[获取网络接口的虚拟网络和子网](../samples/advanced.md#parse-subnets) |如果属性存在，则最好直接访问属性，而不是使用 `parse`。 |
|[project](/azure/data-explorer/kusto/query/projectoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) | |
|[project-away](/azure/data-explorer/kusto/query/projectawayoperator) |[删除结果中的列](../samples/advanced.md#remove-column) | |
|[sort](/azure/data-explorer/kusto/query/sortoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |`order` 的同义词 |
|[summarize](/azure/data-explorer/kusto/query/summarizeoperator) |[对 Azure 资源进行计数](../samples/starter.md#count-resources) |仅已简化首页 |
|[take](/azure/data-explorer/kusto/query/takeoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |Synonym of `limit`. 无法与 [Skip](./work-with-data.md#skipping-records)配合使用。 |
|[返回页首](/azure/data-explorer/kusto/query/topoperator) |[按名称及其 OS 类型显示前五个虚拟机](../samples/starter.md#show-sorted) | |
|[union](/azure/data-explorer/kusto/query/unionoperator) |[将两个查询的结果合并为单个结果](../samples/advanced.md#unionresults) |允许使用单个表：_T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`ColumnName\] Table。 单个查询中的 `union` 分支限制为 3。 不允许对 `union` 分支表进行模糊解析。 可以在单个表中使用，也可以在 Resources 和 ResourceContainers 表中使用。 |
|[where](/azure/data-explorer/kusto/query/whereoperator) |[显示包含存储的资源](../samples/starter.md#show-storage) | |

单个 Resource Graph SDK 查询中默认最多只能有 3 个 `join` 和 3 个 `mv-expand` 运算符。 可以通过“帮助 + 支持”来请求为租户提高这些限制。

为了支持“打开查询”门户体验，Azure Resource Graph 资源管理器具有比 Resource Graph SDK 更高的全局限制。

## <a name="query-scope"></a>查询范围

查询从中返回资源的订阅或[管理组](../../management-groups/overview.md)的范围默认为基于授权用户上下文的订阅列表。 如果未定义管理组或订阅列表，则查询范围为所有资源（包括 [Azure Lighthouse](../../../lighthouse/overview.md) 委托资源）。

可以手动定义要查询的订阅或管理组的列表，以更改结果的范围。 例如，REST API `managementGroups` 属性采用管理组 ID，该 ID 不同于管理组的名称。 指定 `managementGroups` 时，将包含在指定管理组层次结构中或其下的前 5,000 个订阅的资源。 `managementGroups` 与 `subscriptions` 不能同时使用。

示例：使用 ID“myMG”查询管理组层次结构中名为“我的管理组”的所有资源。

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2021-03-01
  ```

- 请求正文

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroups": ["myMG"]
  }
  ```

## <a name="escape-characters"></a>转义字符

某些属性名称（例如，包含 `.` 或 `$` 的名称）必须在查询中进行包装或转义，否则属性名称将被错误解释，并且不会提供预期结果。

- `.` - 包装属性名称，如下所示：`['propertyname.withaperiod']`

  用于包装属性 odata.type 的示例查询：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - 转义属性名称中的字符。 使用的转义字符取决于运行 Resource Graph 的 shell。

  - bash - `\`

    在 Bash 中对 \$type 属性进行转义的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - cmd - 不要转义 `$` 字符。

  - **PowerShell** - ``` ` ```

    用于在 PowerShell 中转义属性 \$type 的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用的语言。
- 请参阅[高级查询](../samples/advanced.md)中的高级用法。
- 详细了解如何[浏览资源](explore-resources.md)。
