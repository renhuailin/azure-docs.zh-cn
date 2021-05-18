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
ms.date: 04/28/2021
ms.author: genli
ms.openlocfilehash: 3cc4cb587a7b2d5d06c249cc8f25bc78cdb86739
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165256"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>在 Azure 中排查出站 SMTP 连接问题

仅当在特定订阅类型中部署 VM 时，才有可能直接向 TCP 端口 25 上的外部域（如 outlook.com 和 gmail.com）发送出站电子邮件。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法

建议使用经过身份验证的 SMTP 中继服务从 Azure VM 或 Azure 应用服务发送电子邮件。 （这些中继服务通常通过 TCP 端口 587 连接，但它们支持其他端口。）这些服务用于维护 IP 和域信誉，以尽量降低外部域拒绝邮件或将其放到 SPAM 文件夹的可能性。 [SendGrid](https://sendgrid.com/partners/azure/) 就是这样一个 SMTP 中继服务，但还有其他服务。 你在本地服务器上可能还有一个经过身份验证的 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议

对于在企业协议订阅中部署的 VM，TCP 端口 25 上的出站 SMTP 连接不会受到阻止。 但是，不保证外部域将接受来自 VM 的传入电子邮件。 如果电子邮件被外部域拒绝或筛选，你应联系外部域的电子邮件服务提供商解决问题。 Azure 支持不包含这些问题。

## <a name="pay-as-you-go"></a>即用即付

对于在即用即付订阅中部署的 VM，Azure 平台会阻止 TCP 端口 25 上的出站 SMTP 连接。 如果你的 Azure 订阅良好并具有足够的付款历史记录，则可以删除该阻止。 你可以通过转到 [Azure 门户](https://portal.azure.com)中 Azure 虚拟网络资源的“诊断和解决”边栏选项卡的“无法发送电子邮件(SMTP-端口 25)”部分，来请求删除此限制 。 

在 Azure 门户中使即用即付订阅不受此项阻止并停止再重启 VM 后，该订阅中的所有 VM 在此后都会被豁免。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

> [!NOTE]
> Microsoft 保留在确定发生违反服务条款的情况下撤销这些豁免的权利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、面向学生的 Azure、Visual Studio 和免费试用版

对于在以下订阅类型中部署的 VM，Azure 平台会阻止 TCP 端口 25 上的出站 SMTP 连接：

- MSDN
- Azure Pass
- Azure 开放许可
- 教育
- 面向学生的 Azure
- 免费试用版
- 任何 Visual Studio 订阅  

此限制是为了防止滥用邮件。 不接受去除这些限制的请求。

如果你正在使用这些订阅类型，建议使用经过身份验证的 SMTP 中继服务（如本文前面部分所述），或者更改订阅类型。

## <a name="cloud-solution-provider"></a>云解决方案提供商

对于在云解决方案提供商订阅中部署的 VM，Azure 平台会阻止 TCP 端口 25 上的出站 SMTP 连接。 可以删除这项阻止。 若要请求删除这项阻止，请转到 Azure 门户中 Azure 虚拟网络资源的“诊断和解决”边栏选项卡的“无法发送电子邮件(SMTP-端口 25)”部分，并创建支持请求 。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作伙伴网络、BizSpark Plus 或 Azure 赞助

对于在以下订阅中部署的 VM，Azure 平台会阻止 TCP 端口 25 上的出站 SMTP 传递尝试：

- Microsoft 合作伙伴网络 (MPN)
- BizSpark Plus
- Azure 赞助

可以删除这项阻止。 若要请求删除这项阻止，请转到 Azure 门户中 Azure 虚拟网络资源的“诊断和解决”边栏选项卡的“无法发送电子邮件(SMTP-端口 25)”部分，并创建支持请求 。

使订阅不受此阻止并停止再重启 VM 后，该订阅中的所有 VM 在此后都会被豁免。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

> [!NOTE]
> Microsoft 保留在确定发生违反服务条款的情况下撤销这些豁免的权利。

## <a name="changing-subscription-type"></a>更改订阅类型

如果将订阅类型从企业协议更改为另一种订阅类型，对部署所做的更改可能导致出站 SMTP 被阻止。 如果你计划将订阅类型从企业协议更改为另一种订阅类型，并需要 TCP 端口 25 上的出站 SMTP，则在更改订阅类型之前，务必联系支持部门将订阅解除锁定。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。 使用以下问题类型：“技术” > “虚拟网络” > “无法发送电子邮件（SMTP/端口 25）”。
