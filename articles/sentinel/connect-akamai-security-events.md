---
title: 将 Akamai 安全事件收集器连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Akamai 安全事件连接器将 Akamai 解决方案的安全日志拉取到 Azure Sentinel 中。 在工作簿中查看 Akamai 数据，创建警报，并改进调查。
services: sentinel
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: df17c8abca5333f23c26ab63479f398ba766aed6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253934"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>将 Akamai 安全事件收集器连接到 Azure Sentinel

> [!IMPORTANT]
> Akamai 安全事件连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Akamai 安全事件收集器连接到 Azure Sentinel。 使用 Akamai 安全事件数据连接器，可以轻松地将 Akamai 日志与 Azure Sentinel 连接，这样，你就可以在工作簿中查看数据，可以查询这些数据以创建自定义警报，并且可以整合这些数据来改进调查。 Akamai 安全事件收集器和 Azure Sentinel 之间的集成使用了 CEF 格式的 Syslog、基于 Linux 的日志转发器以及 Log Analytics 代理。 它还使用了基于 Kusto 函数的定制日志分析程序。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>将 Akamai 安全事件日志发送到 Azure Sentinel

若要将其日志拉取到 Azure Sentinel 中，请将 Akamai 安全事件收集器配置为将 CEF 格式的 Syslog 消息发送到基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng）。 此服务器上将会安装 Log Analytics 代理，该代理会将这些日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Akamai 安全事件(预览版)”，然后选择“打开连接器页”  。

1. 按照“配置”下的“说明”选项卡中的说明进行操作 ：

    1. 在“1. Linux Syslog 代理配置”下 — 如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关调整大小的信息、更多详细说明以及深入介绍，请参阅 Azure Sentinel 文档中的[步骤 1：部署日志转发器](connect-cef-agent.md)。

    1. 在“2. 将通用事件格式 (CEF) 日志转发到 Syslog 代理”下 - 按照 Akamai 的说明来[配置 SIEM 集成](https://developer.akamai.com/tools/integrations/siem)并[设置 CEF 连接器](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)。 此连接器使用 SIEM OPEN API 以接近实时的方式从 Akamai 解决方案接收安全事件，并将这些事件从 JSON 转换为 CEF 格式。
    
        此配置应该包含以下元素：
    
        - 日志目标 – 日志转发服务器的主机名和/或 IP 地址
        - 协议和端口 - TCP 514（如果使用建议的其他协议和端口，请确保在日志转发服务器上的 syslog 守护程序中进行并行更改）
        - 日志格式 – CEF
        - 日志类型 – 所有可用类型

    1. 在“3. 验证连接”下 — 通过复制连接器页上的命令并在日志转发器上运行以验证数据引入。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 3：验证连接性](connect-cef-verify.md)。

        可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分下的“日志”中 。

此数据连接器必须依赖于基于 Kusto 函数的分析程序才能按预期方式工作。 请使用以下步骤来设置要在查询和工作簿中使用的 AkamaiSIEMEvent Kusto 函数。

1. 从 Azure Sentinel 导航菜单中选择“日志”。

1. 复制以下查询并将其粘贴到查询窗口。
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. 单击“保存”下拉菜单，然后单击“保存” 。 在“保存”面板中，

    1. 在“名称”下输入“AkamaiSIEMEvent” 。

    1. 在“另存为”下选择“函数” 。

    1. 在“函数别名”下，输入“AkamaiSIEMEvent” 。

    1. 在“类别”下输入“函数” 。

    1. 单击“保存”。

    函数应用通常需要 10 到 15 分钟的时间才能激活。

现在可以在查询窗口的顶行中输入 `AkamaiSIEMEvent` 来查询 Akamai 数据了。

有关更多查询示例，请参阅连接器页中的“后续步骤”选项卡。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Akamai 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。