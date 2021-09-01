---
title: 对 Azure 数据工厂中的管道业务流程和触发器进行故障排除
description: 使用不同方法排查 Azure 数据工厂中的管道触发器问题。
author: ssabat
ms.service: data-factory
ms.date: 08/17/2021
ms.subservice: troubleshooting
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: aed6df814ddfb240093aad8ff981e9b89a6f2f52
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867568"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>对 Azure 数据工厂中的管道业务流程和触发器进行故障排除

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，假设你有一个管道，它在上午 8:00、9:00 和 10:00 点运行。 在这种情况下，将有三次独立的管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是标识该特定管道运行的全局唯一标识符 (GUID)。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 你可以手动运行管道，也可以使用触发器来这样做。 有关详细信息，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md)。

## <a name="common-issues-causes-and-solutions"></a>常见问题、原因和解决方案

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 应用管道在建立专用终结点连接时引发错误
 
你已安装数据工厂，并在 Azure 专用终结点上运行一个函数应用。 你正在尝试运行一个与该函数应用交互的管道。 你已尝试三种不同的方法，但其中一种方法返回了错误“错误的请求”，另两种方法返回了“103 禁止错误”。

**原因**

数据工厂目前不支持对函数应用使用专用终结点连接器。 Azure Functions 拒绝了调用，因为它已配置为仅允许从专用链接进行连接。

**分辨率**

创建一个 **PrivateLinkService** 终结点并提供函数应用的 DNS。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>管道运行已取消，但监视器仍显示进度状态

**原因**

很多情况下，取消管道运行后，管道监视仍然显示进度状态。 此问题是由浏览器缓存问题导致的。 你也可能没有合适的监视筛选器。

**分辨率**

刷新浏览器，并应用正确的监视筛选器。
 
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

创建复制活动时选择“二进制复制”选项。 这样一来，若要将数据从一个数据湖批量复制或迁移到另一个数据湖，数据工厂不需打开文件来读取架构。 数据工厂会将每个文件视为二进制文件并将其复制到其他位置。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>达到数据流集成运行时的容量限制时管道运行失败

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

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>在 Web 活动中调用 REST API 时，管道运行错误

**问题**

错误消息：

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**原因**

当且仅当 Azure 数据工厂成员被分配到“参与者”角色时，管道才能使用 Web 活动调用 ADF REST API 方法。 必须首先配置 Azure 数据工厂托管标识并将它添加到“参与者”安全角色。 

**分辨率**

在 Web 活动的“设置”选项卡中使用 Azure 数据工厂的 REST API 之前，必须先配置安全性。 当且仅当 Azure 数据工厂托管标识被分配到“参与者”角色时，Azure 数据工厂管道才能使用 Web 活动调用 ADF REST API 方法。 首先打开 Azure 门户，然后单击左侧菜单中的“所有资源”链接。 选择“Azure 数据工厂”，添加具有“参与者”角色的 ADF 托管标识，具体方法是单击“添加角色分配”框中的“添加”按钮 。


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>如何检查和转移管道中活动级别的成功和失败

**原因**

Azure 数据工厂业务流程允许条件逻辑，使用户能够根据前面的活动的结果采用不同的路径。 它允许四个条件路径：“成功时（默认通过）”、“失败时”、“完成时”和“跳过时”。    

Azure 数据工厂会评估所有叶级活动的结果。 仅当所有叶都成功时，管道结果才是成功的。 如果跳过了某个叶活动，则会改为评估其父活动。 

**分辨率**

