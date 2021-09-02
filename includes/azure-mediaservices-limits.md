---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: 0c60b111483d896ad2beb008d165b9fb84160b35
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835556"
---
>[!NOTE]
>对于不固定的资源，请开具支持票证，要求增加配额。 请不要尝试通过创建更多 Azure 媒体服务帐户来提高配额限制。

| 资源 | 限制 | 
| --- | --- | 
| 单个订阅中的 Azure 媒体服务帐户数 | 25（固定） | 
| 每个媒体服务帐户的作业数 | 50,000<sup>2</sup> |
| 每个作业的链接任务数 | 30（固定） |
| 每个媒体服务帐户的资产数 | 1,000,000|
| 每个任务的资产数 | 50 |
| 每个作业的资产数 | 100 |
| 一次与一个资产关联的唯一定位符数 | 5<sup>4</sup> |
| 每个媒体服务帐户的直播频道数 |5|
| 每个频道的停止状态节目数 |50|
| 每个频道的运行状态节目数 |3|
| 每个媒体服务帐户的流式处理端点数（已停止或正在运行）|2|
| 每个流式处理终结点的流式处理单位数 |10 |
| 存储帐户 | 100<sup>5</sup>（已修复） |
| 策略 | 1,000,000<sup>6</sup> |
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。<sup>7</sup> |

<sup>1</sup>如果更改类型（例如，从 S1 更改为 S2），则会重置最大预留单位限制。

<sup>2</sup>这个数字包括已排队的、已完成的、活动的和已取消的作业。 但不包括已删除的作业。 可以使用 **IJob.Delete** 或 **DELETE** HTTP 请求删除旧作业。

自 2017 年 4 月 1 日起，将自动删除帐户中超过 90 天的所有作业记录及其相关任务记录。 即使记录总数低于最大配额，也会执行自动删除。 若要存档作业和任务信息，可使用[使用媒体服务 .NET SDK 管理资产](../articles/media-services/previous/media-services-dotnet-manage-entities.md)中所述的代码。

<sup>3</sup>发出列出作业实体的请求时，每个请求最多返回 1,000 个作业。 如果需要跟踪所有已提交的作业，可以使用 top 或 skip，如 [OData 系统查询选项](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中所述。

<sup>4</sup>定位符不用于管理按用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。 有关详细信息，请参阅[使用 Azure 媒体服务保护内容](../articles/media-services/latest/drm-content-protection-concept.md)。

<sup>5</sup>存储帐户必须来自同一 Azure 订阅。

<sup>6</sup>不同媒体服务策略的策略数限为 1,000,000 个。 例如，对于定位器策略或 ContentKeyAuthorizationPolicy。 

>[!NOTE]
> 如果始终使用相同的天数和访问权限，则使用相同的策略 ID。 有关信息和示例，请参阅[使用媒体服务 .NET SDK 管理资产](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>在 Azure Blob 存储中，单个 Blob 目前支持的最大大小为 5 TB。 媒体服务会根据服务使用的 VM 大小应用其他限制。 大小限制适用于你上传的文件，也适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

下表显示了媒体保留单位（S1、S2 和 S3）的限制。 如果源文件大于表中定义的限制，则编码作业将失败。 编码持续时间较长的 4K 分辨率源时，需要使用 S3 媒体保留单位才能达到所需的性能。 如果 S3 媒体保留单位上的 4K 内容大于 260-GB 限制，请开具支持票证。

|媒体保留单位类型    |最大输入大小 (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
