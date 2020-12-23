---
title: Azure 数据工厂中的管道业务流程和触发器疑难解答
description: 使用不同方法来解决 Azure 数据工厂中的管道触发器问题。
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589145"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure 数据工厂中的管道业务流程和触发器疑难解答

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，你有一个在凌晨8:00 点、9:00 AM 和 10:00 AM 执行的管道。 在这种情况下，将分三次单独运行管道，也即有三次管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是 GUID (全局唯一标识符) ，用于定义特定的管道运行。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 执行管道时，可以手动，也可以使用触发器。 有关详细信息，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md) 。

## <a name="common-issues-causes-and-solutions"></a>常见问题、原因和解决方案

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>带有 Azure 函数的管道引发了与私有终结点连接有关的错误
 
#### <a name="issue"></a>问题
对于某些上下文，你的数据工厂和 Azure Function App 在专用终结点上运行。 你正在尝试获取与 Azure Function App 进行交互的管道。 你已尝试三种不同的方法，但一个返回错误 `Bad Request` ，另两个方法返回 `103 Error Forbidden` 。

#### <a name="cause"></a>原因 
数据工厂当前不支持 Azure Function App 的专用终结点连接器。 这应该是 Azure Function App 拒绝调用的原因，因为它将被配置为仅允许来自专用链接的连接。

#### <a name="resolution"></a>解决方法
你可以创建 **PrivateLinkService** 类型的专用终结点并提供函数应用的 DNS，该连接应正常工作。

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>管道运行已终止，但监视器仍显示进度状态

#### <a name="issue"></a>问题
通常在终止管道运行时，管道监视仍会显示进度状态。 出现这种情况是因为浏览器出现缓存问题，而您没有适当的筛选器进行监视。

#### <a name="resolution"></a>解决方法
刷新浏览器，并应用合适的筛选器进行监视。
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>复制管道故障–找到的列数超出了预期的列计数 (DelimitedTextMoreColumnsThanDefined) 

#### <a name="issue"></a>问题  
如果要复制的特定文件夹下的文件包含具有不同架构（如可变列数、不同分隔符、引号字符设置或某些数据问题）的文件，则数据工厂管道最终会在此错误中运行：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>解决方法
创建复制数据活动时，请选择 "二进制复制" 选项。 通过这种方式，可将数据从一个 Data Lake 大容量复制或迁移到另一（带有 **binary** 选项），数据工厂不会打开要读取架构的文件，而只是将每个文件视为二进制文件并将其复制到另一个位置。

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>当达到集成运行时的容量限制时，管道运行失败

#### <a name="issue"></a>问题
错误消息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

此错误表示每个 integration runtime 的限制，目前为50。 有关详细信息，请参阅 [限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) 。

如果同时使用同一集成运行时执行大量数据流，则可能导致这种错误。

#### <a name="resolution"></a>解决方法 
- 为执行不同的触发器时间分离这些管道。
- 创建新的集成运行时，并将这些管道拆分到多个集成运行时。

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>如何按固定间隔监视管道故障

#### <a name="issue"></a>问题
通常需要以5分钟为间隔监视数据工厂管道。 可以使用终结点从数据工厂查询和筛选管道运行。 

#### <a name="recommendation"></a>建议
1. 将 Azure 逻辑应用设置为每隔5分钟查询所有失败的管道。
2. 然后，你可以根据 [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)将事件报告给票证系统。

#### <a name="reference"></a>参考
- [外部-从数据工厂发送通知](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>如何处理管道中的活动级错误和失败

#### <a name="issue"></a>问题
Azure 数据工厂业务流程允许条件逻辑，并使用户能够根据上一活动的结果采取不同的路径。 它允许四个条件路径： "成功时 (默认传递) "、"失败时"、"完成时" 和 "跳过"。 允许使用不同的路径。

Azure 数据工厂定义管道运行成功和失败，如下所示：

- 评估所有叶级活动的结果。 如果跳过了某个叶活动，则会改为评估其父活动。
- 当且仅当所有叶都成功时，管道结果才会成功。

#### <a name="recommendation"></a>建议
- 实现活动级别检查遵循 [如何处理管道故障和错误](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)。
- 使用 Azure 逻辑应用 [按 DataFactory 进行查询]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)，按固定间隔来监视管道。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](/answers/topics/azure-data-factory.html)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)