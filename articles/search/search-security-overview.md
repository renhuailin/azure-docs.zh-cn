---
title: 安全概述
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索中用于保护终结点、内容和操作的安全功能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 954d08fa163b481393df28ae22016859badea694
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537300"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure 认知搜索的安全性概述

本文介绍 Azure 认知搜索中保护内容和操作的安全功能。

对于向搜索服务发出的入站请求，安全措施的进展是保护搜索服务终结点：从请求上的 API 密钥到防火墙中的入站规则，再到从公共 internet 完全防护服务的专用终结点。

对于对其他服务发出的出站请求，主要请求由从外部源读取内容的索引器发出。 可以提供连接字符串的凭据。 或者，你可以设置一个托管标识，使其在访问 Azure 存储、Azure SQL、Cosmos DB 或其他 Azure 数据源的数据时搜索受信任的服务。 托管标识用于替代连接上的凭据或访问密钥。 有关此功能的详细信息，请参阅 [使用托管标识连接到数据源](search-howto-managed-identities-data-sources.md)。

向外部服务写入操作很少：搜索服务会写入日志文件，并在创建知识存储、保留缓存的根据和保持调试会话时写入 Azure 存储。 其他服务到服务呼叫（例如认知服务）在内部网络上进行。

观看此快速进度的视频，大致了解安全体系结构和每个功能类别。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>网络安全

<a name="service-access-and-authentication"></a>

入站安全性功能通过不断提升的安全性和复杂性级别来保护搜索服务终结点。 首先，所有请求都需要 API 密钥才能进行经过身份验证的访问。 其次，你可以选择设置防火墙规则，以限制对特定 IP 地址的访问。 为了提供高级保护，第三个选项是启用 Azure 专用链接来保护服务终结点不受所有 Internet 流量的影响。

### <a name="public-access-using-api-keys"></a>使用 API 密钥进行公共访问

默认情况下，将使用基于密钥的身份验证（用于对搜索服务终结点进行管理员或查询访问）通过公有云来访问搜索服务。 提交有效密钥被视为请求源自受信任实体的证明。 下一节介绍了基于密钥的身份验证。

### <a name="configure-ip-firewalls"></a>配置 IP 防火墙

为了进一步控制对搜索服务的访问，可以创建入站防火墙规则，以允许访问特定 IP 地址或某个范围的 IP 地址。 所有客户端连接必须通过允许的 IP 地址建立，否则连接将被拒绝。

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="ip 限制访问的示例体系结构示意图":::

可以使用门户[配置入站访问](service-configure-firewall.md)。

或者，可以使用管理 REST API。 从 API 版本2020-03-13 开始，使用 [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) 参数，你可以通过标识你想要授予对搜索服务的访问权限的 IP 地址，限制对服务的访问。

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>通过专用终结点进行网络隔离 (没有 Internet 流量) 

你可以建立 Azure 认知搜索 [专用终结点](../private-link/private-endpoint-overview.md) ，以允许 [虚拟网络](../virtual-network/virtual-networks-overview.md) 上的客户端通过 [专用链接](../private-link/private-link-overview.md)安全访问搜索索引中的数据。

专用终结点使用虚拟网络地址空间中的 IP 地址来连接到搜索服务。 客户端与搜索服务之间的网络流量将穿过虚拟网络以及 Microsoft 主干网络上的专用链接，不会从公共 Internet 公开。 VNET 允许在资源之间通过本地网络和 Internet 进行安全通信。

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="专用终结点访问的示例体系结构关系图":::