* 按照[如何处理管道故障和错误](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)一文的说明实施活动级检查。
* 按照[通过工厂进行查询](/rest/api/datafactory/pipelineruns/querybyfactory)一文的说明使用 Azure 逻辑应用来定期监视管道。
* [以可视方式监视管道](monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>如何按固定的时间间隔监视管道失败

**原因**

你可能需要按特定时间间隔（例如 5 分钟）监视数据工厂管道。 可以使用终结点从数据工厂查询和筛选管道运行。 

**分辨率**
* 可以根据[按照工厂进行查询](/rest/api/datafactory/pipelineruns/querybyfactory)中所述，将 Azure 逻辑应用设置为每隔 5 分钟查询所有失败的管道。 然后，可将事件报告给票证系统。
* 你可以重新运行管道和活动，如[此文](monitor-visually.md#rerun-pipelines-and-activities)所述。
* 如果已取消活动，或者出现故障，可按照[在活动出现故障后重新运行](monitor-visually.md#rerun-from-failed-activity)来重新运行活动。
* [以可视方式监视管道](monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>提高并行度不会提高吞吐量

**原因** 

*ForEach* 中的并行度实际上是最大并行度。 我们无法保证同时发生特定数量的执行，但此参数将保证永不超出所设置的值。 应将此值视为在控制对源和接收器的并发访问时使用的限制。

有关 *ForEach* 的已知事实
 * Foreach 具有名为“批计数”(n) 的属性，其中默认值为 20，最大值为 50。
 * 批计数 (n) 用于构造 n 个队列。 
 * 每个队列按顺序运行，但可以并行运行多个队列。
 * 队列是预先创建的。 这意味着，在运行时不会重新平衡队列。
 * 在任意时间，最多只能处理每个队列的一个项。 这意味着，在任意给定时间，最多会处理 n 个项。
 * foreach 的总处理时间等于最长队列的处理时间。 这意味着 foreach 活动取决于队列的构造方式。
 
**分辨率**

 * 不应在并行运行的 *For Each* 中使用 *SetVariable* 活动。
 * 考虑到队列的构造方式，客户可以通过设置多个 foreach（每个 foreach 的项处理时间相似）来提高 foreach 性能 。 
 * 这可以确保并行（而不是按顺序）处理长时间运行。

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>管道处于排队状态或长时间停滞
 
 **原因**
 
 发生这种情况的原因有多种，例如达到了并发限制、服务中断、网络故障，等等。
 
 **分辨率**
 
* 并发限制：如果管道具有并发策略，请确认当前没有旧管道运行。 
* 监视限制：请转到 ADF 创作画布，选择你的管道，并确定是否已为其分配了并发属性。 如果已分配，请转到“监视”视图，确保过去 45 天内没有任何正在进行的事项。 如果有某个事项正在进行，可以取消它，然后新的管道运行应当会启动。

* 暂时性问题：有可能是暂时性网络问题、凭据故障、服务中断等影响了你的运行。如果发生这种情况，Azure 数据工厂会通过一个内部恢复过程来应对，它会监视所有运行，并在发现问题时启动这些运行。 你可以重新运行管道和活动，如[此文](monitor-visually.md#rerun-pipelines-and-activities)所述。 如果已取消活动，或者出现故障，可按照[在活动出现故障后重新运行](monitor-visually.md#rerun-from-failed-activity)来重新运行活动。 此过程每隔 1 小时发生一次，因此，如果运行停滞超过 1 小时，请创建支持案例。


 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>ADF 复制和数据流中活动的启动时间很长

**原因**

如果尚未对数据流或优化的 SHIR 实现生存时间功能，则可能会发生这种情况。

**分辨率**

* 如果每个复制活动最多需要 2 分钟才能开始，而问题主要出在 VNet 联接（相较于Azure IR），这可能是复制性能问题。 若要查看故障排除步骤，请参阅[复制性能改进](copy-activity-performance-troubleshooting.md)。
* 可以使用“生存时间”功能来减少数据流活动的群集启动时间。 请查看[数据流集成运行时](control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)。

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>遇到 SHIR（自承载 Integration Runtime）的容量问题
 
 **原因**
 
如果未根据工作负载纵向扩展 SHIR，则可能会发生这种情况。

**分辨率**

* 如果遇到 SHIR 的容量问题，请升级 VM 以增加节点来平衡活动。 如果收到有关自承载 IR 一般性故障或错误、自承载 IR 升级或自承载 IR 连接问题的错误消息（可能会生成长队列），请参阅[排查自承载集成运行时问题](self-hosted-integration-runtime-troubleshoot-guide.md)。

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>由于 ADF 复制和数据流队列较长而出现错误消息

**原因**

由于各种原因，可能会出现与长队列相关的错误消息。 

**分辨率**
* 如果通过连接器收到了来自任何源或目标的错误消息（可能会生成长队列），请参阅[连接器故障排除指南](connector-troubleshoot-guide.md)
* 如果收到有关映射数据流的错误消息（可能会生成长队列），请参阅[数据流故障排除指南](data-flow-troubleshoot-guide.md)。
* 如果收到有关 Databricks、自定义活动或 HDI 等其他活动的错误消息（可能会生成长队列），请参阅[活动故障排除指南](data-factory-troubleshoot-guide.md)
* 如果收到有关运行 SSIS 包（可能会生成长队列）的错误消息，请转到 [Azure-SSIS 包执行故障排除指南](ssis-integration-runtime-ssis-activity-faq.yml)和 [Integration Runtime 管理故障排除指南](ssis-integration-runtime-management-troubleshoot.md)。

### <a name="error-message---codebadrequest-messagenull"></a>错误消息 - "code":"BadRequest", "message":"null"

**原因**

这是一个用户错误，因为触及 management.azure.com 的 JSON 有效负载已损坏。 由于用户调用未到达 ADF 服务层，因此不会存储任何日志。

**分辨率**

使用 Edge/Chrome 浏览器开发人员工具从 ADF 门户执行对 API 调用的网络跟踪。 你会看到有问题的 JSON 有效负载，这可能是由特殊字符（例如“$”）、空格和其他类型的用户输入导致的。 修复字符串表达式后，你将在浏览器中继续执行其余的 ADF 用法调用。

### <a name="foreach-activities-do-not-run-in-parallel-mode"></a>ForEach 活动不以并行模式运行

**原因**

正在以调试模式运行 ADF。

**分辨率**

在触发器模式下执行管道。

### <a name="cannot-publish-because-account-is-locked"></a>无法发布，因为帐户被锁定

**原因**

你更改了协作分支以删除存储事件触发器。 你在尝试发布时收到“`Trigger deactivation error`”消息。

**解决方法**

这是由于锁定了用于事件触发器的存储帐户。 解锁帐户。

### <a name="expression-builder-fails-to-load"></a>无法加载表达式生成器

**原因**

可能是由于 Web 浏览器出现网络或缓存问题而导致表达式生成器无法加载。  

**分辨率**


请将 Web 浏览器升级到受支持的浏览器的最新版本，清除站点的 Cookie，然后刷新页面。

### <a name="codebadrequestmessageerrorcodeflowrunsizelimitexceeded"></a>"Code":"BadRequest","message":"ErrorCode=FlowRunSizeLimitExceeded

**原因**

你链接了许多活动。

**分辨率**

可以将管道拆分为子管道，并通过 ExecutePipeline 活动将它们衔接在一起。 

###  <a name="how-to-optimize-pipeline-with-mapping-data-flows-to-avoid-internal-server-errors-concurrency-errors-etc-during-execution"></a>如何使用映射数据流来优化管道，以避免在执行期间发生内部服务器错误、并发错误等

**原因**

你尚未优化映射数据流。

**分辨率**

* 处理大量的数据和转换时，请使用内存优化计算。
* 减小批大小，以防每项活动都出现问题。
* 纵向扩展你的数据库和仓库，使之与 ADF 的性能匹配。 
* 为并行运行的活动使用单独的 IR（集成运行时）。
* 相应地调整源和接收器的分区。 
* 查看[数据流优化](concepts-data-flow-performance.md)

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](/answers/topics/azure-data-factory.html)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](/answers/topics/azure-data-factory.html)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
