---
title: Policy settings for Web Application Firewall with Azure Front Door（Azure Front Door 的 Web 应用程序防火墙的策略设置）
description: 了解 Web 应用程序防火墙 (WAF)。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75932617"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure Front Door 上的 Web 应用程序防火墙的策略设置

使用 Web 应用程序防火墙 (WAF) 策略，可以通过一组自定义规则和托管规则来控制对 Web 应用程序的访问。 WAF 策略名称必须是唯一的。 如果尝试使用现有名称，将收到验证错误。 有多个策略级别设置适用于本文所述的策略所指定的所有规则。

## <a name="waf-state"></a>WAF 状态

Front Door 的 WAF 策略可处于以下两种状态之一：
- “已启用”：启用策略后，WAF 将主动检查传入的请求，并根据规则定义采取相应的操作
- “已禁用”： - 禁用策略后，WAF 检查将暂停。 传入请求将绕过 WAF，并根据 Front Door 路由将其发送到后端。

## <a name="waf-mode"></a>WAF 模式

WAF 策略可配置为在以下两种模式下运行：

- “检测模式”在检测模式下运行时，WAF 除进行监视并将请求及其匹配的 WAF 规则记录到 WAF 日志中以外，不会执行任何操作。 启用 Front Door 日志记录诊断（使用门户时，可以转到 Azure 门户的“诊断”部分来实现此目的）。

- “阻止模式”如果配置为以阻止模式运行，如果请求与规则匹配，WAF 将执行指定的操作。 任何匹配的请求也会记录在 WAF 日志中。

## <a name="waf-response-for-blocked-requests"></a>已阻止请求的 WAF 响应

默认情况下，当 WAF 由于匹配规则而阻止请求时，它将返回 403 状态代码以及 - “请求被阻止”的消息。 还会返回用于记录的引用字符串。

你可以定义自定义当 WAF 阻止请求时显示的响应状态代码和响应消息。 支持以下自定义状态代码：

- 200    正常
- 403    已禁止
- 405    不允许使用该方法
- 406    不可接受
- 429    请求过多

自定义响应状态代码和响应消息是一种策略级别设置。 配置完成后，所有被阻止的请求均收到相同的自定义响应状态和响应消息。

## <a name="uri-for-redirect-action"></a>重定向操作的 URI

如果为 WAF 策略包含的任何规则选择了“重定向”操作，则需要定义将请求重定向到的 URI。 此重定向 URI 必须是有效的 HTTP(S) 站点，配置完成后，所有匹配带有“重定向”操作规则的请求都将被重定向到指定的站点。


## <a name="next-steps"></a>后续步骤
- 了解如何定义 WAF [自定义响应](waf-front-door-configure-custom-response-code.md)
