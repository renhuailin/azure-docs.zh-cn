---
title: Azure 澳大利亚安全性介绍
description: 最常问及的有关澳大利亚区域以及满足澳大利亚政府政策、法规和立法特定要求的信息。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: ccc71707fa15fd1b09fcdc46f887d9ca343a3d04
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "117029073"
---
# <a name="azure-australia-security-explained"></a>Azure 澳大利亚安全性介绍

本文介绍了澳大利亚政府机构调查、设计和部署到 Microsoft Azure 澳大利亚时的一些常见问题和感兴趣的领域。

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP 和认证云服务列表文档

在将该服务添加到认证云服务列表 (CCSL) 时，澳大利亚网络安全中心 (ACSC) 会为其提供认证书、认证报告和使用者指南。

目前，Microsoft 的 Azure、Office 365 和 Dynamics 365 CRM 已被列入 CCSL。

Microsoft 在 [Microsoft 服务信任门户](https://aka.ms/au-irap)的澳大利亚特定页面上向客户和合作伙伴提供我们的审核、评估和 ACSC 认证文档。

## <a name="dissemination-limiting-markers-and-protected-certification"></a>散播限制标记和“受保护”认证

由 ACSC 编制和发布的 [Information Security Manual (ISM)](https://www.cyber.gov.au/acsc/view-all-content/ism)（信息安全手册 (ISM)）规定了批准系统（包括云服务）供政府组织使用的过程。 ACSC 是澳大利亚信号局 (ASD) 内负责网络安全和云认证的实体。

审批过程分为两步：

1. **安全评估 (IRAP)** ：在此过程中，注册专业人员根据 ISM 中的技术控制评估系统、服务和解决方案，并评估这些控制是否得到有效实施。 该评估还会确定审批机构在颁发运营许可 (ATO) 之前要考虑的所有特定风险。

1. **运营许可**：在此过程中，政府机构的高级官员正式承认并接受系统对其处理、存储和通信的信息的残余风险。 此过程的输入之一是安全评估。

在受保护级别对 Azure 服务的评估表明，存储和处理受保护及以下级别数据所需的安全控制的实施被证实已经到位，并且正在有效运行。

## <a name="australian-data-classification-changes"></a>澳大利亚数据分类更改

2018 年 10 月 1 日，司法部公开宣布了对保护性安全策略框架 (PSPF) 的更改，特别是新的[敏感和机密信息系统](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)。

![修订后的 PSPF 分类](media/pspf-classifications.png)

所有在 PSPF 下运作的澳大利亚机构和组织都受到这些更改的影响。 影响当前 IRAP/CCSL 认证的主要更改是，当前的散播限制标记 (DLM) 已停用。 “官方: 敏感”标记取代了用于保护敏感信息的各种 DLM。 这项更改还引入了三个信息管理标记，可应用于所有敏感度和分类级别的所有官方信息。 “受保护”分类保持不变。

新系统中删除了“未分类”一词，“非官方”一词适用于非政府信息，尽管这类信息不需要正式的标记。

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>为“官方: 敏感”和“受保护”工作负载选择 Azure 区域

Azure“官方: 敏感”和“受保护”认证服务已部署到所有澳大利亚数据中心区域（共四个）：澳大利亚东部、澳大利亚东南部、澳大利亚中部和澳大利亚中部 2。 ACSC 颁发的认证报告建议，将“受保护”数据部署到堪培拉的 Azure 政府区域（如果这些区域有可用的服务）。 有关“受保护”认证 Azure 服务的详细信息，请参阅[服务信任门户上的澳大利亚页面](https://aka.ms/au-irap)。

>[!NOTE]
>Microsoft 建议，应将所有敏感度和分类级别的政府数据部署到澳大利亚中部和澳大利亚中部 2 区域，因为这些区域是专为满足政府需求而设计和运行的。

有关 Azure 澳大利亚区域的特殊性质的详细信息，请参阅 [Azure 澳大利亚中部区域](https://azure.microsoft.com/global-infrastructure/australia/)。

## <a name="how-microsoft-separates-classified-and-official-data"></a>Microsoft 如何区分机密数据和官方数据

Microsoft 在澳大利亚运营 Azure 和 Office 365 时，将所有数据都视为敏感或机密数据，从而将我们的安全控制提高到了一个相当高的水准。

支持 Azure 的基础结构可能会提供多种类别的数据。 因为我们假定客户数据是机密数据，所以适当的控制已然到位。 Microsoft 为我们的服务采用了基准安全状态，该状态符合 PSPF 存储和处理受保护机密信息的要求。

为了向客户保证，Azure 中的一个租户不会受到其他租户的威胁，Microsoft 实施了全面的纵深防御控制。

除了在 Microsoft Azure 平台内实施的功能外，其他客户可配置的控制可以进一步降低风险，例如使用客户管理的密钥加密、嵌套虚拟化和实时管理访问。 在堪培拉的 Azure 政府澳大利亚区域内，制定了仅正式批准澳大利亚和新西兰政府以及国家关键基础设施组织的流程。 此社区云为对共同租户风险敏感的组织提供了额外的保障。

Microsoft Azure 受保护认证报告证实，这些控制对于受保护机密数据的存储和处理及其隔离是有效的。

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>IRAP/CCSL 与州政府和关键基础设施提供商的相关性

许多州政府和关键基础设施提供商将联邦政府要求纳入其安全策略和保障框架。 这些组织还处理“官方”数据、“官方: 敏感”数据和一定数量的“受保护”机密数据，这些数据来自其与联邦政府的互动或是自身有权处理的数据。

澳大利亚政府越来越重视有关非政府数据保护的政策和立法，这些数据从根本上影响了澳大利亚的安全和经济繁荣。 因此，Azure 澳大利亚区域和 CCSL 认证与以下所有行业相关。

![关键基础设施部门](media/nci-sectors.png)

Microsoft 认证表明，Azure 服务经过了对安全保护措施的彻底、严格和正式的评估，并获准处理此类高度敏感的数据。

## <a name="location-and-control-of-microsoft-data-centres"></a>Microsoft 数据中心的位置和控制措施

政府和关键基础设施强制要求明确了解处理其数据的云服务的数据中心位置和所有权。 作为超大规模云提供商，Microsoft 在提供有关这些位置和所有权的广泛信息方面是独一无二的。

Microsoft 的 Azure 澳大利亚区域（澳大利亚中部和澳大利亚中部 2）在 CDC 数据中心的设施内运行。 CDC 数据中心的所有权由澳大利亚控制，其中 48% 的所有权来自 Commonwealth Superannuation Corporation，48% 的所有权来自 Infratil（一家位于新西兰、在澳大利亚和新西兰证券交易所双重上市的长期基础设施资产基金），剩下 4% 来自澳大利亚管理部门。 

CDC 数据中心的管理层与澳大利亚政府签订了合同保证，限制未来所有权和控制权的转移。 通过 Microsoft 与 CDC 数据中心的合作，这种供应链和所有权的透明性符合[政府整体托管战略](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy)的原则和认证主权数据中心的定义。

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>当前 CCSL 认证中包含的 Azure 服务

2017 年 6 月，ACSC 认证了 41 项 Azure 服务，用于存储和处理“未分类: DLM”级别的数据。 2018 年 4 月，其中 24 项服务针对受保护机密数据获得了认证。

在澳大利亚的 Azure 区域中，ACSC 认证的 Azure 服务的可用性如下（以粗体显示的服务是在受保护级别认证的）。

|Azure 澳大利亚中部区域|非区域服务和其他区域|
|---|---|
|API 管理、应用网关、应用程序服务、**自动化**、**Azure 门户**、**备份**、**Batch**、**云服务**、Cosmos DB、事件中心、**ExpressRoute**、HDInsight、**Key Vault**、负载均衡器、Log Analytics、**多重身份验证**、Redis 缓存、**资源管理器**、**服务总线**、**Service Fabric**、**Site Recovery**、**SQL 数据库**、**存储**、流量管理器、**虚拟机**、**虚拟网络**、**VPN 网关**|**Azure Active Directory**、CDN、数据目录、**导入导出**、**信息保护**、**IOT 中心**、机器学习、媒体服务、**通知中心**、Power BI、**计划程序**、**安全中心**、搜索、流分析|
|

Microsoft 发布了 [Microsoft Azure 合规性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf)，其中列出了 Azure 经历的所有全球、政府、行业和区域合规性和评估流程的所有范围内服务，包括 IRAP/CCSL。

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>未列出或评估级别低于所需级别的 Azure 服务

未认证的服务，或者已在“官方: 敏感”级别认证但未在“受保护”级别认证的服务，可以与托管受保护数据的解决方案一起使用或作为其一部分使用，前提是这些服务符合以下条件之一：

- 未存储或处理未加密的受保护数据，或
- 你已完成风险评估并自行批准该服务存储受保护数据。

你可以使用未包含在 CCSL 中的服务来存储和处理“官方”数据，但 ISM 要求你在与云服务提供商签订或续订合同之前，以书面形式通知 ACSC 你正在这样做。

机构为“受保护”工作负载使用的任何服务都必须，根据 ISM 中概述的流程以及 [DTA Secure Cloud Strategy](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)（DTA 安全云策略）中机构管理的 IRAP 评估流程进行安全评估和审批。

![DTA 安全云策略认证流程](media/certification.png)

Microsoft 持续不断地评估我们的服务，确保平台安全可靠，适合澳大利亚政府使用。 如需针对当前不在 CCSL“受保护”级别的服务获得帮助，请联系 Microsoft。

由于 Microsoft 在 CCSL 的“未分类 DLM”和“受保护”分类中认证了一系列服务，因此，ISM 要求我们至少每两年对我们的服务进行一次 IRAP 评估。 Microsoft 会进行年度评估，这也是一个将其他服务纳入考虑范围的机会。

## <a name="certified-protected-gateway-in-azure"></a>Azure 中的认证“受保护”网关

由于网关整合计划允许的 SIG 数量有限制，因此，Microsoft 不运营政府认证的安全 Internet 网关 (SIG)。 但是，SIG 的预期和必要功能可以在 Microsoft Azure 中进行配置。

通过 Azure 服务的“受保护”认证，ACSC 向机构提供了连接 Azure 以及在安全域之间（例如受保护域和 Internet 之间）实施网络分段时的具体建议。 这些建议包括使用网络安全组、防火墙和虚拟专用网络。 ACSC 建议使用虚拟网关设备。 Azure 提供了多种虚拟设备，它们在 ASD 评估产品列表中具有物理等效项，或者已根据共同标准保护配置文件进行评估并在共同标准门户中列出。 这些产品被 ASD 相互承认为共同标准承认协定的签署方。

Microsoft 制定了有关实施基于 Azure 的功能的指南，这些功能提供保护不同安全域之间的边界所需的安全功能，结合使用时等效于经过认证的 SIG。 许多合作伙伴可以协助设计和实施这些功能，并且有许多合作伙伴解决方案可以做到这一点。

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Microsoft 支持人员的安全许可和公民身份

Microsoft 通过经过选拔和培训的安全人员在全球范围内运营我们的服务。 无需陪同即可进入悉尼和墨尔本设施的人员拥有澳大利亚政府基准安全许可。 澳大利亚中部和澳大利亚中部 2 区域内的人员拥有最低限度的“先订立后审议 1”(NV1) 许可（适用于“机密”数据）。 这些许可要求为客户提供了额外的保证，即操作 Azure 的数据中心内的人员非常值得信赖。

Microsoft 有一个零长期访问策略，根据 Azure 基于角色的访问控制 (Azure RBAC)，通过采用 Just In Time 和 Just Enough Administration 的系统授予访问权限。 在绝大多数情况下，我们的管理员在排除故障和维护服务时，不需要对客户数据具有访问权限或特权。 远程执行的任务已高度自动化和脚本化，因此无需直接访问客户数据。

司法部已确认，Microsoft 在 Azure 制定的人员安全策略和程序与 INFOSEC-9 中 PSPF 信息访问条款的意图是一致的。

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>存储国际武器贸易条例 (ITAR) 或出口管理条例 (EAR) 数据

帮助客户履行出口管制数据义务的 Azure 技术控制在 Azure 全球所有区域中都是一样的。 重要的是，对于出口管制数据，并没有正式的评估和认证框架。

对于 Azure 政府和 Office 365 美国国防政府，我们采取了额外的合同和流程措施来支持受出口管制的客户。 这些额外的合同条款以及美国国内对 Azure 区域的有保证的支持和管理不适用于澳大利亚。

这并不意味着澳大利亚的 Azure 不能用于 ITAR/EAR，但你需要清楚地了解出口许可证对你施加的限制。 在使用 Azure 存储该数据之前，还必须实施额外的保护来履行这些义务。 例如，你可能需要：

- 将国籍作为属性构建到 Azure Active Directory 中。
- 使用 Azure 信息保护对数据实施加密规则，并将其限制为仅限美国和出口许可证中包含的任何其他国籍。
- 在将数据存储到 Azure 之前，使用客户密钥或自留密钥（针对 ITAR 数据）在本地加密所有数据。

因为 ITAR 不是正式的认证，所以你需要了解与出口许可证相关的限制有哪些。 然后，可以确定 Azure 中是否有足够的控制措施来满足这些要求。 在这种情况下，需要认真考虑的一个问题是我方工程师的访问权限，他们可能不是出口许可证上批准的国籍。

## <a name="next-steps"></a>后续步骤

 有关与 Azure 澳大利亚的 VPN 连接的符合 ISM 的配置和实施，请参阅 [Azure VPN 网关](vpn-gateway.md)。
