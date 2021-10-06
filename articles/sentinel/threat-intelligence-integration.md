---
title: Azure Sentinel 中的威胁情报集成 | Microsoft Docs
description: 了解在 Azure Sentinel 中集成和使用威胁情报源的不同方式。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: ba9d853b6c4bcbe43b81463870a06821dc34d665
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277606"
---
# <a name="threat-intelligence-integration-in-azure-sentinel"></a>Azure Sentinel 中的威胁情报集成

Azure Sentinel 可让你通过几种不同的方式[使用威胁情报源](work-with-threat-indicators.md)来增强安全分析师检测和优先处理已知威胁的能力。 

你可以使用众多可用的集成式[威胁情报平台 (TIP) 产品](connect-threat-intelligence-tip.md)之一，可以[连接到 TAXII 服务器](connect-threat-intelligence-taxii.md)以利用任何与 STIX 兼容的威胁情报源，还可以利用任何可直接与 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) 通信的自定义解决方案。 

此外，还可以从 playbook 连接到威胁情报源，以使用有助于引导完成调查和响应操作的 TI 信息来扩充事件。

> [!TIP]
> 如果同一租户中有多个工作区，例如针对[托管服务提供商 (MSSP)](mssp-protect-intellectual-property.md)的工作区，则仅将威胁指标连接到集中式工作区可能更具成本效益。
>
> 将同一组威胁指标导入到每个单独的工作区时，可以运行跨工作区查询以聚合整个工作区的威胁指标。 在 MSSP 事件检测、调查和搜寻体验中关联这些指标。
>

## <a name="taxii-threat-intelligence-feeds"></a>TAXII 威胁情报源

若要连接到 TAXII 威胁情报源，请结合使用下面链接的每个供应商提供的数据，按说明[将 Azure Sentinel 连接到 STIX/TAXII 威胁情报源](connect-threat-intelligence-taxii.md)。 你可能需要直接联系供应商来获取与连接器配合使用的必要数据。

### <a name="anomali-limo"></a>Anomali Limo

