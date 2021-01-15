---
title: 对 Azure 数据工厂中的管道业务流程和触发器进行故障排除
description: 使用不同方法排查 Azure 数据工厂中的管道触发器问题。
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0ceee3c65e8c4df5d843bb441fb6426a0f4eb696
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220246"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>对 Azure 数据工厂中的管道业务流程和触发器进行故障排除

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，假设有一个管道在凌晨8:00 点、9:00 AM 和 10:00 AM 运行。 在这种情况下，有三个单独的管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是一个全局唯一标识符 (GUID) ，用于定义特定的管道运行。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 您可以手动运行管道，也可以通过使用触发器来运行管道。 有关详细信息，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md) 。

## <a name="common-issues-causes-and-solutions"></a>常见问题、原因和解决方案

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 应用管道在专用终结点连接时引发错误
 
你的数据工厂和 Azure function app 在专用终结点上运行。 正在尝试运行与 function app 交互的管道。 你已尝试三种不同的方法，但一个返回错误 "错误的请求"，另两个方法返回 "103 错误禁止"。

**原因**：数据工厂当前不支持 function apps 的专用终结点连接器。 Azure Functions 拒绝调用，因为它已配置为仅允许来自专用链接的连接。

**解决方法**：创建 **PrivateLinkService** 终结点并提供函数应用的 DNS。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>管道运行已取消，但监视器仍显示进度状态

取消管道运行时，管道监视通常仍显示进度状态。 出现这种情况的原因是浏览器缓存出现问题。 您还可能没有正确的监视筛选器。

**解决方法**：刷新浏览器，并应用正确的监视筛选器。
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>复制管道时出现 "DelimitedTextMoreColumnsThanDefined" 错误
 
如果要复制的文件夹包含具有不同架构的文件，如列数、不同分隔符、引号字符设置或某些数据问题，数据工厂管道可能会引发此错误：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**解决方法**：在创建复制活动时选择 " **二进制复制** " 选项。 这样一来，就可以将数据从一个 data lake 大容量复制或迁移到另一个 data lake，数据工厂不会打开要读取架构的文件。 相反，数据工厂会将每个文件视为二进制文件并将其复制到其他位置。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>达到 integration runtime 的容量限制时，管道运行失败

错误消息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**原因**：已达到集成运行时的容量限制。 使用相同的集成运行时，您可能会运行大量数据流。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) 。

**解决方法**：
 
- 在不同的触发器时间运行管道。
- 创建新的集成运行时，并将管道拆分到多个集成运行时。

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>管道中存在活动级别的错误和失败

Azure 数据工厂业务流程允许条件逻辑，并使用户能够根据前一个活动的结果采用不同的路径。 它允许四个条件路径： **成功** 时 (默认传递) ，在 **失败** 时、 **完成** 时以及 **跳过**。 

Azure 数据工厂评估所有叶级活动的结果。 仅当所有叶都成功时，管道结果才会成功。 如果跳过了某个叶活动，则会改为评估其父活动。 

**分辨率**

1. 按照 [如何处理管道故障和错误](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)来实现活动级别检查。
1. 使用 Azure 逻辑应用 [通过工厂查询按](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)固定间隔来监视管道。

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>按固定间隔监视管道故障

可能需要按间隔5分钟监视失败的数据工厂管道。 可以使用终结点从数据工厂查询和筛选管道运行。 

将 Azure 逻辑应用设置为每隔5分钟查询所有失败的管道，如 [工厂查询](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)中所述。 然后，你可以将事件报告给我们的票证系统。

有关详细信息，请参阅 [从数据工厂发送通知，第2部分](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](/answers/topics/azure-data-factory.html)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
