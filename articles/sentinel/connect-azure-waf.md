---
title: 将 Azure Web 应用程序防火墙 (WAF) 数据连接到 Azure Sentinel
description: 了解如何将 Azure WAF 数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: cd1ec5d11083c21511b39ba08fa2bfcf159bfc54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779775"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>从 Azure Web 应用程序防火墙 (WAF) 连接数据

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 Azure Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受代码注入、跨站点脚本等常见攻击和威胁的影响。 Azure WAF 可以部署在 [Azure 应用程序网关](../web-application-firewall/ag/ag-overview.md)服务、[Azure Front Door](../web-application-firewall/afds/afds-overview.md) 服务上，并且可以通过 [Azure 内容分发网络 (CDN)](../web-application-firewall/cdn/cdn-overview.md) WAF 策略（后者目前为公共预览版）部署。
可以将 Azure WAF 日志连接到 Azure Sentinel，以便查看工作簿中的日志数据，使用这些数据创建自定义警报，并通过整合这些数据来改进调查。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

## <a name="connect-to-azure-waf"></a>连接到 Azure WAF

### <a name="instructions-tab"></a>说明选项卡

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure Web 应用程序防火墙 (WAF)”，然后在预览窗格中选择“打开连接器页面”。

1. 选择要连接其日志的 WAF 资源类型的链接（打开应用程序网关资源 >、打开 Front Door 资源 > 或打开内容分发网络 (CDN) WAF 策略 >），出现在资源列表的屏幕中后，从列表中选择 WAF 资源。

    1. 从 WAF 资源的导航菜单中选择“诊断设置”。

    1. 选择列表底部的“+ 添加诊断设置”。

    1. 在“诊断设置”屏幕的“诊断设置名称”字段中键入一个名称。

    1. 单击“发送到 Log Analytics”复选框。 其下将显示两个新字段。 选择相关“订阅”和“Log Analytics 工作区”（Azure Sentinel 驻留的位置）。

    1. 单击要引入其日志的规则类型的复选框。 针对每种资源类型的建议：

        | 资源 | 要选择用于引入的日志 |
        |----------|------------------------------|
        | 应用程序网关 | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF 策略      | WebApplicationFirewallLogs |
        |

    1. 选择“保存”。

### <a name="next-steps-tab"></a>后续步骤选项卡

- 若要深入了解 Azure WAF 日志数据，请参阅与“Azure Web 应用程序防火墙”数据连接器捆绑在一起的建议工作簿、查询示例和分析规则模板。

- 若要在“日志”中查询 Azure WAF 数据，请在查询窗口中键入“AzureDiagnostics”。

> [!NOTE]
>
> 对于这个特定的数据连接器，只有当数据在过去两周内的某个时间点被引入的情况下，连接状态指示器（数据连接器库中的色条和数据类型名称旁边的连接图标）才会显示为“已连接”（绿色）。 一旦两周过去但没有数据引入，连接器将显示为已断开。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Azure WAF 日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。