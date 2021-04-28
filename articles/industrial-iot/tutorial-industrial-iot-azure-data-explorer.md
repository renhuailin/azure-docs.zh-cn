---
title: 将 Azure 工业 IoT 数据拉取到 ADX
description: 在本教程中，您将了解如何将 IIoT 数据拉取到 ADX 中。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 9a18609ab0dc41031a22db6d8b21c1fff83920f2
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137303"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>教程：将 Azure 工业 IoT 数据拉取到 ADX

Azure 工业 IoT (IIoT) 平台将边缘模块和云微服务与大量 Azure PaaS 服务相结合，为工业资产发现提供功能，并使用 OPC UA 从这些资产中收集数据。 [Azure 数据资源管理器 (ADX) ](/azure/data-explorer)是包含数据分析功能的 IIoT 数据的自然目标，可让您从通过 OPC 发布者连接到 IoT 中心的 OPC UA 服务器对引入数据运行灵活的查询。 尽管 ADX 群集可以直接从 IoT 中心引入数据，但 IIoT 平台会进一步处理数据，使其更有用，然后再在完全部署微服务时将其放在所提供的事件中心（参见 IIoT 平台体系结构）。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 ADX 中创建表
> * 将事件中心连接到 ADX 群集
> * 分析 ADX 中的数据

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>如何使数据在 ADX 群集中可用，以便有效地对其进行查询 

如果我们查看事件中心的消息格式（由类 Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel 定义），可以看到我们的 ADX 表架构所需的结构提示。

![结构](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

下面是使数据在 ADX 群集中可用并能有效查询该数据所需的步骤。  
1. 创建 ADX 群集。 如果尚未使用 IIoT 平台预配 ADX 群集，或者想要使用其他群集，请按照[此处](/azure/data-explorer/create-cluster-database-portal#create-a-cluster)的步骤操作。 
2. 如[此处](/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)所述，在 ADX 群集上启用流式引入。 
3. 按照[此处](/azure/data-explorer/create-cluster-database-portal#create-a-database)的步骤创建 ADX 数据库。

下一步，我们将使用 [ADX Web 界面](/azure/data-explorer/web-query-data)来运行必要的查询。 请确保将群集添加到 Web 界面，如链接中所述。  
 
4. 在 ADX 中创建一个表，将引入数据放入。  尽管 MonitoredItemMessageModel 类可用于定义 ADX 表的架构，但建议先将数据引入包含一列类型为[动态](/azure/data-explorer/kusto/query/scalar-data-types/dynamic)的临时表中。 这样，我们就可以更灵活地处理数据，并将数据处理到其他表中（可能会将其与其他数据源组合），从而满足多个用例的需求。 以下 ADX 查询创建临时表“iiot_stage”，其中包含一个“有效负载”列，

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

还需要添加 json 引入映射，以指示群集将事件中心的整个 Json 消息放入临时表中，

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. 现在，我们的表已准备好从事件中心接收数据。 
6. 使用[此处](/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)的说明将事件中心连接到 ADX 群集，并开始将数据引入到临时表中。 只需创建连接，因为我们已经有了 IIoT 平台预配的事件中心。  
7. 验证连接后，数据将开始流动到表中，并且在短暂延迟后，可以开始检查表中的数据。 在 ADX Web 界面中使用以下查询查看 10 行数据示例。 在此，我们可以看到有效负载中的数据与前面提到的 MonitoredItemMessageModel 类的相似之处。

![查询](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. 现在，让我们通过直接分析“有效负载”列中的“动态”数据来对此数据运行分析。 在此示例中，我们将计算从特定时间点（由 variable min_t 定义）引入的所有记录上由“DisplayName”: “PositiveTrendData”在 10 分钟时间识别的平均遥测数据 let min_t = datetime(2020-10-23); iiot_stage | where todatetime(payload.Timestamp) > min_t | where tostring(payload.DisplayName)== 'PositiveTrendData' | summarize event_avg = avg(todouble(payload.Value)) by bin(todatetime(payload.Timestamp), 10 m)
 
由于我们的“有效负载”列包含“动态”数据类型，因此我们需要在查询时执行数据转换，以便对正确的数据类型执行计算。

![有效负载时间戳](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

正如前文所述，使用一个“动态”列将 OPC UA 数据引入到临时表中，可以为我们提供灵活性。 但是，在查询时必须运行数据类型转换可能会导致查询执行延迟，特别是当数据量很大且存在许多并发查询时。 在此阶段，我们可以再创建一个表，其中包含已确定的数据类型，以便避免查询时运行数据类型转换。
 
9. 为已分析的数据创建一个新表，其中包含从临时表中动态“有效负载”内容中进行的有限选择。 请注意，我们已为遥测中预期的每个预期数据类型创建了一个值列。

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. 在数据库级别创建函数，以从临时表投影所需数据。 在此我们从“有效负载”列中选择“Timestamp”、“PublisherId”、“DisplayName”、“Datatype”和“NodeId”项，并分别投影为“Tag_Timestamp”、“Tag_PublisherId”、“Tag”、“Tag_Datatype”、“Tag_NodeId”。 “Value”项基于“DataType”投影为三个不同的部分。

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

有关在 ADX 中映射数据类型的更多信息，请参阅[此处](/azure/data-explorer/kusto/query/scalar-data-types/dynamic)，有关 ADX 中的函数，可从[此处](/azure/data-explorer/kusto/query/schema-entities/stored-functions)开始。
 
11. 使用更新策略将上一步中的函数应用到已分析的表中。 每当基于对插入源表的数据运行的转换查询将新数据插入源表时，更新[策略](/azure/data-explorer/kusto/management/updatepolicy)都会指示 ADX 自动将数据追加到目标表中。 对于我们在上一步创建的函数所定义的更新策略，我们可以使用以下查询将已分析的表指定为目标，将临时表指定为源。

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

执行上述查询后，数据将开始流动，并填充目标表“iiot_parsed”。 我们可以在“iiot_parsed as follows”中查看此数据。

![已分析表](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. 现在，让我们来看一下如何重复我们在上一步中所做的分析；计算从特定时间点（由 variable min_t 定义）引入的所有记录上由“DisplayName”: “PositiveTrendData”在 10 分钟时间识别的平均遥测数据。 现在，“PositveTrendData”标记的值存储在 Double 数据类型的列中，这会提高查询性能。

![重复分析](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. 最后，我们来比较这两种情况下的查询性能。 我们可以使用 ADX UI 中的“Stats”（位于查询结果上方）查找执行查询所花的时间。  

![查询性能 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![查询性能 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

可以看到，使用已分析表的查询的速度比临时表的查询速度几乎快两倍。 在此示例中，我们的数据集较小，并且没有并发查询正在运行，因此对查询执行时间的影响并不大，但对于实际工作负载，可能会对性能产生重大影响。 这就是为什么一定要将不同数据类型分隔到不同列中的原因。

> [!NOTE] 
> 更新策略仅适用于设置策略后引入到临时表中的数据，不适用于预先存在的任何数据。 例如，需要更改更新策略时，需要考虑这一点。 完整的详细信息可以在 ADX 文档中找到。

## <a name="next-steps"></a>后续步骤
现在，您已了解如何更改配置的默认值，您可以 

> [!div class="nextstepaction"]
> [配置工业 IoT 组件](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [使用时序见解可视化和分析数据](tutorial-visualize-data-time-series-insights.md)