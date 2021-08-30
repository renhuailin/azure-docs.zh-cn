---
title: 安全概述
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索中用于保护终结点、内容和操作的安全功能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2021
ms.custom: references_regions
ms.openlocfilehash: c3e6112c1bee8e42f411eaa8d12d873db2657142
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114726453"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure 认知搜索的安全性概述

本文介绍 Azure 认知搜索中保护数据和操作的安全功能。

## <a name="network-traffic-patterns"></a>网络流量模式

搜索服务托管在 Azure 上，通常通过公共网络连接进行访问。 了解服务的访问模式可帮助设计安全策略，有效阻止对可搜索内容的未经授权的访问。

认知搜索具有三种基本的网络流量模式：

+ 客户端向搜索服务发出的入站请求（主要模式）
+ 搜索服务向 Azure 和其他平台上的其他服务发出的出站请求
+ 通过安全的 Microsoft 主干网络的内部服务到服务请求

入站请求包括创建对象、加载数据和查询等。 对于针对数据和操作的入站访问，可以实现一系列安全措施，并从请求中的 API 密钥开始。 然后，可以补充 IP 防火墙中的入站规则，也可以创建全面保护服务免受公共 Internet 服务影响的专用终结点。

出站请求可以包括读取和写入操作。 出站调用的主要代理是索引器和组成技能集。 对于索引器，读取操作包括[文档破解](search-indexer-overview.md#document-cracking)和数据引入。 在创建知识存储、持久缓存扩充和持久调试会话时，索引器也可写入 Azure 存储。 最后，技能集也可以包括运行外部代码的自定义技能，例如在 Azure Functions 或 Web 应用中。

内部请求包括通过 Azure Active Directory 执行的诊断日志记录、加密、身份验证和授权等任务的服务到服务调用、专用终结点连接，以及向认知服务发出的内置技能请求。

## <a name="network-security"></a>网络安全

<a name="service-access-and-authentication"></a>

入站安全性功能通过不断提升的安全性和复杂性级别来保护搜索服务终结点。 认知搜索使用[基于密钥的身份验证](search-security-api-keys.md)，其中所有请求都需要 API 密钥才能进行经过身份验证的访问。

也可以选择通过设置仅限特定 IP 地址可进行访问的防火墙规则来实现更多控制层。 如需高级保护，可以启用 Azure 专用链接来保护服务终结点，使其免受所有 Internet 流量的影响。

### <a name="connect-over-the-public-internet"></a>通过公共 Internet 进行连接

默认情况下，将使用基于密钥的身份验证（用于对搜索服务终结点进行管理员或查询访问）通过公有云来访问搜索服务终结点。 需要密钥。 提交有效密钥被视为请求源自受信任实体的证明。 下一部分介绍基于密钥的身份验证。 如果没有 API 密钥，请求将返回 401 和 404 响应。

### <a name="connect-through-ip-firewalls"></a>通过 IP 防火墙进行连接

为了进一步控制对搜索服务的访问，可以创建入站防火墙规则，以允许访问特定 IP 地址或某个范围的 IP 地址。 所有客户端连接必须通过允许的 IP 地址建立，否则连接将被拒绝。

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="IP 受限访问的示例体系结构图":::

可以使用门户[配置入站访问](service-configure-firewall.md)。

或者，可以使用管理 REST API。 从 API 版本 2020-03-13 开始，利用 [IpRule](/rest/api/searchmanagement/2020-08-01/services/create-or-update#iprule) 参数，可以通过标识希望向其授予搜索服务访问权限的 IP 地址（单个或在某个范围内）来限制对服务的访问。

### <a name="connect-to-a-private-endpoint-network-isolation-no-internet-traffic"></a>连接到专用终结点（网络隔离，无 Internet 流量）

可以为 Azure 认知搜索建立[专用终结点](../private-link/private-endpoint-overview.md)，允许[虚拟网络](../virtual-network/virtual-networks-overview.md)上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问搜索索引中的数据。

专用终结点将虚拟网络地址空间中的 IP 地址用于到搜索服务的连接。 客户端与搜索服务之间的网络流量将穿过虚拟网络以及 Microsoft 主干网络上的专用链接，不会从公共 Internet 公开。 VNET 允许在资源间通过本地网络以及 Internet 进行安全通信。

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="专用终结点访问的示例体系结构关系图":::

虽然这种解决方案是最安全的，但使用其他服务会增加成本，因此，在开始之前请先确保清楚地了解其中的益处。 有关费用的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/private-link/)。 若要详细了解这些组件如何协同工作，请观看本文顶部的视频。 涉及专用终结点方案的内容从该视频中 5:48 开始。 有关如何设置终结点的说明，请参阅[为 Azure 认知搜索创建专用终结点](service-create-private-endpoint.md)。

### <a name="outbound-connections-to-external-services"></a>与外部服务的出站连接

索引器和技能集均为可建立外部连接的对象。 你将使用这些机制之一提供连接信息作为对象定义的一部分。

+ 连接字符串中的凭据

+ 连接字符串中的托管标识

  可以设置托管标识，以使搜索在访问 Azure 存储、Azure SQL、Cosmos DB 或其他 Azure 数据源中的数据时成为受信任的服务。 托管标识用于替代连接上的凭据或访问密钥。 有关此功能的详细信息，请参阅[使用托管标识连接到数据源](search-howto-managed-identities-data-sources.md)。

## <a name="authentication"></a>身份验证

对于向搜索服务发出的入站请求，通过证明该请求来自可靠来源的 [API 密钥](search-security-api-keys.md)（由随机生成的数字和字母组成的字符串）进行身份验证。 另外，还提供了对 Azure Active Directory 身份验证和基于角色的授权的新支持，[目前以预览版提供](search-security-rbac.md)。

由索引器发出的出站请求受外部服务提供的身份验证方式影响。 认知搜索中的索引器子服务可以成为 Azure 上的可信服务，使用托管标识连接到其他服务。 有关详细信息，请参阅[使用托管标识设置到数据源的索引器连接](search-howto-managed-identities-data-sources.md)。

## <a name="authorization"></a>授权

认知搜索为内容管理和服务管理提供不同的授权模型。 

### <a name="authorization-for-content-management"></a>内容管理的授权

对内容的授权，以及与内容相关的操作，也是写入访问权限，通过根据请求提供的 [API 密钥](search-security-api-keys.md)来授予。 API 密钥不仅是身份验证机制，而且还会根据 API 密钥的类型来授予访问权限。

+ 管理员密钥（允许针对搜索服务的“创建-读取-更新-删除”操作进行“读取-写入”访问），在预配服务时创建

+ 查询密钥（允许对索引的文档集合进行只读访问），根据需要创建，并且是为发出查询的客户端应用程序设计的

在应用程序代码中，请指定终结点和 API 密钥，以允许访问内容和选项。 终结点可能是服务本身、索引集合、特定索引、文档集合或特定文档。 在链接到一起时，终结点、操作（例如，创建或更新请求）和权限级别（基于密钥的全部或只读权限）构成保护内容和操作的安全方案。

> [!NOTE]
> 使用 Azure 基于角色的访问控制 (RBAC) 提供的数据平面操作授权目前以预览版提供。 如果想要[使用角色分配而不是 API 密钥](search-security-rbac.md)，则可以使用此预览版功能。

### <a name="controlling-access-to-indexes"></a>控制对索引的访问

在 Azure 认知搜索中，单个索引不是安全对象。 对索引的访问权限是在服务层连同操作上下文一起确定的（根据提供哪个 API 密钥来确定读取还是写入访问）。

对于只读访问，可以构建要使用[查询密钥](search-security-api-keys.md)连接的查询请求，并包括由应用使用的特定索引。 在查询请求中，没有联接索引或同时访问多个索引的概念，所有请求都会根据定义以单个索引为目标。 因此，查询请求本身的结构（密钥加上单个目标索引）定义了安全边界。

管理员和开发人员对索引的访问权限没有区别：两者都需要写访问权限才能创建、删除和更新服务管理的对象。 拥有服务的[管理员密钥](search-security-api-keys.md)的任何人都可以读取、修改或删除同一服务中的任何索引。 为了防止意外删除或恶意删除索引，代码资产的内部源代码管理作为一种补救机制，可以还原意外的索引删除或修改。 Azure 认知搜索在群集中提供故障转移功能来确保可用性，但它不会存储或执行用于创建或加载索引的专属代码。

需要索引级安全边界的多租户解决方案通常包含一个中间层，客户可以使用它来处理索引隔离。 有关多租户用例的详细信息，请参阅[多租户 SaaS 应用程序与 Azure 认知搜索的设计模式](search-modeling-multitenant-saas-applications.md)。

### <a name="controlling-access-to-documents"></a>控制对文档的访问

如果需要对搜索结果进行精细的、基于每个用户的控制，可以在查询中生成安全筛选器，返回与给定安全标识关联的文档。 

在概念上等同于“行级别安全性”，在本机不支持使用映射到 Azure Active Directory 中实体的预定义角色或角色分配来对索引中的内容授权。 Cosmos DB 等外部系统中数据的任何用户权限不会在通过认知搜索为这些数据编制索引时与数据一起传输。

对于需要“行级别安全性”的解决方案，可以采用这样的解决方法：在数据源中创建表示安全组或用户标识的字段，然后在认知搜索中使用筛选器根据标识有选择地裁剪文档和内容的搜索结果。 下表描述了修整未经授权内容的搜索结果的两种方法。

| 方法 | 说明 |
|----------|-------------|
|[基于标识筛选器的安全修整](search-security-trimming-for-azure-search.md)  | 阐述实现用户标识访问控制的基本工作流。 该工作流包括将安全标识符添加到索引，然后解释如何针对该字段进行筛选，以修整受禁内容的结果。 |
|[Azure Active Directory 标识的安全修整](search-security-trimming-for-azure-search-with-aad.md)  | 此文延伸了前一篇文章的内容，提供了有关从 Azure Active Directory（Azure AD，Azure 云平台中的一个[免费服务](https://azure.microsoft.com/free/)）检索标识的步骤。 |

### <a name="authorization-for-service-management"></a>服务管理的授权

服务管理操作通过 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 获得授权。 Azure RBAC 是基于 [Azure 资源管理器](../azure-resource-manager/management/overview.md)构建的授权系统，用于预配 Azure 资源。 

在 Azure 认知搜索中，资源管理器用于创建或删除服务，管理 API 密钥，以及缩放服务。 因此，Azure 角色分配将确定哪些用户可以执行那些任务，而不考虑他们是使用[门户](search-manage.md)、[PowerShell](search-manage-powershell.md) 还是[管理 REST API](/rest/api/searchmanagement)。

为搜索服务管理定义了[三个基本角色](search-security-rbac.md)。 可以使用任何受支持的方法（门户、PowerShell，等等）来完成角色分配，并且角色分配是在服务范围内执行的。 所有者和参与者角色可执行许多管理功能。 你可以将读取者角色分配给只查看基本信息的用户。

> [!Note]
> 利用 Azure 范围内的机制，可以锁定订阅或资源，以防止具备管理员权限的用户意外或在未经授权的情况下删除搜索服务。 有关详细信息，请参阅[锁定资源以防止意外删除](../azure-resource-manager/management/lock-resources.md)。

<a name="encryption"></a>

## <a name="data-protection"></a>数据保护

在存储层，为所有保存到磁盘的服务托管内容（包括索引、同义词映射，以及索引器、数据源和技能组的定义）内置了数据加密。 （可选）你可以添加客户管理的密钥 (CMK) 用于已编制索引内容的补充加密。 对于 2020 年 8 月 1 日后创建的服务，CMK 加密延伸到临时磁盘上的数据，以对索引内容进行完全“双重加密”。

### <a name="data-in-transit"></a>传输中的数据

在 Azure 认知搜索中，加密从连接和传输开始，一直延伸到磁盘上存储的内容。 对于公共 Internet 上的搜索服务，Azure 认知搜索会侦听 HTTPS 端口 443。 客户端到服务的所有连接都使用 TLS 1.2 加密。 不支持更早的版本（1.0 或 1.1）。

### <a name="encrypted-data-at-rest"></a>加密的静态数据

对于由搜索服务在内部处理的数据，下表介绍了[数据加密模型](../security/fundamentals/encryption-models.md)。 有些功能（如知识库、增量扩充和基于索引器的索引编制）会读取或写入其他 Azure 服务中的数据结构。 这些服务具有它们自己的（与 Azure 认知搜索分开的）加密支持级别。

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

在 Azure 认知搜索中，双重加密是 CMK 的扩展。 这种情况可以理解为双层加密（通过 CMK 加密一次，通过服务托管密钥再次加密），并且范围全面，包含写入数据磁盘的长期存储和写入临时磁盘的短期存储。 双重加密在特定日期之后创建的服务中实现。 有关详细信息，请参阅[双重加密](search-security-manage-encryption-keys.md#double-encryption)。

## <a name="security-management"></a>安全管理

### <a name="api-keys"></a>API 密钥

依靠基于 API 密钥的身份验证表明你应该按照 Azure 安全最佳做法来制定计划，以便定期重新生成管理密钥。 每个搜索服务最多有两个管理密钥。 若要详细了解如何保护和管理 API 密钥，请参阅[创建和管理 API 密钥](search-security-api-keys.md)。

#### <a name="activity-and-diagnostic-logs"></a>活动日志和诊断日志

认知搜索不会记录用户标识，因此，你无法参考日志来了解有关某个特定用户的信息。 但是，该服务会记录“创建-读取-更新-删除”操作，你也许可以将这些操作与其他日志关联，以了解特定操作的代理。

通过使用 Azure 中的警报和日志记录基础结构，可以选择查询量峰值或其他偏离预期工作负载的操作。 若要详细了解如何设置日志，请参阅[收集和分析日志数据](search-monitor-logs.md)和[监视查询请求](search-monitor-queries.md)。

### <a name="certifications-and-compliance"></a>认证和合规性

Azure 认知搜索参与定期审核，并且已经在公有云和 Azure 政府两方面都获得了多项全球性、区域性和特定行业性标准的认证。 如需完整列表，请从官方审核报告页下载[ Microsoft Azure 符合性产品/服务白皮书](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)。

为实现符合性，可以使用 [Azure Policy](../governance/policy/overview.md) 来实现 [Azure 安全基准](../security/benchmarks/introduction.md)的高安全性最佳做法。 Azure 安全基准是一批编辑到安全控制中的安全建议，这些安全控制映射到为缓解服务和数据受到的威胁而应采取的关键操作。 目前有 11 个安全控制，其中包括[网络安全](../security/benchmarks/security-control-network-security.md)、[日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)和[数据保护](../security/benchmarks/security-control-data-protection.md)等。

Azure Policy 是 Azure 中内置的一项功能，可帮助你管理对多个标准（包括 Azure 安全基准的标准）的符合性。 对于众所周知的基准，Azure Policy 提供了内置定义，这些定义既提供了标准，又提供了解决非符合性的可操作响应。

对于 Azure 认知搜索，当前有一个内置定义。 它用于诊断日志记录。 使用此内置功能，你可以分配一个策略来标识缺少诊断日志记录的任何搜索服务，然后将其启用。 有关详细信息，请参阅 [Azure 认知搜索的 Azure Policy 法规符合性控制](security-controls-policy.md)。

## <a name="watch-this-video"></a>观看此视频

请观看这段快节奏的视频，以便大致了解安全体系结构和每个功能类别。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="see-also"></a>另请参阅

+ [Azure 安全基础知识](../security/fundamentals/index.yml)
+ [Azure 安全性](https://azure.microsoft.com/overview/security)
+ [Azure 安全中心](../security-center/index.yml)
