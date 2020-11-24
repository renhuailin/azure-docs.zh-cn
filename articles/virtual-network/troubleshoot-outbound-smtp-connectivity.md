---
title: 在 Azure 中排查出站 SMTP 连接问题 | Microsoft Docs
description: 了解发送电子邮件的建议方法，以及如何对 Azure 中的出站 SMTP 连接问题进行故障排除。
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 063ebc40fd845fe6300b008e7ca048357a2fce49
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95806674"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>在 Azure 中排查出站 SMTP 连接问题

从 2017 年 11 月 15 日开始，从虚拟机 (VM) 直接发送到外部域（例如 outlook.com 和 gmail.com）的出站电子邮件将仅适用于 Microsoft Azure 中的某些订阅类型。 将阻止使用 TCP 端口 25 的出站 SMTP 连接。 （端口 25 主要用于未经身份验证的电子邮件传递。）

此行为更改仅适用于 2017 年 11 月 15 日及以后的新订阅和新部署。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法

建议使用经过身份验证的 SMTP 中继服务（通常通过 TCP 端口 587 或 443 连接，但也支持其他端口），从 Azure VM 或 Azure 应用服务发送电子邮件。 这些服务用于维护 IP 或域信誉，以尽量降低第三方电子邮件提供商拒绝邮件的可能性。 此类 SMTP 中继服务包括但不限于 [SendGrid](https://sendgrid.com/partners/azure/)。 也可以使用本地运行的安全的 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议

企业协议 Azure 用户仍然无需使用经身份验证的中继便可发送电子邮件。 新的和现有的企业协议用户均可以尝试从 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 虽然不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件，但对于企业协议订阅中的 VM，Azure 平台不会阻止发送尝试。 必须直接与电子邮件提供商接洽，以修复涉及特定提供商的所有邮件发送或垃圾邮件筛选问题。

## <a name="pay-as-you-go"></a>即用即付

如果在2017年11月15日之前注册了 "即用即付" 订阅，则无需更改技术功能即可进行出站电子邮件传递。 将能够继续尝试从这些订阅中的 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 同样，不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件，用户需要直接与电子邮件提供商协作，以处理涉及特定提供商的所有邮件发送或垃圾邮件筛选问题。

对于在2017年11月15日之后创建的即用即付订阅，将出现直接从这些订阅中的 Vm 发送的电子邮件的技术限制。 如果你希望能够直接从 Azure Vm 向外部电子邮件提供商发送电子邮件 (未使用经过身份验证的 SMTP 中继) ，你可以在 Azure 门户中发出请求以删除 Azure 虚拟网络资源的 "**诊断和解决**" 边栏选项卡的 "**连接**" 部分中的限制。 如果有资格，将启用你的订阅，或者你将收到有关后续步骤的说明。

免除即用即付订阅并在 Azure 门户中停止并启动 Vm 后，将免除该订阅中的所有 Vm。 豁免仅适用于请求的订阅，并且仅适用于直接路由到 internet 的 VM 通信。

> [!NOTE]
> 如果确定发生违反服务条款的情况，Microsoft 保留撤销此豁免的权利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、面向学生的 Azure、Vistual Studio 和免费试用版

如果你在2017年11月15日之后创建了 MSDN、Azure Pass、Azure 开放许可、教育版、Azure 学生版、免费试用版或任何 Visual Studio 订阅，你将有技术限制，这些限制会阻止从这些订阅中的 Vm 直接发送到电子邮件提供商的电子邮件。 此限制是为了防止滥用邮件。 不接受去除此限制的请求。

如果使用这些订阅类型，建议使用 SMTP 中继服务，如本文前面部分所述，或者更改订阅类型。

## <a name="cloud-service-provider-csp"></a>云服务提供商 (CSP)

如果通过 CSP 使用 Azure 资源，则可以在 Azure 门户中发出请求以删除虚拟网络资源的 "诊断和解决" 边栏选项卡的 "连接" 部分中的限制。 如果有资格，将启用你的订阅，或者你将收到有关后续步骤的说明。

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作伙伴网络 (MPN) 、BizSpark Plus 或 Azure 赞助

对于在2017年11月15日之后创建的 Microsoft 合作伙伴网络 (MPN) 、BizSpark Plus 或 Azure 赞助订阅，将会阻止从这些订阅中的 Vm 直接发送的电子邮件。 如果你希望能够直接从 Azure vm 向外部电子邮件提供商发送电子邮件 (未使用经过身份验证的 SMTP 中继) ，你可以通过使用以下问题类型打开支持案例来发出请求：**技术**  >  **虚拟网络**  >  **连接**  >  **无法发送电子邮件 (SMTP/端口 25)**。 请确保添加详细信息，解释为什么要直接向电子邮件提供商发送邮件而不是使用经身份验证的中继。 请求将由 Microsoft 自行批准和批准。 只有在完成了其他反欺诈检查后，才可授予请求。 

免除即用即付订阅并在 Azure 门户中停止并启动 Vm 后，将免除该订阅中的所有 Vm。 豁免仅适用于请求的订阅，并且仅适用于直接路由到 internet 的 VM 通信。

## <a name="restrictions-and-limitations"></a>限制和局限

- 不支持通过 Azure PaaS 服务（如 [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/) ）路由端口25的流量。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请使用以下问题类型 [联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。“订阅管理”问题类型：**请求启用端口 25 电子邮件流**。
