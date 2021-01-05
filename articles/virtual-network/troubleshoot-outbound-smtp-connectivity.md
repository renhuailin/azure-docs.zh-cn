---
title: 在 Azure 中排查出站 SMTP 连接问题 | Microsoft Docs
description: 了解发送电子邮件的建议方法，以及如何解决 Azure 中的出站 SMTP 连接问题。
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
ms.openlocfilehash: e1e7e78cab1f3a240737b5e25e0dff28c420add8
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883106"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>排查 Azure 中的出站 SMTP 连接问题

从2017年11月15日开始，将直接发送到外部域的出站电子邮件消息 (例如， (VM 的虚拟机中的 outlook.com 和 gmail.com) ，) 仅适用于 Azure 中的某些订阅类型。 将阻止使用 TCP 端口 25 的出站 SMTP 连接。  (端口25主要用于未经身份验证的电子邮件传递。 ) 

此行为更改仅适用于2017年11月15日之后创建的订阅和部署。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法

建议使用经过身份验证的 SMTP 中继服务从 Azure Vm 或 Azure App Service 发送电子邮件。  (这些中继服务通常通过 TCP 端口587或443进行连接，但它们支持其他端口。 ) 这些服务用于维持 IP 或域信誉，以最大程度地降低第三方电子邮件提供商拒绝邮件的可能性。 [SendGrid](https://sendgrid.com/partners/azure/) 是一种这样的 SMTP 中继服务，但还有其他。 你可能还具有可在本地运行的安全 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议

企业协议 Azure 用户仍然无需使用经身份验证的中继便可发送电子邮件。 新的和现有的企业协议用户均可以尝试从 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 不能保证电子邮件提供商将接受来自任何给定用户的传入电子邮件。 但是，Azure 平台不会阻止企业协议订阅内的 Vm 的传递尝试。 您必须直接处理电子邮件提供程序，以解决任何涉及特定提供程序的邮件传递或垃圾邮件筛选问题。

## <a name="pay-as-you-go"></a>即用即付

如果你在2017年11月15日之前注册，则对于即用即付订阅，你的技术无法进行出站电子邮件传送。 你仍可以从这些订阅中的 Azure Vm 直接尝试将出站电子邮件传递到外部电子邮件提供商，而不会受到 Azure 平台的任何限制。 同样，也不能保证电子邮件提供商将接受来自任何给定用户的传入电子邮件。 用户必须直接处理电子邮件提供程序，以解决任何涉及特定提供程序的邮件传递或垃圾邮件筛选问题。

对于在2017年11月15日之后创建的即用即付订阅，将出现直接从订阅中的 Vm 发送的电子邮件的技术限制。 如果你希望能够直接从 Azure Vm 向外部电子邮件提供商发送电子邮件 (而无需使用经过身份验证的 SMTP 中继) 并且具有良好的付款历史记录，则可以请求删除限制。 可在 Azure 门户中的 Azure 虚拟网络资源的 "**诊断和解决**" 边栏选项卡的 "**连接**" 部分中执行此操作。 如果接受了你的请求，则将启用你的订阅，否则你将收到有关后续步骤的说明。 

免除即用即付订阅并在 Azure 门户中停止并重新启动 Vm 后，将免除该订阅中的所有 Vm。 豁免仅适用于请求的订阅，并且仅适用于直接路由到 internet 的 VM 通信。

> [!NOTE]
> 如果确定违反了服务条款，Microsoft 将保留吊销这些免除项的权利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、面向学生的 Azure、Visual Studio 和免费试用版

如果你在2017年11月15日之后创建了以下订阅类型之一，则你将有技术限制，这些限制会阻止从订阅内的 Vm 直接发送到电子邮件提供商的电子邮件：
- MSDN
- Azure Pass
- Azure 开放许可
- 教育
- 面向学生的 Azure
- 免费试用版
- 任何 Visual Studio 订阅  

限制是为了防止滥用。 不会授予删除这些限制的请求。

如果你使用这些订阅类型，则建议你使用 SMTP 中继服务（如本文前面所述），或更改你的订阅类型。

## <a name="cloud-solution-provider"></a>云解决方案提供商

如果使用通过云解决方案提供商的 Azure 资源，则可以在 "诊断和解决" 窗格的 "**诊断和解决**" 窗格的 "**连接**" 部分中，请求删除 Azure 门户中虚拟网络资源的限制。 如果接受了你的请求，则将启用你的订阅，否则你将收到有关后续步骤的说明。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作伙伴网络、BizSpark Plus 或 Azure 赞助

对于在2017年11月15日之后创建的以下类型的订阅，将出现直接从订阅中的 Vm 发送的电子邮件的技术限制：

- Microsoft 合作伙伴网络 (MPN) 
- BizSpark Plus
- Azure 赞助

如果你希望能够直接从 Azure vm 向外部电子邮件提供商发送电子邮件 (无需使用经过身份验证的 SMTP 中继) ，你可以通过使用以下问题类型打开支持案例来发出请求：**技术**  >  **虚拟网络**  >  **连接**  >  **无法发送电子邮件 (SMTP/端口 25)**。 请确保添加有关部署为何必须将邮件直接发送到邮件提供商，而不是使用经过身份验证的中继的详细信息。 请求将由 Microsoft 自行批准和批准。 只有在完成附加的 antifraud 检查后，才会授予请求。 

在免除了某个订阅并在 Azure 门户中停止并重新启动了 Vm 后，将免除该订阅中的所有 Vm。 豁免仅适用于请求的订阅，并且仅适用于直接路由到 internet 的 VM 通信。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。 使用此问题类型：**技术**  >  **虚拟网络**  >  **连接**  >  **无法 (SMTP/端口 25) 发送电子邮件**。
