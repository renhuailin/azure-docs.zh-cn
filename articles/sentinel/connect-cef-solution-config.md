---
title: 配置安全解决方案以将 CEF 数据连接到 Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: 49dab18a0f3b58d82b7de9c393f63428ac0d115b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695188"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>步骤 2：配置安全解决方案以发送 CEF 消息

在此步骤中，你将对安全解决方案本身执行必要的配置更改，以将日志发送到 CEF 代理。

## <a name="configure-a-solution-with-a-connector"></a>使用连接器配置解决方案

如果安全解决方案已具有现有连接器，请使用以下特定于连接器的说明：

- [AI Vectra 检测](connect-ai-vectra-detect.md)
- [Akamai 安全事件](connect-akamai-security-events.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint 产品](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [Imperva WAF 网关](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX Network Forensics Platform](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>配置任何其他解决方案

如果特定安全解决方案不存在连接器，请使用以下一般说明将日志转发到 CEF 代理。

1. 请转到特定的配置文章，以获取有关如何配置解决方案以发送 CEF 消息的步骤。 如果未列出解决方案，则需要在设备上设置这些值，以使设备根据 Log Analytics 代理，以要求的格式将必要的日志发送到 Azure Sentinel Syslog 代理。 你可以在设备中修改这些参数，只要你在 Azure Sentinel 代理上的 Syslog 守护程序中也修改了这些参数。
    - 协议 = TCP
    - 端口 = 514
    - 格式 = CEF
    - IP 地址 - 确保将 CEF 消息发送到专用于此目的的虚拟机 IP 地址。

   此解决方案支持 Syslog RFC 3164 或 RFC 5424。

1. 要在 Log Analytics 中搜索 CEF 事件，请在查询窗口中输入 `CommonSecurityLog`。

1. 继续执行步骤 3：[验证连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。

> [!NOTE]
> 更改 TimeGenerated 字段的源
>
> - 默认情况下，Log Analytics 代理使用其从 Syslog 守护程序接收到事件的时间来填充架构中的 TimeGenerated 字段。 因此，在源系统上生成事件的时间不会记录在 Azure Sentinel 中。
>
> - 但是，可以运行以下命令，该命令将下载并运行 `TimeGenerated.py` 脚本。 该脚本将 Log Analytics 代理配置为在其源系统上使用事件的原始时间（而不是代理接收事件的时间）填充 TimeGenerated 字段。
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。