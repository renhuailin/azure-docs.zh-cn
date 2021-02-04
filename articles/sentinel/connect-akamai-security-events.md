---
title: 将 Akamai 安全事件收集器连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Akamai 安全事件连接器将 Akamai 解决方案的安全日志请求到 Azure Sentinel。 查看工作簿中的 Akamai 数据、创建警报并改善调查。
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
ms.openlocfilehash: c833d87b8d85c75c4f050f0130ddfd74342f4c52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566716"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>将 Akamai 安全事件收集器连接到 Azure Sentinel

> [!IMPORTANT]
> Akamai 安全事件连接器目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Akamai Security Events 收集器连接到 Azure Sentinel。 Akamai Security Events 数据连接器可让你轻松地将 Akamai 日志与 Azure Sentinel 连接，以便你可以查看工作簿中的数据，对其进行查询以创建自定义警报，并将其合并以改进调查。 Akamai Security Events 收集器和 Azure Sentinel 之间的集成利用了 CEF 格式的 Syslog、基于 Linux 的日志转发器和 Log Analytics 代理。 它还使用基于 Kusto 函数的自定义的日志分析器。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>将 Akamai 安全事件日志发送到 Azure Sentinel

若要将其日志导入 Azure Sentinel，请配置 Akamai 安全事件收集器，以 CEF 格式将 Syslog 消息发送到基于 Linux 的日志转发服务器 (运行 rsyslog 或 Syslog-ng) 。 此服务器上安装了 Log Analytics 代理，代理将日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Akamai 安全事件 (预览")**，然后单击 "连接器" **页**。

1. 按照 "**配置**" 下的 "**说明**" 选项卡中的说明进行操作：

    1. 小于 **1。Linux Syslog 代理配置** -如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关大小调整信息、更详细的说明和详细说明，请参阅第1步：在 Azure Sentinel 文档中 [部署日志转发器](connect-cef-agent.md) 。

    1. 低于 **2。将 CEF) 日志的常见事件 (格式转发到 Syslog 代理** -按照 Akamai 的说明 [配置 SIEM 集成](https://developer.akamai.com/tools/integrations/siem) 并 [设置 CEF 连接器](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)。 此连接器使用 SIEM OPEN API 以近乎实时的方式从你的 Akamai 解决方案接收安全事件，并将其从 JSON 转换为 CEF 格式。
    
        此配置应包含以下元素：
    
        - 日志目标–日志转发服务器的主机名和/或 IP 地址
        - 协议和端口– TCP 514 (否则，请确保在日志转发服务器上的 syslog 后台程序中进行并行更改) 
        - 日志格式– CEF
        - 日志类型-所有可用

    1. **3。验证连接**-通过复制 "连接器" 页上的命令并在日志转发器上运行，验证数据引入。 有关更详细的说明和说明，请参阅 [第3步：验证](connect-cef-verify.md) Azure Sentinel 文档中的连接。

        可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 使用以下步骤设置要在查询和工作簿中使用的 **AkamaiSIEMEvent** Kusto 函数。

1. 在 Azure Sentinel 导航菜单中，选择 " **日志**"。

1. 复制以下查询并将其粘贴到查询窗口中。
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

1. 单击 " **保存** " 下拉箭头，然后单击 " **保存**"。 在 " **保存** " 面板中，

    1. 在 " **名称**" 下输入 **AkamaiSIEMEvent**。

    1. 在 " **另存为**" 下选择 " **函数**"。

    1. 在 " **函数别名**" 下输入 **AkamaiSIEMEvent**。

    1. 在 " **类别**" 下输入 **函数**。

    1. 单击“保存”。

    函数应用通常需要10到15分钟的时间才能激活。

现在，你已准备好查询 Akamai 数据，方法是 `AkamaiSIEMEvent` 在查询窗口的顶部行输入。

有关更多查询示例，请参阅连接器页中的 " **后续步骤** " 选项卡。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Akamai 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
