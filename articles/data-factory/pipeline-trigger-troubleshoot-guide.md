---
title: 对 Azure 数据工厂中的管道业务流程和触发器进行故障排除
description: 使用不同方法排查 Azure 数据工厂中的管道触发器问题。
author: ssabat
ms.service: data-factory
ms.date: 04/01/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 49205025e26f7c0eb609638e70a58c9c0c14748e
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385405"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>对 Azure 数据工厂中的管道业务流程和触发器进行故障排除

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，假设你有一个管道，它在上午 8:00、9:00 和 10:00 点运行。 在这种情况下，将有三次独立的管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是标识该特定管道运行的全局唯一标识符 (GUID)。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 你可以手动运行管道，也可以使用触发器来这样做。 有关详细信息，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md)。

## <a name="common-issues-causes-and-solutions"></a>常见问题、原因和解决方案

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 应用管道在建立专用终结点连接时引发错误
 
你已安装数据工厂，并在专用终结点上运行一个 Azure 函数应用。 你正在尝试运行一个与该函数应用交互的管道。 你已尝试三种不同的方法，但其中一种方法返回了错误“错误的请求”，另两种方法返回了“103 禁止错误”。

**原因**

数据工厂目前不支持对函数应用使用专用终结点连接器。 Azure Functions 拒绝了调用，因为它已配置为仅允许从专用链接进行连接。

**分辨率**

创建一个 **PrivateLinkService** 终结点并提供函数应用的 DNS。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>管道运行已取消，但监视器仍显示进度状态

**原因**

很多情况下，取消管道运行后，管道监视仍然显示进度状态。 此问题是由浏览器缓存问题导致的。 你也可能没有合适的监视筛选器。

**分辨率**

刷新浏览器，并应用合适的监视筛选器。
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>复制管道时出现“DelimitedTextMoreColumnsThanDefined”错误
 
 **原因**
 
如果你要复制的文件夹包含的文件具有不同架构（例如可变列数、不同分隔符、引号字符设置或某个数据问题），则数据工厂管道可能会引发以下错误：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**分辨率**

创建复制活动时，选择“二进制复制”选项。 这样一来，若要将数据从一个数据湖批量复制或迁移到另一个数据湖，数据工厂不需打开文件来读取架构。 数据工厂会将每个文件视为二进制文件并将其复制到其他位置。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>当你达到数据流的集成运行时容量限制时，管道运行会失败

**问题**

错误消息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**原因**

已达到集成运行时的容量限制。 你可能在使用同一个集成运行时同时运行大量数据流。 有关详细信息，请参阅 [Azure 订阅和服务的限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2)。

**分辨率**
 
- 在不同的触发时间运行你的管道。
- 创建一个新的集成运行时，并将你的管道拆分到多个集成运行时中。

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>在 Web 活动中调用 REST api 时，管道运行错误

**问题**

错误消息：

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**原因**

当且仅当 Azure 数据工厂成员被分配到“参与者”角色时，管道才能使用 Web 活动调用 ADF REST API 方法。 必须首先配置将 Azure 数据工厂的“托管标识”添加到“参与者”安全角色。 

**分辨率**

在 Web 活动的“设置”选项卡中使用 Azure 数据工厂的 REST API 之前，必须先配置安全性。 当且仅当 Azure 数据工厂托管标识被分配到“参与者”角色时，Azure 数据工厂管道才能使用 Web 活动调用 ADF REST API 方法。 首先打开 Azure 门户，然后单击左侧菜单中的“所有资源”链接。 单击“添加角色分配”框中的“添加”按钮，选择“Azure 数据工厂”添加具有“参与者”角色的 ADF 托管标识。


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>如何检查和转移管道中活动级别的成功和失败

**原因**

Azure 数据工厂业务流程允许条件逻辑，使用户能够根据前面的活动的结果采用不同的路径。 它允许四个条件路径：“成功时（默认通过）”、“失败时”、“完成时”和“跳过时”。    

Azure 数据工厂会评估所有叶级活动的结果。 仅当所有叶都成功时，管道结果才是成功的。 如果跳过了某个叶活动，则会改为评估其父活动。 

**分辨率**

