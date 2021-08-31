---
title: 使用 Azure 数据资源管理器进行查询
titleSuffix: Azure Digital Twins
description: 了解 Azure 数据资源管理器的 Azure 数字孪生查询插件
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438703"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Azure 数据资源管理器的 Azure 数字孪生查询插件

使用 [Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)的 Azure 数字孪生插件，你可以运行可跨 Azure 数字孪生图形和 Azure 数据资源管理器时序数据库访问和合并数据的 Azure 数据资源管理器查询。 使用此插件，根据数字孪生及其关系进行推理来情景化不同的时序数据，从而深入了解建模环境的行为。

例如，借助此插件，可以编写 Kusto 查询，该查询可以
1. 通过 Azure 数字孪生查询插件选择感兴趣的数字孪生，
2. 根据 Azure 数据资源管理器中各自的时序加入这些孪生，然后 
3. 对这些孪生执行高级时序分析。  

将 Azure 数字孪生的孪生图形中的数据与 Azure 数据资源管理器中的时序数据组合在一起，可帮助你了解解决方案的各个部分的操作行为。 

## <a name="using-the-plugin"></a>使用插件

可以使用以下命令在 Kusto 查询中调用插件。 有两个占位符：`<Azure-Digital-Twins-endpoint>` 和 `<Azure-Digital-Twins-query>`，分别是代表 Azure 数字孪生实例终结点和 Azure 数字孪生查询的字符串。 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

插件的工作原理是调用 [Azure 数字孪生查询 API](/rest/api/digital-twins/dataplane/query)，[查询语言结构](concepts-query-language.md)与使用 API 时相同，但有两种例外情况： 
* 不支持 `SELECT` 子句中的 `*` 通配符。 相反，使用插件执行的 Azure 数字孪生查询应在 `SELECT` 子句中使用别名。

    例如，考虑以下使用 API 执行的 Azure 数字孪生查询：
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    若要在使用插件时执行该查询，应对其进行重写，如下所示：
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* 插件返回的列名不得以 `$` 开头。 在 `SELECT` 子句中使用别名还有助于避免这种情况。

    例如，考虑以下使用 API 执行的 Azure 数字孪生查询：
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    若要在使用插件时执行该查询，应对其进行重写，如下所示：
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>必须向插件的用户授予 Azure 数字孪生数据读取者角色或 Azure 数字孪生数据所有者角色，因为用户的 Azure AD 令牌用于进行身份验证。 可以在 [Azure 数字孪生解决方案的安全性](concepts-security.md#authorization-azure-roles-for-azure-digital-twins)中找到有关如何分配此角色的信息。

有关如何使用插件的详细信息，请参阅 [azure_digital_twins_query_request 插件的 Kusto 文档](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)。

若要查看示例查询并使用示例数据完成演练，请参阅 GitHub 中的 [Azure 数据资源管理器的 Azure 数字孪生查询插件：示例查询和演练](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries)。

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>通过 Azure 数字孪生使用 Azure 数据资源管理器 IoT 数据

可通过多种方式将 IoT 数据引入到 Azure 数据资源管理器中。 将 Azure 数据资源管理器与 Azure 数字孪生结合使用时，可以使用以下两种方式：
* 使用 Azure 函数将数字孪生属性值记载到 Azure 数据资源管理器，该函数可处理孪生更改事件并将孪生数据写入 Azure 数据资源管理器，这类似于[与 Azure 时序见解集成](how-to-integrate-time-series-insights.md)中使用的过程。 此路径适用于使用遥测数据将数字孪生引入生活的客户。
* [直接从 IoT 中心或其他源将 IoT 数据引入到 Azure 数据资源管理器群集中](/azure/data-explorer/ingest-data-iot-hub)。 然后，Azure 数字孪生图形将用于通过联合 Azure 数字孪生/Azure 数据资源管理器查询情景化时序数据。 此路径可能适用于直接引入工作负载。 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>跨 Azure 数据资源管理器和 Azure 数字孪生映射数据

如果将时序数据直接引入到 Azure 数据资源管理器中，则可能需要将此原始时序数据转换为适用于联合 Azure 数字孪生/Azure 数据资源管理器查询的架构。

每当将新数据插入源表时，Azure 数据资源管理器中的[更新策略](/azure/data-explorer/kusto/management/updatepolicy) 都会允许你自动将数据转换并追加到目标表中。 

可以使用更新策略通过 Azure 数字孪生中的相应孪生 ID 来扩充原始时序数据，并将其保存到目标表中。 使用孪生 ID，可以将目标表与 Azure 数字孪生插件选择的数字孪生联接。 

例如，假设你创建了下表来保存流入 Azure 数据资源管理器实例的原始时序数据。 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

可以创建一个映射表，将时序 ID 与孪生 ID 和其他可选字段相关联。 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

然后，创建一个目标表来保存扩充的时序数据。 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

接下来，创建函数 `Update_rawData`，通过将原始数据与映射表联接来扩充原始数据。 这会将孪生 ID 添加到生成的目标表中。 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

最后，创建更新策略来调用函数并更新目标表。 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

创建目标表后，可以使用 Azure 数字孪生插件来选择相关孪生，然后将其与目标表中的时序数据联接。 

### <a name="example-schema"></a>示例架构

下面是一个可用于表示共享数据的架构示例。

| timestamp | twinId | modelId | name | 值 | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 2021-02-01 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | 温度 | 301.0 |  |  |

数字孪生属性以键值对 (`name, value`) 的形式存储。 `name` 和 `value` 存储为动态数据类型。 

该架构还支持根据 `relationshipTarget` 和 `relationshipID` 字段存储关系的属性。 键值架构无需为每个孪生属性创建列。

### <a name="representing-properties-with-multiple-fields"></a>表示具有多个字段的属性 

可能需要在架构中存储具有多个字段的属性。 通过在架构中将 JSON 对象存储为 `value` 来表示这些属性。

例如，如果想要表示具有三个字段（绕 Z 轴旋转、绕 X 轴旋转和绕 Y 轴旋转）的属性，则值对象将如下所示：`{"roll": 20, "pitch": 15, "yaw": 45}`。

## <a name="next-steps"></a>后续步骤

* 在 Azure 数据资源管理器中查看 Kusto 查询语言的插件文档：[azure_digital_twins_query_request plugin](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)

* 使用插件查看示例查询，包括在示例方案中运行查询的演练：[Azure 数据资源管理器的 Azure 数字孪生查询插件：示例查询和演练](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) 

* 阅读有关在 Azure 数字孪生中分析历史数据的另一个策略：[与 Azure 时序见解集成](how-to-integrate-time-series-insights.md)