- [了解需要做好哪些准备才能连接到 Anomali Limo 源](https://www.anomali.com/resources/limo)。

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [了解 Cybersixgill 与 Azure Sentinel 的集成 @Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- 若要将 Azure Sentinel 连接到 Cybersixgill TAXII Server 并获取 Darkfeed 访问权限，请[联系 Cybersixgill](mailto://azuresentinel@cybersixgill.com) 获取 API Root、集合 ID、用户名和密码。

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>Financial Services Information Sharing and Analysis Center (FS-ISAC)

- 加入 [FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join) 以获取用于访问此源的凭据。

### <a name="health-intelligence-sharing-community-h-isac"></a>Health Intelligence Sharing Community (H-ISAC)

- [加入 H-ISAC](https://h-isac.org/soltra/) 以获取用于访问此源的凭据。

### <a name="ibm-x-force"></a>IBM X-Force

- [详细了解 IBM X-Force 集成](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [详细了解 IntSights 与 Azure Sentinel 的集成 @IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- 若要将 Azure Sentinel 连接到 IntSights TAXII Server，请在针对要发送到 Azure Sentinel 的数据配置策略后，从 IntSights 门户获取 API Root、集合 ID、用户名和密码。

### <a name="threatconnect"></a>ThreatConnect

- [详细了解 STIX 和 TAXII @ThreatConnect](https://threatconnect.com/stix-taxii/)
- [TAXII 服务文档 @ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/v2/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>集成式威胁情报平台产品

若要连接到威胁情报平台 (TIP) 源，请按说明[将威胁情报平台连接到 Azure Sentinel](connect-threat-intelligence-tip.md)。 这些说明的第二部分要求你在 TIP 解决方案中输入信息。 有关详细信息，请参阅以下链接。

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari Phishing Defense 和 Agari Brand Protection

- 若要连接 [Agari Phishing Defense and Brand Protection](https://agari.com/products/phishing-defense/)，请使用 Azure Sentinel 中的内置 [Agari 数据连接器](./data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview)。

### <a name="anomali-threatstream"></a>Anomali ThreatStream

- 若要下载 [ThreatStream 集成器和扩展](https://www.anomali.com/products/threatstream)以及有关将 ThreatStream 情报连接到 Microsoft Graph 安全性 API 的说明，请参阅 [ThreatStream 下载](https://ui.threatstream.com/downloads)页。

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AT&T Cybersecurity 提供的 AlienVault Open Threat Exchange (OTX)

- [AlienVault OTX](https://otx.alienvault.com/) 利用 Azure 逻辑应用 (playbook) 连接到 Azure Sentinel。 请参阅有关充分利用完整产品/服务所需的[专门说明](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566)。

### <a name="eclecticiq-platform"></a>EclecticIQ 平台

- EclecticIQ 平台与 Azure Sentinel 集成以增强威胁检测、搜寻和响应功能。 详细了解此双向集成的[优势和用例](https://www.eclecticiq.com/resources/azure-sentinel-and-eclecticiq-intelligence-center)。

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB Threat Intelligence and Attribution

- GroupIB 利用 Azure 逻辑应用将 [GroupIB Threat Intelligence and Attribution](https://www.group-ib.com/intelligence-attribution.html) 连接到 Azure Sentinel。 请参阅有关充分利用完整产品/服务所需的[专门说明](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904)。

### <a name="misp-open-source-threat-intelligence-platform"></a>MISP 开源威胁情报平台

- 如需向客户端提供 MISP 实例以将威胁指示器迁移到 Microsoft Graph 安全性 API 的示例脚本，请参阅 [MISP 到 Microsoft Graph 安全性脚本](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。
- [详细了解 MISP 项目](https://www.misp-project.org/)。

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- 若要为 [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) 配置 Azure Sentinel 连接信息，请参阅[使用 MineMeld 将 IOC 发送到 Microsoft Graph 安全性 API](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540) 并跳转到“MineMeld 配置”标题。

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) 利用 Azure 逻辑应用 (playbook) 连接到 Azure Sentinel。 请参阅有关充分利用完整产品/服务所需的[专门说明](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf)。

### <a name="threatconnect-platform"></a>ThreatConnect 平台

- 有关将 [ThreatConnect](https://threatconnect.com/solution/) 连接到 Azure Sentinel 的说明，请参阅 [Microsoft Graph 安全威胁指标集成配置指南](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article)。

### <a name="threatquotient-threat-intelligence-platform"></a>ThreatQuotient Threat Intelligence Platform

- 有关支持信息以及将 [ThreatQuotient TIP](https://www.threatq.com/) 连接到 Azure Sentinel 的说明，请参阅[用于 ThreatQ 集成的 Microsoft Sentinel 连接器](https://azuremarketplace.microsoft.com/marketplace/apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?tab=overview)。

## <a name="incident-enrichment-sources"></a>事件扩充源

除了用于导入威胁指标以外，威胁情报源还可以充当一个源来扩充事件中的信息，并为调查工作提供更多上下文。 以下源可帮助实现此目的，并提供可在[自动事件响应](automate-responses-with-playbooks.md)中使用的逻辑应用 playbook。

### <a name="hyas-insight"></a>HYAS Insight

- 在 Azure Sentinel [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)中查找并启用用于 [HYAS Insight](https://www.hyas.com/hyas-insight) 的事件扩充 playbook。 搜索以“Enrich-Sentinel-Incident-HYAS-Insight-”开头的子文件夹。
- 参阅 HYAS Insight 逻辑应用[连接器文档](/connectors/hyasinsight/)。

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- 在 Azure Sentinel [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)中查找并启用用于 [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) 的事件扩充 playbook。 搜索以“RecordedFuture_”开头的子文件夹。
- 参阅 Recorded Future 逻辑应用[连接器文档](/connectors/recordedfuture/)。

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- 在 Azure Sentinel [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/ReversingLabs/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information)中查找并启用用于 [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) 的事件扩充 playbook。
- 参阅 ReversingLabs Intelligence 逻辑应用[连接器文档](/connectors/reversinglabsintelligence/)。

### <a name="riskiq-passive-total"></a>RiskIQ Passive Total

- 在 Azure Sentinel [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)中查找并启用用于 [RiskIQ Passive Total](https://www.riskiq.com/products/passivetotal/) 的事件扩充 playbook。 搜索以“Enrich-SentinelIncident-RiskIQ-”开头的子文件夹。
- 参阅有关使用 RiskIQ playbook 的[详细信息](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412)。
- 参阅 RiskIQ PassiveTotal 逻辑应用[连接器文档](/connectors/riskiqpassivetotal/)。

### <a name="virus-total"></a>Virus Total

- 在 Azure Sentinel [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)中查找并启用用于 [Virus Total](https://developers.virustotal.com/v3.0/reference) 的事件扩充 playbook。 搜索以“Get-VirusTotal”和“Get-VTURL”开头的子文件夹。
- 参阅 Virus Total 逻辑应用[连接器文档](/connectors/virustotal/)。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将威胁情报连接到 Azure Sentinel。 若要详细了解 Azure Sentinel，请参阅以下文章。

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
