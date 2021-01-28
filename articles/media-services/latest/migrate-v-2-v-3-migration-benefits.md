---
title: 迁移到媒体服务 API V3 的优点
description: 本文列出了从 Media Services v2 迁移到 v3 的好处。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 70f64813546c66c0f9e3533e09de192315f75600
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955065"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>步骤 1-了解迁移到媒体服务 API V3 的优点

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-1.svg)

我们鼓励现在开始使用 Azure 媒体服务 V3 API 版本2020-05-01 来获得好处，因为新功能、功能和性能优化仅在当前 V3 API 中可用。

你可以在门户中更改 API 版本、最新的 Sdk、最新 CLI 和 REST API，并提供正确的版本字符串。

使用 V3 对 Media Services 进行了重大改进。  

## <a name="benefits-of-media-services-v3"></a>媒体服务 v3 的优势

| **V3 功能** | **好处** |
| --- | --- |
| **Azure 门户** | |
| Azure 门户更新 | Azure 门户已更新，以包括对 V3 API 实体的管理。 它使客户能够使用门户来启动实时流式处理，提交 V3 转换作业，管理内容保护策略，流式处理终结点，获取 API 访问权限，管理链接的存储帐户，以及执行监视任务。 |
| **帐户和存储** | |
| Azure 基于角色的访问控制 (RBAC)  | 现在，客户可以定义自己的角色，并控制对媒体服务 ARM API 中每个实体的访问。 这有助于按 AAD 帐户控制对资源的访问。 |
| 托管标识 | 托管标识使开发人员无需通过在 Azure AD 中提供 Azure 资源的标识来管理凭据。 请在 [此处](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)查看有关托管标识的详细信息。 |
| 私有链接支持 | 客户将通过其 VNet 上的 PrivateEndpoint 访问用于密钥传递、LiveEvents 和 Streamingendpoint 的媒体服务终结点。 |
| [客户托管密钥，](concept-use-customer-managed-keys-byok.md) 或自带密钥 (BYOK) 支持 | 客户可以使用 Azure Key Vault 中的密钥来加密其媒体服务帐户中的数据。 |
| **资产** | |
| 资产可以有多个 [流式处理](streaming-locators-concept.md) 定位符，每个具有不同的 [动态打包](dynamic-packaging-overview.md) 和动态加密设置。 | 每个资产上只允许使用100个流式处理定位符。 客户可以在资产中存储媒体内容的单个副本，但使用不同的流策略或基于目标受众的内容保护策略共享不同的流式 Url。
| **作业处理** ||
| V3 介绍了 [转换](transforms-jobs-concept.md)   对基于文件的作业处理的概念。 | 使用转换可以生成可重用的配置、创建 Azure 资源管理器模板，并隔离多个客户或租户之间的处理设置。 |
| 对于基于文件的作业处理，可以使用 HTTP (S) URL 作为输入。 | 不需要将内容存储在 Azure 中，也不需要创建输入资产。 |
| **实时事件** ||
| 高级1080p 实时事件 | 新的实时事件 SKU 允许客户在分辨率最多提供1080p 的情况上获取云编码。 |
| 实时事件的新 [低延迟](live-event-latency.md) 实时流式处理支持。 | 这样，用户就可以查看实时事件，使其比未启用此设置的实时事件更近。 |
| 实时事件预览支持 [动态打包](dynamic-packaging-overview.md)   和动态加密。 | 这会启用对预览、破折号和 HLS 打包的内容保护。 |
| 实时输出替换程序 | 实时输出比 v2 Api 中的程序实体更简单。 |
| 针对实时事件的 RTMP 引入经过改进，支持更多编码器 | 增加了稳定性并提供源编码器灵活性。 |
| 实时事件可以进行24x7 流式处理 | 您可以托管实时活动，并使您的受众保持更长时间。 |
| 实时运行事件 | Live 脚本允许客户在实时事件广播期间实时自动将口头转录为文本。 这极大地提高了实时事件的可访问性。 |
| 实时事件的[待机模式](live-events-outputs-concept.md#standby-mode) | 处于待机状态的实时事件比运行实时事件的成本更低。 这样，客户便可以维护一组实时事件，这些活动可以在几秒内启动，而不是维护一组正在运行的实时事件。 对于大多数区域，备用实时事件的定价将在2021年2月生效，其余部分将于4月2021生效。
|**内容保护** ||
| [内容保护](content-key-policy-concept.md)  支持多关键功能。 | 现在，客户可以在其流式处理定位符上使用多个内容加密密钥。 |
| **Monitoring** | |
| [Azure EventGrid](reacting-to-media-services-events.md) 通知支持 | EventGrid 通知更丰富功能。 有更多类型的通知、更广泛的 SDK 支持，可用于在你自己的应用程序中接收通知，还可以充当事件处理程序。 |
| [Azure 门户中的 Azure Monitor 支持和集成](monitor-events-portal-how-to.md) | 这使客户能够直观显示媒体服务帐户配额使用情况、流式处理终结点的实时统计信息，以及引入和存档实时事件的统计信息。 现在，客户可以根据实时指标数据设置警报并执行必要的操作。 |

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
