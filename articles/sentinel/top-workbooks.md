---
title: 常用的 Azure Sentinel 工作簿 | Microsoft Docs
description: 了解最常用的工作簿，以便使用常见的内置 Azure Sentinel 资源。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: e2da161d98a7f4bcae0f8da55a1cf7129c7633f9
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183954"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>常用的 Azure Sentinel 工作簿

下表列出了最常用的内置 Azure Sentinel 工作簿。

在左侧的“威胁管理” > “工作簿”下访问 Azure Sentinel 中的工作簿，然后搜索要使用的工作簿 。 有关详细信息，请参阅[可视化和监视数据](monitor-your-data.md)。

> [!TIP]
> 我们建议部署与你所引入的数据关联的任何工作簿。 使用工作簿可基于收集的数据进行更广泛的监视和调查。
>
> 有关详细信息，请参阅[连接数据源](connect-data-sources.md)和[发现和部署 Azure Sentinel 解决方案](sentinel-solutions-deploy.md)。
>

|工作簿名称  |描述  |
|---------|---------|
|**分析效率**     |  提供对分析规则效力的见解，帮助实现更好的 SOC 性能。 <br><br>有关详细信息，请参阅[用于数据驱动的 SOC 的工具包](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)。|
|**Azure 活动**     |     通过分析和关联所有用户操作和事件提供对组织的 Azure 活动的深入见解。 <br><br>有关详细信息，请参阅[通过 Azure 活动日志进行审核](audit-sentinel-data.md#auditing-with-azure-activity-logs)。    |
|**Azure AD 审核日志**     |  使用 Azure Active Directory 审核日志提供对 Azure AD 方案的见解。 <br><br>有关详细信息，请参阅[快速入门：开始使用 Azure Sentinel](get-visibility.md)。     |
|**Azure AD 审核、活动和登录日志**     |   通过一个工作簿，提供对 Azure Active Directory 审核、活动和登录数据的见解。 按位置、设备、失败原因、用户操作等显示活动（例如，登录）。 <br><br> 此工作簿可供安全管理员和 Azure 管理员使用。   |
|**Azure AD 登录日志**     | 使用 Azure AD 登录日志提供对 Azure AD 方案的见解。        |
|**网络安全成熟度模型认证 (CMMC)**     |   提供了一种机制，用于查看与 Microsoft 产品组合（包括 Microsoft 安全产品/服务、Office 365、Teams、Intune、Windows 虚拟桌面等）上 CMMC 控件一致的日志查询。 <br><br>有关详细信息，请参阅[公共预览版中的网络安全成熟度模型认证 (CMMC) 工作簿](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184)。|
|数据收集运行状况监视 / 使用情况监视     |  提供对工作区数据引入状态的见解，例如引入大小、延迟和每个源的日志数。 查看监视器并检测异常，以帮助你确定工作区数据收集运行状况。 <br><br>有关详细信息，请参阅 [通过此 Azure Sentinel 工作簿监视数据连接器的运行状况](monitor-data-connector-health.md)。    |
|**事件分析器**     |  借助事件分析器工作簿，可以浏览和审核 Windows 事件日志，并加快对 Windows 事件日志的分析，其中包括所有事件详细信息和属性(如安全性、应用程序、系统、安装程序、目录服务、DNS 等)。       |
|**Exchange Online**     |通过跟踪和分析所有 Exchange 操作和用户活动，深入了解 Microsoft Exchange online。         |
|**标识和访问**     |   通过包含审核和登录日志的安全日志，提供对 Microsoft 产品使用中的标识和访问操作的见解。     |
|**事件概述**     |   旨在通过提供有关事件的详细信息（包括常规信息、实体数据、会审时间、缓解时间和注释）来帮助进行会审和调查。 <br><br>有关详细信息，请参阅[用于数据驱动的 SOC 的工具包](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)。      |
|<a name="investigation-insights"></a>调查见解     | 为分析师提供对事件、书签和实体数据的见解。 常见查询和详细可视化效果可帮助分析师调查可疑活动。     |
|**Microsoft Cloud App Security - 发现日志**     |   提供有关在组织中使用的云应用的详细信息，以及针对特定用户和应用程序的使用趋势和深化数据的见解。  <br><br>有关详细信息，请参阅[连接来自 Microsoft Cloud App Security 的数据](connect-cloud-app-security.md)。|
|**MITRE ATT&CK 工作簿**     |   提供有关 Azure Sentinel 的 MITRE ATT&CK 覆盖范围的详细信息。      |
|**Office 365**     | 通过跟踪和分析所有操作和活动，提供对 Office 365 的见解。 向下钻取到 SharePoint、OneDrive、Teams 和 Exchange 数据。       |
|**安全警报**     |  提供用于 Azure Sentinel 环境中警报的安全警报仪表板。 <br><br>有关详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。      |
|**安全操作效率**     |  旨在用于安全运营中心 (SOC) 管理器查看有关其团队性能的总体效率指标和度量值。 <br><br>有关详细信息，请参阅[通过事件指标更好地管理 SOC](manage-soc-with-incident-metrics.md)。  |
|**威胁情报**     | 深入了解威胁指示器，包括威胁的类型和严重性、一段时间内的威胁活动以及与其他数据源(包括 Office 365 和防火墙)的关联。  <br><br>有关详细信息，请参阅[了解 Azure Sentinel 中的威胁情报](understand-threat-intelligence.md)。      |
|**零信任 (TIC3.0)**     |  提供对交叉遍历到[受信任 Internet 连接框架](https://www.cisa.gov/trusted-internet-connections)的零信任原则的自动可视化。   <br><br>有关详细信息，请参阅[零信任 (TIC 3.0) 工作簿公告博客](https://techcommunity.microsoft.com/t5/public-sector-blog/announcing-the-azure-sentinel-zero-trust-tic3-0-workbook/ba-p/2313761)。  |