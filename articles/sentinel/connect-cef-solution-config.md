---
title: 配置安全解决方案以将 CEF 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何配置安全解决方案以将 CEF 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 36c832e198d7b6e9a6c3f6ddc19ad87c87917f38
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541268"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>步骤2：配置安全解决方案以发送 CEF 消息

在此步骤中，你将对安全解决方案本身执行必要的配置更改，以将日志发送到 CEF 代理。

## <a name="configure-a-solution-with-a-connector"></a>使用连接器配置解决方案

如果你的安全解决方案已经具有现有连接器，请使用以下连接器特定的说明：

- [AI Vectra 检测](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint 产品](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [走向微 TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX 网络取证平台](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>配置任何其他解决方案

如果特定安全解决方案没有连接器，请使用以下一般说明将日志转发到 CEF 代理。

1. 请参阅特定配置一文，了解有关如何配置解决方案以发送 CEF 消息的步骤。 如果未列出你的解决方案，则在设备上需要设置这些值，以便设备根据 Log Analytics 代理将必要的格式的必要日志发送到 Azure Sentinel Syslog 代理。 你可以在设备中修改这些参数，只要你还在 Azure Sentinel 代理上的 Syslog 后台程序中修改它们即可。
    - 协议 = TCP
    - 端口 = 514
    - Format = CEF
    - IP 地址-请确保将 CEF 消息发送到专用于此目的的虚拟机的 IP 地址。

   此解决方案支持 Syslog RFC 3164 或 RFC 5424。

1. 若要在 Log Analytics 中搜索 CEF 事件，请 `CommonSecurityLog` 在 "查询" 窗口中输入。

1. 继续执行步骤3： [验证连接性](connect-cef-verify.md)。

> [!NOTE]
> **更改 TimeGenerated 字段的源**
>
> - 默认情况下，Log Analytics 代理使用代理从 Syslog 守护程序收到事件的时间填充架构中的 *TimeGenerated* 字段。 因此，不会在 Azure Sentinel 中记录源系统上生成事件的时间。
>
> - 但可以运行以下命令，该命令将下载并运行 `TimeGenerated.py` 脚本。 此脚本将 Log Analytics 代理配置为在其源系统上用事件的原始时间（而不是代理接收到的时间）填充 *TimeGenerated* 字段。
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。