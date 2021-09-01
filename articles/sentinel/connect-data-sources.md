---
title: 将数据源连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Microsoft 365 Defender（原 Microsoft 威胁防护）、Microsoft 365 和 Office 365、Azure AD、ATP 和 Cloud App Security 等数据源连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: c9bce99f26d7d23d2bf655719373f43e78b5a8cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749365"
---
# <a name="connect-data-sources"></a>连接数据源

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

启用 Azure Sentinel 后，首先需要连接数据源。 Azure Sentinel 附带许多适用于 Microsoft 产品的连接器，这些连接器立即可用并且可实现实时集成。 例如，服务到服务连接器包括 Microsoft 365 Defender 连接器和 Microsoft 365 源，例如 Office 365、Azure Active Directory (Azure AD)、Microsoft Defender for Identity 和 Microsoft Cloud App Security。

还可以启用内置的连接器，以拓宽非 Microsoft 产品的安全生态系统。 例如，可以使用 [Syslog](#syslog)、[通用事件格式 (CEF)](#common-event-format-cef) 或 [REST API](#rest-api-integration) 将数据源与 Azure Sentinel 连接。

有关详细信息，请参阅 [Azure Sentinel 合作伙伴数据连接器](partner-data-connectors.md)和 [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)。

## <a name="enable-a-data-connector"></a>启用数据连接器

若要在 Azure Sentinel 中启用数据连接器：

1. 在 Azure Sentinel 左侧导航菜单中，选择“数据连接器”。 “数据连接器”页显示 Azure Sentinel 提供的连接器及其状态的完整列表。 选择要连接的连接器，然后选择“打开连接器页”。 

   ![数据连接器库](./media/collect-data/collect-data-page.png)
   
   有关详细信息，请参阅[服务到服务集成](#service-to-service-integration)和 [Azure Sentinel 合作伙伴数据连接器](partner-data-connectors.md)。
   

1. 在连接器页上，确保已满足所有先决条件，并按照相关说明将数据连接到 Azure Sentinel。 可能需要一段时间才能让日志开始与 Azure Sentinel 保持同步。 在连接后，可以在“收到的数据”图中查看数据摘要，以及数据类型的连接状态。

   ![配置数据连接器](./media/collect-data/opened-connector-page.png)
  
1. 选择“后续步骤”选项卡，以查看 Azure Sentinel 针对特定数据类型提供的内容。

   ![连接器的后续步骤](./media/collect-data/data-insights.png)

<a name="agent-options"></a>
## <a name="data-connection-methods"></a>数据连接方法
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

Azure Sentinel 支持以下数据连接方法：

### <a name="service-to-service-integration"></a>服务到服务集成

Azure Sentinel 使用 Azure 基础为以下 Microsoft 服务和 Amazon Web Services 提供内置的服务到服务支持：

- [Amazon Web Services - CloudTrail](connect-aws.md)
- [Azure Active Directory](connect-azure-active-directory.md)，包括审核日志和登录日志
- [Azure Active Directory 标识保护](connect-azure-ad-identity-protection.md)
- [Azure 活动](connect-azure-activity.md)
- [Azure DDoS 防护](connect-azure-ddos-protection.md)
- Azure 安全中心的 [Azure Defender 警报](connect-azure-security-center.md)
- [Azure Defender for IoT](connect-asc-iot.md)（以前称为适用于 IoT 的 Azure 安全中心）
- [Azure 防火墙](connect-azure-firewall.md)
- [Azure 信息保护](connect-azure-information-protection.md)。 有关详细信息，请参阅[如何修改报告和创建自定义查询](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)。
- [Azure Key Vault](connect-azure-key-vault.md)
- [Azure Kubernetes 服务 (AKS)](connect-azure-kubernetes-service.md)
- [Azure SQL 数据库](connect-azure-sql-logs.md)
- [Azure 存储帐户](connect-azure-storage-account.md)
- [Azure Web 应用程序防火墙 (WAF)](connect-azure-waf.md)（原 Microsoft WAF）
- [域名服务器](connect-dns.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Microsoft 365 Defender](connect-microsoft-365-defender.md)，包括 Microsoft 365 Defender 事件和 Microsoft 365 Defender for Endpoint 的原始数据
- [Microsoft Cloud App Security (MCAS)](connect-cloud-app-security.md)
- [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md)（原 Microsoft Defender 高级威胁防护）
- [Microsoft Defender For Identity](connect-azure-atp.md)（原 Azure 高级威胁防护）
- [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md)（原 Office 365 高级威胁防护）
- [Office 365](connect-office-365.md)，包括 Microsoft Teams
- [安全事件 (Windows)](connect-windows-security-events.md)。 有关详细信息，请参阅[不安全的协议工作簿设置](./get-visibility.md#use-built-in-workbooks)。
- [Windows 防火墙](connect-windows-firewall.md)

### <a name="rest-api-integration"></a>REST API 集成

许多安全技术提供了一组用于检索日志文件的 API，并且某些数据源可以使用这些 API 连接到 Azure Sentinel。

使用 API 的数据连接器或是从提供程序端集成，或是使用 Azure Functions 集成，具体如以下部分所述。

有关这些连接器的完整列表和信息，请参阅 [Azure Sentinel 合作伙伴数据连接器](partner-data-connectors.md)。

#### <a name="rest-api-integration-on-the-provider-side"></a>提供程序端的 REST API 集成

由提供程序构建的 API 集成将与提供程序数据源连接，并使用 [Azure Monitor 数据收集器 API](../azure-monitor/logs/data-collector-api.md) 将数据推送到 Azure Sentinel 自定义数据表。

有关配置这些数据连接器以将日志发送到 Azure Sentinel 的详细信息，请参阅相关的提供程序文档。 

#### <a name="rest-api-integration-using-azure-functions"></a>使用 Azure Functions 进行 REST API 集成

使用 [Azure Functions](../azure-functions/index.yml) 与提供程序 API 连接的集成首先会格式化数据，然后使用 [Azure Monitor 数据收集器 API](../azure-monitor/logs/data-collector-api.md) 将数据发送至 Azure Sentinel 自定义日志表。

若要了解配置这些数据连接器以与提供程序 API 连接和收集 Azure Sentinel 中的日志的详细信息，请执行 Azure Sentinel 中针对每个数据连接器显示的步骤。

使用 Azure Functions 的集成在 Azure Sentinel 数据连接器页也有一个“部署到 Azure”按钮。 对于这些集成，请执行以下操作来简化配置：

1. 选择“部署到 Azure”
1. 输入参数值，以与提供程序 API 连接。
1. 输入 Azure Sentinel 工作区 ID 和密钥以与 Log Analytics 连接，并将数据发送到 Azure Sentinel。

> [!IMPORTANT]
> 使用 Azure Functions 的集成可能会产生额外的数据引入成本，这是因为你在 Azure 租户托管 Azure Functions。 有关详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

### <a name="agent-based-integration"></a>基于代理的集成

Azure Sentinel 可以使用 Syslog 协议通过代理连接到任何执行实时日志流式处理的数据源。 例如，大多数本地数据源通过基于代理的集成进行连接。
以下部分介绍基于 Azure Sentinel 代理的数据连接器的不同类型。 按照每个 Azure Sentinel 数据连接器页中的步骤，使用基于代理的机制配置连接。

有关通过 CEF 或 Syslog 连接到 Azure Sentinel 的防火墙、代理和终结点的完整列表，请参阅 [Azure Sentinel 合作伙伴数据连接器](partner-data-connectors.md)。

#### <a name="syslog"></a>Syslog

可以使用适用于 Linux 的 Log Analytics 代理（以前称为 OMS 代理），将基于 Linux 的、支持 Syslog 的设备中的事件流式传输到 Azure Sentinel。 允许直接在设备上安装 Log Analytics 代理的任何设备均支持 Log Analytics 代理。

设备的内置 Syslog 守护程序会收集指定类型的本地事件，并在本地将其转发到代理，然后代理会将其流式传输到 Log Analytics 工作区。 成功配置后，数据将显示在 Log Analytics Syslog 表中。

根据设备类型，可以直接在设备上安装代理，或在专用的 Linux 日志转发器上安装代理。 Log Analytics 代理通过 UDP 从 Syslog 守护程序接收事件。 如果预期 Linux 计算机需要收集大量的 Syslog 事件，则会通过 TCP 将这些事件从 Syslog 守护程序发送到代理，然后从代理发送到 Log Analytics。

有关详细信息，请参阅[将基于 Syslog 的设备连接到 Azure Sentinel](connect-syslog.md)。 

#### <a name="common-event-format-cef"></a>通用事件格式 (CEF)

日志格式各不相同，但许多源支持基于 CEF 的格式设置。 Azure Sentinel 代理（实际上是 Log Analytics 代理）将 CEF 格式的日志转换为 Log Analytics 可以引入的格式。

对于在 CEF 中发出数据的数据源，请设置 Syslog 代理，然后配置 CEF 数据流。 成功配置后，数据将显示在 CommonSecurityLog 表中。

有关详细信息，请参阅[将基于 CEF 的设备连接到 Azure Sentinel](connect-common-event-format.md)。

#### <a name="custom-logs"></a>自定义日志

某些数据源的日志可用于在 Windows 或 Linux 上作为文件收集。 可以使用 Log Analytics 自定义日志收集代理收集这些日志。

按照每个 Azure Sentinel 数据连接器页中的步骤，使用 Log Analytics 自定义日志收集代理进行连接。 成功配置后，数据将显示在自定义表中。

有关详细信息，请参阅[使用 Log Analytics 代理收集自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)。

## <a name="data-connector-support"></a>数据连接器支持

Microsoft 和其他组织都会开发 Azure Sentinel 连接器。 每个数据连接器都具有以下支持类型之一：

| 支持类型| 说明|
|-------------|------------|
|Microsoft 支持|适用于：<ul><li>数据源的数据连接器，其中 Microsoft 是数据提供程序和创建者。</li><li>适用于非 Microsoft 数据源的部分 Microsoft 开发的数据连接器。</li></ul>Microsoft 根据 [Microsoft Azure 支持计划](https://azure.microsoft.com/support/options/#overview)支持和维护此类别中的数据连接器。<br><br>合作伙伴或社区支持由除 Microsoft 以外的任何一方开发的数据连接器。|
|合作伙伴支持|适用于由 Microsoft 之外的各方开发的数据连接器。<br><br>合作伙伴公司可为这些数据连接器提供支持或维护。 合作伙伴公司可以是独立软件供应商、托管服务提供商 (MSP/MSSP)、系统集成商 (SI) 或在该数据连接器的 Azure Sentinel 页上提供其联系信息的任何组织。<br><br>对于有关合作伙伴支持的数据连接器的任何问题，请联系指定的数据连接器支持联系人。|
|社区支持|适用于由 Microsoft 或合作伙伴开发者开发、但未在 Azure Sentinel 中的指定数据连接器页列出数据连接器支持和维护联系人的数据连接器。<br><br>对于有关这些数据连接器的问题，可以在 [Azure Sentinel GitHub 社区](https://aka.ms/threathunters)[提出问题](https://github.com/Azure/Azure-Sentinel/issues/new/choose)。|

### <a name="find-the-support-contact-for-a-data-connector"></a>查找数据连接器的支持联系人

若要查找数据连接器的支持联系人信息：

1. 在 Azure Sentinel 左侧菜单中，选择“数据连接器”。
   
1. 选择要为其查找支持信息的连接器。
   
1. 查看数据连接器侧面板上的“支持者”字段。
   
   ![Azure Sentinel 中显示数据连接器的“支持者”字段的屏幕截图。](./media/collect-data/connectors.png)
   
   “支持者”字段提供了支持联系人的链接，你可以使用该链接来访问所选数据连接器的支持和维护信息。



## <a name="next-steps"></a>后续步骤

- 若要开始使用 Azure Sentinel，需要订阅 Microsoft Azure。 如果没有订阅，可以注册[免费试用版](https://azure.microsoft.com/free/)。
- 了解如何[将数据载入到 Azure Sentinel](quickstart-onboard.md)，以及[获取数据和潜在威胁的见解](get-visibility.md)。