虽然这种解决方案是最安全的，但使用其他服务是一项额外的成本，因此请确保你清楚地了解其中的好处。 有关成本的详细信息，请参阅 [定价页](https://azure.microsoft.com/pricing/details/private-link/)。 有关这些组件如何协同工作的详细信息，请观看本文顶部的视频。 专用终结点选项的范围从5:48 开始到视频。 有关如何设置终结点的说明，请参阅 [为 Azure 认知搜索创建专用终结点](service-create-private-endpoint.md)。

## <a name="authentication"></a>身份验证

对于对搜索服务的入站请求，身份验证是通过 [必需的 API 密钥](search-security-api-keys.md) (由随机生成的数字和) 字母组成的字符串，用于证明请求来自可靠来源。 认知搜索当前不支持入站请求 Azure Active Directory 身份验证。

索引器发出的出站请求将由外部服务进行身份验证。 认知搜索中的索引器子服务可成为 Azure 上的可信服务，使用托管标识连接到其他服务。 有关详细信息，请参阅 [使用托管标识设置与数据源的索引器连接](search-howto-managed-identities-data-sources.md)。

## <a name="authorization"></a>授权

认知搜索为内容管理和服务管理提供不同的授权模型。 

### <a name="authorization-for-content-management"></a>内容管理的授权

对内容和与内容相关的操作的授权是写入访问权限，这是通过请求提供的 [API 密钥](search-security-api-keys.md) 授予的。 API 密钥是一种身份验证机制，还会根据 API 密钥的类型授予访问权限。

+ 管理密钥 (允许对搜索服务) 上的 "读取-更新-删除" 操作进行读写访问，该操作是在设置服务时创建的

+ 查询密钥 (允许以只读方式访问索引) 的文档集合，并根据需要创建并为发出查询的客户端应用程序设计

在应用程序代码中，指定终结点和 API 密钥以允许访问内容和选项。 终结点可以是服务本身、索引集合、特定索引、文档集合或特定文档。 当链接在一起时，操作 (例如，创建或更新请求) 并且权限级别 (基于密钥) 的完全或只读权限构成用于保护内容和操作的安全公式。

### <a name="controlling-access-to-indexes"></a>控制对索引的访问权限

在 Azure 认知搜索中，单个索引不是安全对象。 相反，可以根据) 提供的 API 密钥以及操作的上下文，在服务层上确定对索引的访问权限 (读取或写入访问权限。

对于只读访问，你可以使用 [查询密钥](search-security-rbac.md)构造要连接的查询请求，并包括应用使用的特定索引。 在查询请求中，没有联接索引或同时访问多个索引的概念，所有请求都会根据定义以单个索引为目标。 因此，查询请求本身的结构（密钥加上单个目标索引）定义了安全边界。

管理员和开发人员对索引的访问权限没有区别：两者都需要写访问权限才能创建、删除和更新服务管理的对象。 具有服务 [管理密钥](search-security-rbac.md) 的任何人都可以读取、修改或删除同一服务中的任何索引。 为了防止意外删除或恶意删除索引，代码资产的内部源代码管理作为一种补救机制，可以还原意外的索引删除或修改。 Azure 认知搜索在群集中提供故障转移功能来确保可用性，但它不会存储或执行用于创建或加载索引的专属代码。

需要索引级安全边界的多租户解决方案通常包含一个中间层，客户可以使用它来处理索引隔离。 有关多租户用例的详细信息，请参阅[多租户 SaaS 应用程序与 Azure 认知搜索的设计模式](search-modeling-multitenant-saas-applications.md)。

### <a name="controlling-access-to-documents"></a>控制对文档的访问

如果需要对搜索结果进行特定于每个用户的控制，则可以在查询上生成安全筛选器，返回与给定安全标识相关联的文档。 

在概念上等效于 "行级别安全性"，使用映射到 Azure Active Directory 中的实体的预定义角色或角色分配，对索引中的内容的授权不受本机支持。 对于外部系统中的数据（如 Cosmos DB）的任何用户权限，不会通过认知搜索为其编制索引的数据进行传输。

需要 "行级别安全性" 的解决方案的解决方法包括：在数据源中创建表示安全组或用户标识的字段，然后在认知搜索中使用筛选器根据标识有选择地裁剪文档和内容的搜索结果。 下表描述了修整未经授权内容的搜索结果的两种方法。

| 方法 | 说明 |
|----------|-------------|
|[基于标识筛选器的安全修整](search-security-trimming-for-azure-search.md)  | 阐述实现用户标识访问控制的基本工作流。 该工作流包括将安全标识符添加到索引，然后解释如何针对该字段进行筛选，以修整受禁内容的结果。 |
|[Azure Active Directory 标识的安全修整](search-security-trimming-for-azure-search-with-aad.md)  | 本文展开了前面一篇文章，其中提供了从 Azure Active Directory (Azure AD) （Azure 云平台中的一个 [免费服务](https://azure.microsoft.com/free/) ）检索标识的步骤。 |

### <a name="authorization-for-service-management"></a>用于服务管理的授权

服务管理操作通过 [AZURE RBAC)  (azure 基于角色的访问控制 ](../role-based-access-control/overview.md)获得授权。 Azure RBAC 是一种基于 [azure 资源管理器](../azure-resource-manager/management/overview.md) 构建的授权系统，用于预配 azure 资源。 

在 Azure 认知搜索中，资源管理器用于创建或删除服务、管理 API 密钥以及缩放服务。 因此，Azure 角色分配将确定谁可以执行这些任务，无论他们使用的是 [门户](search-manage.md)、 [POWERSHELL](search-manage-powershell.md)还是 [管理 REST api](/rest/api/searchmanagement/search-howto-management-rest-api)。

为搜索服务管理定义了[三个基本角色](search-security-rbac.md#management-tasks-by-role)。 可使用任何受支持的方法（ (门户、PowerShell 等）进行角色分配，) 并在服务范围内进行。 所有者和参与者角色可以执行各种管理功能。 你可以将 "读者" 角色分配给仅查看基本信息的用户。

> [!Note]
> 利用 Azure 范围内的机制，可以锁定订阅或资源，以防止具备管理员权限的用户意外或在未经授权的情况下删除搜索服务。 有关详细信息，请参阅[锁定资源以防止意外删除](../azure-resource-manager/management/lock-resources.md)。

## <a name="threat-protection"></a>威胁防护

可以通过查询独占访问搜索服务中的内容。 如果搜索服务是查询攻击的目标，则系统将在系统接近高峰容量时删除查询。 

对不同 API 的限制行为各不相同。 系统会根据服务的负载动态限制查询 API（搜索/建议/自动完成）和索引 API。 索引 Api 和服务操作 API 具有静态请求速率限制。 可以在 [限制限制](search-limits-quotas-capacity.md#throttling-limits)中查看静态速率请求限制。 若要深入了解限制行为，请参阅 [监视查询请求](search-monitor-queries.md)。

<a name="encryption"></a>

## <a name="data-protection"></a>数据保护

在存储层，数据加密是为保存到磁盘的所有服务托管内容（包括索引、同义词映射以及索引器、数据源和技能集的定义）而构建的。 （可选）可以添加客户托管的密钥 (CMK) 用于索引内容的补充加密。 对于8月 1 2020 日后创建的服务，CMK 加密扩展到临时磁盘上的数据，以获取索引内容的完整 "双加密"。

### <a name="data-in-transit"></a>传输中的数据

在 Azure 认知搜索中，加密从连接和传输开始，一直延伸到磁盘上存储的内容。 对于公共 Internet 上的搜索服务，Azure 认知搜索会侦听 HTTPS 端口 443。 客户端到服务的所有连接都使用 TLS 1.2 加密。 不支持更早的版本（1.0 或 1.1）。

### <a name="encrypted-data-at-rest"></a>静态加密数据

对于由搜索服务在内部处理的数据，下表介绍了 [数据加密模型](../security/fundamentals/encryption-models.md)。 某些功能（如知识库、增量扩充和基于索引器的索引）、对其他 Azure 服务中的数据结构进行读取或写入。 这些服务独立于 Azure 认知搜索提供自己的加密支持级别。

| 建模 | 密钥&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 要求&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 限制 | 适用于 |
|------------------|-------|-------------|--------------|------------|
| 服务器端加密 | Microsoft 管理的密钥 | 无（内置） | 无，可在所有层级、所有区域使用，适用于 2018 年 1 月 24 日后创建的内容。 | 内容（索引和同义词映射）和定义（索引器、数据源、技能组） |
| 服务器端加密 | 客户管理的密钥 | Azure Key Vault | 可在计费层级、所有区域使用，适用于 2019 年 1 月后创建的内容。 | 数据磁盘上的内容（索引和同义词映射） |
| 服务器端双重加密 | 客户管理的密钥 | Azure Key Vault | 可在计费层级、所选区域使用，适用于 2020 年 8 月 1 日后的搜索服务。 | 数据磁盘和临时磁盘上的内容（索引和同义词映射） |

### <a name="service-managed-keys"></a>服务托管的密钥

服务托管加密是一种基于 [Azure 存储服务加密](../storage/common/storage-service-encryption.md)的 Microsoft 内部操作，使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)进行。 加密自动对所有索引进行，包括对未完全加密的索引（在 2018 年 1 月前创建）的增量更新。

### <a name="customer-managed-keys-cmk"></a>客户管理的密钥 (CMK)

客户管理的密钥需要额外的计费服务 Azure Key Vault，它与 Azure 认知搜索可以位于不同区域，但需位于同一订阅下。 启用 CMK 加密会增大索引大小，降低查询性能。 根据迄今为止的观察结果，查询时间预期会增加 30%-60%，不过，实际性能根据索引定义和查询类型而有所不同。 由于这种性能影响，我们建议仅对真正需要此功能的索引启用此功能。 有关详细信息，请参阅[在 Azure 认知搜索中配置客户管理的加密密钥](search-security-manage-encryption-keys.md)。

<a name="double-encryption"></a>

### <a name="double-encryption"></a>双重加密

在 Azure 认知搜索中，双重加密是 CMK 的扩展。 它被理解为两折 (一次通过 CMK 的加密，并再次通过服务托管的密钥) 和全面的作用域，其中包含写入到数据磁盘的长期存储以及写入临时磁盘的短期存储。 CMK 在 2020 年 8 月 1 日之前和之后的差异在于，该日期之后，它将对临时磁盘上的静态数据进行额外加密，这也是使 CMK 成为 Azure 认知搜索中双重加密功能的原因。

对于 8 月 1 日后在这些区域中创建的新服务，目前可以使用双重加密：

+ 美国西部 2
+ 美国东部
+ 美国中南部
+ US Gov 弗吉尼亚州
+ US Gov 亚利桑那州

## <a name="security-management"></a>安全管理

### <a name="api-keys"></a>API 密钥

依靠基于 API 密钥的身份验证，你应该制定计划，以便定期按 Azure 安全最佳实践重新生成管理密钥。 每个搜索服务最多有两个管理密钥。 有关保护和管理 API 密钥的详细信息，请参阅 [创建和管理 api 密钥](search-security-api-keys.md)。

#### <a name="activity-and-diagnostic-logs"></a>活动日志和诊断日志

认知搜索不记录用户标识，因此你无法参考日志来了解有关特定用户的信息。 但是，该服务会记录 "创建-读取-更新-删除" 操作，你可以与其他日志关联，以了解特定操作的代理。

使用 Azure 中的警报和日志记录基础结构，可以选择与预期的工作负荷偏离的查询量峰值或其他操作。 有关设置日志的详细信息，请参阅 [收集和分析日志数据](search-monitor-logs.md) 和 [监视查询请求](search-monitor-queries.md)。

### <a name="certifications-and-compliance"></a>认证和合规性

Azure 认知搜索参与定期审核，并已针对公有云和 Azure 政府的多种全球、区域和行业特定标准进行了认证。 有关完整列表，请从官方审核报告页下载 [ **Microsoft Azure 符合性产品** 白皮书](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)。

为实现符合性，你可以使用 [Azure 策略](../governance/policy/overview.md) 来实现 [azure 安全基准](../security/benchmarks/introduction.md)的高安全性最佳实践。 Azure 安全基准是安全建议的集合，整理到安全控件中，这些安全控制映射到你应对服务和数据的威胁应采取的关键操作。 目前有11个安全控制，其中包括 [网络安全性](../security/benchmarks/security-control-network-security.md)、 [日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)以及 [数据保护](../security/benchmarks/security-control-data-protection.md) 。

Azure Policy 是 Azure 中内置的一项功能，可帮助你管理对多个标准（包括 Azure 安全基准的标准）的符合性。 对于众所周知的基准，Azure Policy 提供了内置定义，这些定义既提供了标准，又提供了解决非符合性的可操作响应。

对于 Azure 认知搜索，当前有一个内置定义。 它用于诊断日志记录。 使用此内置功能，你可以分配一个策略来标识缺少诊断日志记录的任何搜索服务，然后将其启用。 有关详细信息，请参阅 azure [认知搜索的 Azure 策略合规性控制](security-controls-policy.md)。

## <a name="see-also"></a>另请参阅

+ [Azure 安全基础知识](../security/fundamentals/index.yml)
+ [Azure 安全性](https://azure.microsoft.com/overview/security)
+ [Azure 安全中心](../security-center/index.yml)