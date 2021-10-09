---
title: Azure 媒体服务中的配额和限制
description: 本主题介绍 Microsoft Azure 媒体服务中的配额和限制。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: a269f2d93730b6219a2d29d797d94c101b5fdc57
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129052816"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Azure 媒体服务的配额和限制

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文列出了一些最常见的 Microsoft Azure 媒体服务限制（有时也称为配额）。

> [!NOTE]
> 对于不固定的资源，请提交支持票证来要求增加配额。 请不要尝试通过创建更多 Azure 媒体服务帐户来提高配额限制。

## <a name="account-limits"></a>帐户限制

| 资源 | 默认限制 |
| --- | --- |
| 单个订阅中的[媒体服务帐户数](account-move-account-how-to.md) | 100（固定） |

## <a name="asset-limits"></a>资产限制

| 资源 | 默认限制 |
| --- | --- |
| 每个媒体服务帐户的[资产数](assets-concept.md) | 1,000,000|

## <a name="storage-limits"></a>存储限制

| 资源 | 默认限制 | 
| --- | --- | 
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。 <sup>(1)</sup> |
| [存储帐户](storage-account-concept.md) | 100<sup>(2)</sup>（固定） |

<sup>1</sup> 在 Azure Blob 存储中，单个 Blob 目前支持的最大大小为 5 TB。 媒体服务会根据服务使用的 VM 大小应用其他限制。 大小限制适用于你上传的文件，也适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

<sup>2</sup> 存储帐户必须来自同一 Azure 订阅。

## <a name="jobs-encoding--analyzing-limits"></a>作业（编码和分析）限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的[作业数](transform-jobs-concept.md) | 500,000 <sup>(3)</sup>（固定）|
| 每个作业的作业输入数 | 50（固定）|
| 每个作业的作业输出数 | 20（固定） |
| 每个媒体服务帐户的[转换数](transform-jobs-concept.md) | 100（固定）|
| 每个转换中的转换输出数 | 20（固定） |
| 每个作业输入的文件数|10（固定）|

<sup>3</sup> 此数字包括排队的、完成的、活动的和取消的作业。 不包括已删除的作业。 

即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录。 

## <a name="live-streaming-limits"></a>实时传送视频流限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的[直播活动数](live-event-outputs-concept.md) <sup>(4)</sup> |5|
| 每个直播活动的实时输出 |3 <sup>(5)</sup> |
| 最长实时输出持续时间 | [DVR 窗口的大小](live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> 有关直播活动限制的详细信息，请参阅[直播活动类型比较和限制](live-event-types-comparison-reference.md)。 根据所选的流式处理用例和区域数据中心，AMS 能够为每个媒体服务帐户提供 5 个以上的直播活动。 请提交支持请求以增加帐户配额。

<sup>5</sup> 实时输出在创建时启动，在删除后停止。

## <a name="packaging--delivery-limits"></a>打包和传送限制

| 资源 | 默认限制 |
| --- | --- |
| 每个媒体服务帐户的[流式处理终结点数](stream-streaming-endpoint-concept.md)（“已停止的”或“正在运行的”） | 2 |
| 高级流式处理单元 | 10 |
| [动态清单筛选器数](filters-dynamic-manifest-concept.md)|100|
| [流式处理策略](stream-streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| 一次与一个资产关联的唯一[流式处理定位符数](stream-streaming-locators-concept.md) | 100<sup>(7)</sup>（固定） |

<sup>6</sup> 使用自定义[流式处理策略](/rest/api/media/streamingpolicies)时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于流定位器。 不应为每个流式处理定位符创建新的流式处理策略。

<sup>7</sup> 流式处理定位符不适用于管理按用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。

## <a name="protection-limits"></a>保护限制

| 资源 | 默认限制 |
| --- | --- |
| 每个[内容密钥策略](drm-content-key-policy-concept.md)的选项 |30 |
| 每个帐户的媒体服务密钥交付服务上每种 DRM 类型的每月许可证数|1,000,000|

## <a name="support-ticket"></a>支持票证

对于不固定的资源，可以通过开具[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求提高配额。 请在请求中包含有关所需的配额更改、用例方案和所需区域的详细信息。 <br/>**请勿** 尝试通过创建更多 Azure 媒体服务帐户的方式来提高配额限制。

## <a name="next-steps"></a>后续步骤

[概述](media-services-overview.md)
