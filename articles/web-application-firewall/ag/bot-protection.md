---
title: 为 Azure Web 应用程序防火墙 (WAF) 配置机器人防护
description: 了解如何为 Azure 应用程序网关上的 Web 应用程序防火墙 (WAF) 配置机器人防护。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 12c9466a4bee6adb77a0e46e63767c2b22fcc01d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729423"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway"></a>为 Azure 应用程序网关上的 Web 应用程序防火墙配置机器人防护

本文介绍如何使用 Azure 门户在 Azure Web 应用程序防火墙 (WAF) 中为应用程序网关配置机器人防护规则。 

可以为 WAF 启用托管机器人防护规则集，以便阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

## <a name="prerequisites"></a>先决条件

按照 [为应用程序网关创建 Web 应用程序防火墙策略](create-waf-policy-ag.md)中所述的说明，为应用程序网关创建基本 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用机器人防护规则集

1. 在之前创建的“基本”策略页的“设置”下，选择“规则”。  

2. 在详细信息页的“管理规则” ****  部分下，从下拉菜单中选中“机器人防护规则”复选框，然后选择“保存”。

> [!div class="mx-imgBorder"]
> ![机器人防护](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>后续步骤

有关自定义规则的详细信息，请参阅 [Azure 应用程序网关上的 Web 应用程序防火墙 v2 的自定义规则](custom-waf-rules-overview.md)。