* 按照[如何处理管道故障和错误](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)一文的说明实施活动级检查。
* 按照[通过工厂进行查询](/rest/api/datafactory/pipelineruns/querybyfactory)一文的说明使用 Azure 逻辑应用来定期监视管道。
* [以直观的方式监视管道](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>如何按固定时间间隔监视管道故障

**原因**

你可能需要按特定时间间隔（例如 5 分钟）监视数据工厂管道。 可以使用终结点从数据工厂查询和筛选管道运行。 

**分辨率**
* 可以将 Azure 逻辑应用设置为每隔 5 分钟查询一次所有故障管道，如[通过工厂进行查询](/rest/api/datafactory/pipelineruns/querybyfactory)一文所述。 然后，你可以将事件报告给票证系统。
* [以直观的方式监视管道](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>提高并行度不会导致吞吐量有所增加

**原因** 

ForEach 中的并行度实际上是最大并行度。 我们无法保证同时发生特定数量的执行，但此参数将保证从不超出所设置的值。 你应将其视为限制，以在控制对源和接收器的并发访问时使用。

有关 ForEach 的已知事实
 * Foreach 具有名为 batch count(n) 的属性，其中默认值为 20，最大值为 50。
 * 批处理数 n 用于构造 n 个队列。 
 * 每个队列按顺序运行，但可以并行运行多个队列。
 * 队列是预先创建的。 这意味着在运行时不会重新平衡队列。
 * 在任何时间，每个队列最多处理一项。 这意味着，在任何给定时间，最多处理 n 个项。
 * Foreach 的总处理时间等于最长队列的处理时间。 这意味着 foreach 活动取决于构造队列的方式。
 
**分辨率**

 * 不应在并行运行的 For Each 内使用 SetVariable 活动。
 * 考虑到队列的构造方式，客户可以通过设置多个 *foreach*（每个 foreach 的项处理时间相似）来提高 foreach 性能。 
 * 这将确保并行（而不是按顺序）处理长时间运行。

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>管道状态为已排队或已停滞很长时间
 
 **原因**
 
 出现这种情况的原因有多种，如达到并发限制、出现服务中断、发生网络故障等。
 
 **分辨率**
 
* 并发限制：如果管道具有并发策略，请验证是否没有旧管道运行正在进行。 Azure 数据工厂中允许的最大管道并发数为 10 个管道。 
* 监视限制：请转到“ADF 创作”画布，选择你的管道，并确定是否已为其分配了并发属性。 如果是，请转到“监视”视图，确保过去 45 天内没有任何正在进行的事项。 如果发现有某个事项正在进行，可以取消它，然后新的管道运行应当会启动。
* 暂时性问题：有可能是暂时性网络问题、凭据故障、服务中断等影响了你的运行。如果发生这种情况，Azure 数据工厂有一个内部恢复过程会予以应对，它会监视所有运行，并在发现问题时启动这些运行。 此过程每隔 1 小时发生一次，因此，如果运行停滞超过 1 小时，请创建支持案例。
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>ADF 复制和数据流中的活动的启动时间较长

**原因**

如果尚未对数据流实施“生存时间”功能或者尚未优化 SHIR，可能会发生这种情况。

**分辨率**

* 如果每个复制活动最多需要 2 分钟才能开始，而问题主要出在 VNet 联接（相较于Azure IR），这可能是复制性能问题。 若要查看故障排除步骤，请转到[复制性能改进](./copy-activity-performance-troubleshooting.md)。
* 可以使用“生存时间”功能来减少数据流活动的群集启动时间。 请查看[数据流集成运行时](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)。

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>遇到 SHIR（自承载集成运行时）的容量问题
 
 **原因**
 
如果未按工作负荷扩展 SHIR，可能会发生这种情况。

**分辨率**

* 如果遇到 SHIR 的容量问题，请升级 VM 以增加节点来平衡活动。 如果收到关于自承载 IR 一般性故障或错误、自承载 IR 升级或自承载 IR 连接问题（可能会生成长队列）的错误消息，请转到[自承载集成运行时故障排除](./self-hosted-integration-runtime-troubleshoot-guide.md)。

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>由于 ADF 复制和数据流的长队列导致的错误消息

**原因**

由于各种原因，可能会出现长队列相关错误消息。 

**分辨率**
* 如果通过连接器（可能会生成长队列）从任何源或目标收到错误消息，请转到[连接器故障排除指南](./connector-troubleshoot-guide.md)。
* 如果收到有关映射数据流（可能会生成长队列）的错误消息，请转到[数据流故障排除指南](./data-flow-troubleshoot-guide.md)。
* 如果收到有关 Databricks、自定义活动或 HDI 等其他活动（可能会生成长队列）的错误消息，请转到[活动故障排除指南](./data-factory-troubleshoot-guide.md)。
* 如果收到有关运行 SSIS 包（可能会生成长队列）的错误消息，请转到 [Azure-SSIS 包执行故障排除指南](./ssis-integration-runtime-ssis-activity-faq.md)和 [Integration Runtime 管理故障排除指南](./ssis-integration-runtime-management-troubleshoot.md)。


## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](/answers/topics/azure-data-factory.html)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
