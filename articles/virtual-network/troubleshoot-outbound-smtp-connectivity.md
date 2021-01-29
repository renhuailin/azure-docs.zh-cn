---
title: 在 Azure 中排查出站 SMTP 连接问题 | Microsoft Docs
description: 了解建议的电子邮件发送方法，以及排查 Azure 中的出站 SMTP 连接问题的方法。
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054846"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>在 Azure 中排查出站 SMTP 连接问题

从2017年11月15日开始，将直接发送到外部域的出站电子邮件消息 (例如， (VM 的虚拟机中的 outlook.com 和 gmail.com) ，) 仅适用于 Azure 中的某些订阅类型。 将阻止使用 TCP 端口 25 的出站 SMTP 连接。 （端口 25 主要用于未经身份验证的电子邮件传递。）

此行为更改仅适用于在 2017 年 11 月 15 日之后创建的订阅和部署。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法

建议使用经过身份验证的 SMTP 中继服务从 Azure VM 或 Azure 应用服务发送电子邮件。  (这些中继服务通常通过 TCP 端口587进行连接，但它们支持其他端口。 ) 这些服务用于维持 IP 或域信誉，以最大程度地降低第三方电子邮件提供商拒绝邮件的可能性。 [SendGrid](https://sendgrid.com/partners/azure/) 就是这样一个 SMTP 中继服务，但还有其他服务。 还可具有本地运行并能使用的安全的 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议

企业协议 Azure 用户仍然无需使用经身份验证的中继便可发送电子邮件。 新的和现有的企业协议用户均可以尝试从 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件。 但是，Azure 平台不会阻止企业协议订阅中针对 VM 的交付尝试。 必须直接与电子邮件提供商接洽，以修复涉及特定提供商的各项邮件发送或垃圾邮件筛选问题。

## <a name="pay-as-you-go"></a>即用即付

如果你在2017年11月15日之前注册，则对于即用即付订阅，你的技术无法进行出站电子邮件传送。 你仍然能够尝试从这些订阅中的 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，而且不存在任何 Azure 平台限制。 同样地，不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件。 用户必须直接与电子邮件提供商接洽，以修复涉及特定提供商的各项邮件发送或垃圾邮件筛选问题。

对于在2017年11月15日之后创建的即用即付订阅，将出现直接从订阅中的 Vm 发送的电子邮件的技术限制。 如果希望能够将来自 Azure VM 的电子邮件直接发送给外部电子邮件提供商（不使用经过身份验证的 SMTP 中继），并且你的帐户具有良好的付款历史记录，那么你可请求删除限制。 可在 Azure 门户中的 Azure 虚拟网络资源的 "**诊断和解决**" 边栏选项卡的 "**连接**" 部分中执行此操作。 如果你的请求被接受，你的订阅将被启用，或者你将收到后续步骤的说明。 

免除即用即付订阅并在 Azure 门户中停止并重新启动 Vm 后，将免除该订阅中的所有 Vm。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

> [!NOTE]
> 如果确定违反了服务条款，Microsoft 将保留吊销这些免除项的权利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、面向学生的 Azure、Visual Studio 和免费试用版

如果你在 2017 年 11 月 15 日之后创建了以下订阅类型之一，则将存在技术限制，它们会阻止从订阅中的 VM 直接发送给电子邮件提供商的电子邮件：
- MSDN
- Azure Pass
- Azure 开放许可
- 教育
- 面向学生的 Azure
- 免费试用版
- 任何 Visual Studio 订阅  

此限制是为了防止滥用邮件。 不接受去除这些限制的请求。

如果你正在使用这些订阅类型，建议使用 SMTP 中继服务（如本文前面部分所述），或者更改订阅类型。

## <a name="cloud-solution-provider"></a>云解决方案提供商

如果使用通过云解决方案提供商的 Azure 资源，则可以在 "诊断和解决" 窗格的 "**诊断和解决**" 窗格的 "**连接**" 部分中，请求删除 Azure 门户中虚拟网络资源的限制。 如果你的请求被接受，你的订阅将被启用，或者你将收到后续步骤的说明。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作伙伴网络、BizSpark Plus 或 Azure 赞助

对于在 2017 年 11 月 15 日之后创建的以下类型的订阅，将存在一些技术限制，它们会阻止订阅中的 VM 直接发送电子邮件：

- Microsoft 合作伙伴网络 (MPN) 
- BizSpark Plus
- Azure 赞助

如果你希望能够直接从 Azure vm 向外部电子邮件提供商发送电子邮件 (无需使用经过身份验证的 SMTP 中继) ，你可以通过使用以下问题类型打开支持案例来发出请求：**技术**  >  **虚拟网络**  >  **连接**  >  **无法发送电子邮件 (SMTP/端口 25)**。 请务必添加详细信息，解释你的部署为什么需要向邮件直接发送给电子邮件提供商，而不使用经过身份验证的中继。 请求将由 Microsoft 自行决定审查和审批。 只有在完成其他反欺诈检查后，才能授予请求。 

在 Azure 门户中豁免订阅并停止再重启 VM 后，该订阅中的所有 VM 在此后都会被豁免。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。 使用此问题类型：**技术**  >  **虚拟网络**  >  **连接**  >  **无法 (SMTP/端口 25) 发送电子邮件**。
