---
title: 了解迁移到媒体服务 API V3 的好处。
description: 本文列出了从媒体服务 v2 迁移到 v3 的好处。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 1a733492a46b51d471a0efb0c5f316b0a47c8598
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353173"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>步骤 1 - 了解迁移到媒体服务 API V3 的好处

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-1.svg)

## <a name="use-the-latest-api"></a>使用最新的 API

我们建议你立即开始使用 2020-05-01（或更新）版本的 Azure 媒体服务 V3 API 来获得相关优势，因为新的特性、功能和性能优化只在当前的 V3 API 中提供。

你可在门户、最新的 SDK、最新的 CLI 和 REST API 中使用正确的版本字符串更改 API 版本。

媒体服务 V3 中进行了重大改进。  

## <a name="benefits-of-media-services-v3"></a>媒体服务 v3 的优势

| **V3 功能** | **好处** |
| --- | --- |
| **Azure 门户** | |
| Azure 门户更新 | Azure 门户已经过更新，现包含 V3 API 实体管理功能。 它使客户能够使用门户来启动实时传送视频流、提交 V3 转换作业、管理内容保护策略、流式处理终结点、获取 API 访问权限、管理链接的存储帐户，以及执行监视任务。 |
| **帐户和存储** | |
| Azure 基于角色的访问控制 (RBAC) | 客户现可定义其自己的角色，还可控制对媒体服务 ARM API 中每个实体的访问。 这样有助于按 AAD 帐户控制对资源的访问。 |
| 托管标识 | 借助托管标识，开发人员无需在 Azure AD 中提供 Azure 资源的标识即可管理凭据。 请在[此处](../../active-directory/managed-identities-azure-resources/overview.md)查看托管标识的详细信息。 |
| 对专用链接的支持 | 客户将通过其 VNet 上的 PrivateEndpoint 访问 Key Delivery、LiveEvents 和 StreamingEndpoints 的媒体服务终结点。 |
| 对[客户管理的密钥](concept-use-customer-managed-keys-byok.md)或创建自己的密钥 (BYOK) 的支持 | 客户可使用 Azure Key Vault 中的密钥来加密其媒体服务帐户中的数据。 |
| **资产** | |
| 一个资产可以有多个[流式处理定位符](stream-streaming-locators-concept.md)，其中每个定位符都有不同的[动态打包](encode-dynamic-packaging-concept.md)和动态加密设置。 | 每个资产上只能使用 100 个流式处理定位符。 客户可在资产中存储媒体内容的单个副本，而使用不同的流式处理策略或基于目标受众的内容保护策略共享不同的流式处理 URL。
| **作业处理** ||
| V3 为基于文件的作业处理引入了 [转换](transform-jobs-concept.md) 的概念。 | 使用转换可以生成可重用的配置、创建 Azure 资源管理器模板，并隔离多个客户或租户之间的处理设置。 |
| 对于基于文件的作业处理，可使用 HTTP(S) URL 作为输入。 | 无需事先在 Azure 中存储内容，也无需创建输入资产。 |
| **直播活动** ||
| 高级 1080p 直播活动 | 通过新的直播活动 SKU，客户可进行云编码，同时获得高达 1080p 输出分辨率。 |
| 直播活动支持新的[低延迟](live-event-latency-reference.md)实时传送视频流。 | 这样，与未启用此设置时相比，用户可更接近实时地观看直播活动。 |
| 直播活动预览版支持 [动态打包](encode-dynamic-packaging-concept.md) 和动态加密。 | 这样，用户就可使用内容保护功能（预览版）以及 DASH 和 HLS 打包。 |
| 实时输出替换程序 | 与 v2 API 中的节目实体相比，实时输出更易于使用。 |
| 针对直播活动的 RTMP 引入功能经过改进，现支持更多编码器 | 增加了稳定性，并让你能够灵活使用源编码器。 |
| 直播活动可全天候进行流式传输 | 你可托管直播活动，让观众观看更长时间。 |
| 直播活动的直播转录 | 直播转录允许客户在直播事件广播期间实时自动将口头转录为文本。 这极大地提高了直播活动的可访问性。 |
| 直播活动的[待机模式](live-event-outputs-concept.md#standby-mode) | 与正在运行的直播活动相比，待机状态下的直播活动成本更低。 这样，客户就可以更低的成本维护一组几秒内即可启动的直播活动，而不是维护一组正在运行的直播活动。 我们将于 2021 年 2 月在大部分区域推出待机直播活动的更低价，于 2021 年 4 月在其余区域提供该价格。
|**内容保护** ||
| [内容保护](drm-content-key-policy-concept.md) 支持多密钥功能。 | 现在，客户可在其流式处理定位符上使用多个内容加密密钥。 |
| **Monitoring** | |
| 对 [Azure EventGrid](monitoring/reacting-to-media-services-events.md) 通知的支持 | EventGrid 通知功能更加丰富。 增加了通知的类型，扩大了对在你自己的应用程序中接收通知的 SDK 支持的涵盖范围，还提供更多可充当事件处理程序的现有 Azure 服务。 |
| [Azure 门户中的 Azure Monitor 支持和集成](monitoring/monitor-events-portal-how-to.md) | 这使客户能够直观查看媒体服务帐户配额的使用情况、流式处理终结点的实时统计信息，以及直播活动的引入和存档统计信息。 客户现可设置警报，并根据实时指标数据执行必要的操作。 |
